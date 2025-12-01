-- SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- VARIABLES
local flying = false
local flySpeed = 100
local followDistance = 2
local following = false
local currentTarget = nil
local noclipEnabled = false
local vToggled = false
local paused = false
local holdStates = {}

-- CAMERA
player.CameraMaxZoomDistance = 1e9
player.CameraMinZoomDistance = 0.5

-- TELEPORT LOCATIONS
local teleportLocations = {
	V1 = Vector3.new(10000,0,0),
	V2 = Vector3.new(100,442,-10)
}

-- FLY
local bg, bv, flyConn
local function createFlyParts(hrp)
	bg = Instance.new("BodyGyro")
	bg.MaxTorque = Vector3.new(9e9,9e9,9e9)
	bg.P = 10000
	bg.CFrame = hrp.CFrame
	bg.Parent = hrp

	bv = Instance.new("BodyVelocity")
	bv.MaxForce = Vector3.new(9e9,9e9,9e9)
	bv.Velocity = Vector3.new(0,0,0)
	bv.Parent = hrp
end

local function startFlying()
	if flying then return end
	flying = true
	local character = player.Character or player.CharacterAdded:Wait()
	local hrp = character:WaitForChild("HumanoidRootPart")
	createFlyParts(hrp)
	flyConn = RunService.RenderStepped:Connect(function()
		if not flying or paused then return end
		local cam = workspace.CurrentCamera
		bg.CFrame = cam.CFrame
		bv.Velocity = cam.CFrame.LookVector * flySpeed
	end)
end

local function stopFlying()
	flying = false
	if flyConn then flyConn:Disconnect() end
	if bg then bg:Destroy() end
	if bv then bv:Destroy() end
end

-- FOLLOW
local function getClosestPlayerInSight()
	local camera = workspace.CurrentCamera
	local closestPlayer = nil
	local smallestAngle = math.huge
	for _, otherPlayer in ipairs(Players:GetPlayers()) do
		if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("HumanoidRootPart") then
			local hrp = otherPlayer.Character.HumanoidRootPart
			local dirToPlayer = (hrp.Position - camera.CFrame.Position).Unit
			local angle = math.acos(camera.CFrame.LookVector:Dot(dirToPlayer))
			if angle < math.rad(30) and angle < smallestAngle then
				smallestAngle = angle
				closestPlayer = otherPlayer
			end
		end
	end
	return closestPlayer
end

RunService.RenderStepped:Connect(function()
	if paused then return end
	if following and currentTarget and currentTarget.Character and currentTarget.Character:FindFirstChild("HumanoidRootPart") then
		local targetHRP = currentTarget.Character.HumanoidRootPart
		local myChar = player.Character or player.CharacterAdded:Wait()
		local myHRP = myChar:FindFirstChild("HumanoidRootPart")
		if myHRP then
			local offset = -targetHRP.CFrame.LookVector * followDistance
			myHRP.CFrame = CFrame.new(targetHRP.Position + offset, targetHRP.Position)
		end
	end
end)

player.CharacterAdded:Connect(function()
	if flying then
		task.wait(1)
		startFlying()
	end
end)

-- TELEPORT
local function teleportCharacter(key)
	local character = player.Character or player.CharacterAdded:Wait()
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if hrp then
		if key == "V" then
			if not vToggled then
				hrp.CFrame = CFrame.new(teleportLocations.V1)
				vToggled = true
			else
				hrp.CFrame = CFrame.new(teleportLocations.V2)
				vToggled = false
			end
		end
	end
end

local function teleportToMouse()
	local char = player.Character
	if char and char:FindFirstChild("HumanoidRootPart") and mouse.Hit then
		char.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.p + Vector3.new(0,3,0))
	end
end

-- NOCLIP
local noclipConn
local function setNoclip(state)
	noclipEnabled = state
	if noclipConn then noclipConn:Disconnect() end
	if state then
		noclipConn = RunService.Stepped:Connect(function()
			local character = player.Character
			if character then
				for _, part in pairs(character:GetDescendants()) do
					if part:IsA("BasePart") then
						part.CanCollide = false
					end
				end
			end
		end)
	end
end

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "FlyMenuGui"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local guiFrame = Instance.new("Frame")
guiFrame.Size = UDim2.new(0,300,0,420)
guiFrame.Position = UDim2.new(1,-300,0,242)
guiFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
guiFrame.BorderSizePixel = 0
guiFrame.Visible = true
guiFrame.Parent = gui

