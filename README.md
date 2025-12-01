local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local flying = false
local flySpeed = 100
local followDistance = 2
local following = false
local currentTarget = nil
local noclipEnabled = false
local vToggled = false

-- Pause system
local scriptPaused = false
local savedState = { flying = false, following = false, noclip = false }

local function pauseScript()
	savedState.flying = flying
	savedState.following = following
	savedState.noclip = noclipEnabled

	stopFlying()
	following = false
	currentTarget = nil
	setNoclip(false)

	scriptPaused = true
end

local function resumeScript()
	if savedState.flying then startFlying() end
	if savedState.following then
		local target = getClosestPlayerInSight()
		if target then
			following = true
			currentTarget = target
		end
	end
	if savedState.noclip then setNoclip(true) end

	scriptPaused = false
end

player.CameraMaxZoomDistance = 1e9
player.CameraMinZoomDistance = 0.5

local teleportLocations = {
	V1 = Vector3.new(10000, 0, 0),
	V2 = Vector3.new(100, 442, -10)
}

-- Fly
local bg, bv, flyConn
local function createFlyParts(hrp)
	bg = Instance.new("BodyGyro")
	bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
	bg.P = 10000
	bg.CFrame = hrp.CFrame
	bg.Parent = hrp

	bv = Instance.new("BodyVelocity")
	bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
	bv.Velocity = Vector3.new(0, 0, 0)
	bv.Parent = hrp
end

function startFlying()
	if flying then return end
	flying = true
	local character = player.Character or player.CharacterAdded:Wait()
	local hrp = character:WaitForChild("HumanoidRootPart")
	createFlyParts(hrp)
	flyConn = RunService.RenderStepped:Connect(function()
		if not flying then return end
		local cam = workspace.CurrentCamera
		bg.CFrame = cam.CFrame
		bv.Velocity = cam.CFrame.LookVector * flySpeed
	end)
end

function stopFlying()
	flying = false
	if flyConn then flyConn:Disconnect() end
	if bg then bg:Destroy() end
	if bv then bv:Destroy() end
end

-- Follow
function getClosestPlayerInSight()
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
	if following and currentTarget and currentTarget.Character and currentTarget.Character:FindFirstChild("HumanoidRootPart") then
		local targetHRP = currentTarget.Character.HumanoidRootPart
		local myChar = player.Character or player.CharacterAdded:Wait()
		local myHRP = myChar:FindFirstChild("HumanoidRootPart")
		if myHRP then
			local offset = -targetHRP.CFrame.LookVector * followDistance
			local newPos = targetHRP.Position + offset
			myHRP.CFrame = CFrame.new(newPos, targetHRP.Position)
		end
	end
end)

player.CharacterAdded:Connect(function()
	if flying then
		task.wait(1)
		startFlying()
	end
end)

-- Teleport
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
		char.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.p + Vector3.new(0, 3, 0))
	end
end

-- Noclip
local noclipConn
function setNoclip(state)
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
local guiFrame, infoLabels
local function updateInfo()
	infoLabels[1].Text = "Fly speed: " .. tostring(flySpeed)
	infoLabels[2].Text = "Noclip: " .. (noclipEnabled and "on" or "off")
	infoLabels[3].Text = "Follow distance: " .. tostring(followDistance)
	infoLabels[4].Text = "E to follow"
	infoLabels[5].Text = "R to fly"
	infoLabels[6].Text = "T to turn on/off noclip"
	infoLabels[7].Text = "Y to teleport to mouse"
	infoLabels[8].Text = "V tp to void / ground (TSB)"
	infoLabels[9].Text = "RightShift show/hide menu"
	infoLabels[10].Text = "Pause: " .. (scriptPaused and "ON (`)" or "OFF (`)")
end

local function makeDraggable(frame)
	local dragging, dragInput, dragStart, startPos

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	frame.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			dragInput = input
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
end

local function createGUI()
	local gui = Instance.new("ScreenGui")
	gui.Name = "FlyMenuGui"
	gui.ResetOnSpawn = false
	gui.Parent = player:WaitForChild("PlayerGui")

	guiFrame = Instance.new("Frame")
	guiFrame.Size = UDim2.new(0, 300, 0, 460)
	guiFrame.Position = UDim2.new(1, -300, 0, 200)
	guiFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	guiFrame.BorderSizePixel = 0
	guiFrame.Parent = gui

	makeDraggable(guiFrame)

	infoLabels = {}
	for i = 1, 10 do
		local lbl = Instance.new("TextLabel")
		lbl.Size = UDim2.new(1, -10, 0, 25)
		lbl.Position = UDim2.new(0, 5, 0, 5 + (i - 1) * 30)
		lbl.BackgroundTransparency = 1
		lbl.TextColor3 = Color3.new(1,1,1)
		lbl.Font = Enum.Font.Gotham
		lbl.TextSize = 14
		lbl.TextXAlignment = Enum.TextXAlignment.Left
		lbl.Parent = guiFrame
		infoLabels[i] = lbl
	end

	local function createButton(name, text, yPos)
		local btn = Instance.new("TextButton")
		btn.Name = name
		btn.Size = UDim2.new(1, -10, 0, 35)
		btn.Position = UDim2.new(0, 5, 0, yPos)
		btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
		btn.TextColor3 = Color3.new(1,1,1)
		btn.Font = Enum.Font.GothamBold
		btn.TextSize = 14
		btn.Text = text
		btn.Parent = guiFrame
		return btn
	end

	local increaseBtn = createButton("IncreaseSpeedBtn","faster", 330)
	local decreaseBtn = createButton("DecreaseSpeedBtn","slower", 370)
	local adjustFollowBtn = createButton("AdjustFollowBtn","adjust distance (max:10)", 410)

	increaseBtn.MouseButton1Click:Connect(function()
		flySpeed += 50
		updateInfo()
	end)
	decreaseBtn.MouseButton1Click:Connect(function()
		flySpeed = math.max(0, flySpeed - 50)
		updateInfo()
	end)
	adjustFollowBtn.MouseButton1Click:Connect(function()
		followDistance += 1
		if followDistance > 10 then followDistance = 1 end
		updateInfo()
	end)

	UserInputService.InputBegan:Connect(function(input)
		if input.KeyCode == Enum.KeyCode.RightShift then
			guiFrame.Visible = not guiFrame.Visible
			updateInfo()
		end
	end)

	updateInfo()
end

createGUI()

-- Input
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end

	-- Toggle Pause bằng dấu `
	if input.KeyCode == Enum.KeyCode.Backquote then
		if not scriptPaused then pauseScript() else resumeScript() end
		updateInfo()
		return
	end

	if scriptPaused then return end

	if input.KeyCode == Enum.KeyCode.R then
		if flying then stopFlying() else startFlying() end
	elseif input.KeyCode == Enum.KeyCode.V then
		teleportCharacter("V")
	elseif input.KeyCode == Enum.KeyCode.Y then
		teleportToMouse()
	elseif input.KeyCode == Enum.KeyCode.E then
		if not following then
			currentTarget = getClosestPlayerInSight()
			if currentTarget then following = true end
		else
			following = false
			currentTarget = nil
		end
	elseif input.KeyCode == Enum.KeyCode.T then
		setNoclip(not noclipEnabled)
	end

	updateInfo()
end)