-- DRAG
do
	local dragging, dragInput, dragStart, startPos
	guiFrame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = guiFrame.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)
	guiFrame.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			dragInput = input
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			guiFrame.Position = UDim2.new(startPos.X.Scale,startPos.X.Offset+delta.X,
				startPos.Y.Scale,startPos.Y.Offset+delta.Y)
		end
	end)
end

-- LABELS
local infoLabels = {}
for i = 1,9 do
	local lbl = Instance.new("TextLabel")
	lbl.Size = UDim2.new(1,-10,0,25)
	lbl.Position = UDim2.new(0,5,0,5+(i-1)*30)
	lbl.BackgroundTransparency = 1
	lbl.TextColor3 = Color3.new(1,1,1)
	lbl.Font = Enum.Font.Gotham
	lbl.TextSize = 14
	lbl.TextXAlignment = Enum.TextXAlignment.Left
	lbl.Parent = guiFrame
	infoLabels[i] = lbl
end

local function updateInfo()
	infoLabels[1].Text = "Fly speed: "..tostring(flySpeed)
	infoLabels[2].Text = "Noclip: "..(noclipEnabled and "on" or "off")
	infoLabels[3].Text = "Follow distance: "..tostring(followDistance)
	infoLabels[4].Text = "E to follow"
	infoLabels[5].Text = "R to fly"
	infoLabels[6].Text = "T to turn on/off noclip"
	infoLabels[7].Text = "Y to teleport"
	infoLabels[8].Text = "V tp to void, V again tp to ground (For TSB)"
	infoLabels[9].Text = "Rightshift to turn on/off menu"
end

-- BUTTONS
local function createButton(name,text,y)
	local btn = Instance.new("TextButton")
	btn.Name = name
	btn.Size = UDim2.new(1,-10,0,35)
	btn.Position = UDim2.new(0,5,0,y)
	btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 14
	btn.Text = text
	btn.Parent = guiFrame
	return btn
end

local increaseBtn = createButton("IncreaseSpeedBtn","faster",300)
local decreaseBtn = createButton("DecreaseSpeedBtn","slower",340)
local adjustFollowBtn = createButton("AdjustFollowBtn","adjust distance (max:10)",380)

increaseBtn.MouseButton1Click:Connect(function() flySpeed+=50 updateInfo() end)
decreaseBtn.MouseButton1Click:Connect(function() flySpeed=math.max(0,flySpeed-50) updateInfo() end)
adjustFollowBtn.MouseButton1Click:Connect(function() followDistance+=1 if followDistance>10 then followDistance=1 end updateInfo() end)

UserInputService.InputBegan:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.RightShift then
		guiFrame.Visible = not guiFrame.Visible
		updateInfo()
	end
end)

-- CHECKBOX CHỮ HOA
for _, obj in pairs(guiFrame:GetDescendants()) do
	if (obj:IsA("TextLabel") or obj:IsA("TextButton")) and obj.Text:match("%u") then
		local checkbox = Instance.new("TextButton")
		checkbox.Size = UDim2.new(0,20,0,20)
		checkbox.Position = UDim2.new(1,-25,.5,-10)
		checkbox.Text = "✔"
		checkbox.Parent = obj
		holdStates[checkbox] = true
		checkbox.MouseButton1Click:Connect(function()
			holdStates[checkbox] = not holdStates[checkbox]
			checkbox.Text = holdStates[checkbox] and "✔" or ""
		end)
	end
end

updateInfo()

-- INPUT
UserInputService.InputBegan:Connect(function(input,gameProcessed)
	if gameProcessed then return end
	if paused then return end

	if input.KeyCode==Enum.KeyCode.R then
		for box,state in pairs(holdStates) do
			if not state then stopFlying() return end
		end
		if flying then stopFlying() else startFlying() end
	elseif input.KeyCode==Enum.KeyCode.V then
		teleportCharacter("V")
	elseif input.KeyCode==Enum.KeyCode.Y then
		teleportToMouse()
	elseif input.KeyCode==Enum.KeyCode.E then
		if not following then
			currentTarget=getClosestPlayerInSight()
			if currentTarget then following=true end
		else
			following=false
			currentTarget=nil
		end
	elseif input.KeyCode==Enum.KeyCode.T then
		setNoclip(not noclipEnabled)
	end
	updateInfo()
end)

-- PHÍM ` TẠM DỪNG
UserInputService.InputBegan:Connect(function(input)
	if input.KeyCode==Enum.KeyCode.Backquote then
		paused = not paused
		if paused then stopFlying() print("⛔ Script tạm dừng (ấn ` để chạy lại)") 
		else print("▶ Script chạy lại") end
	end
end)
