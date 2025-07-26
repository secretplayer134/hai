-- 📁 LocalScript trong StarterPlayerScripts

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local flying = false
local flySpeed = 50
local moveVector = Vector3.zero
local direction = {
	W = false,
	A = false,
	S = false,
	D = false
}

-- 🛫 Hàm cập nhật hướng bay
local function updateDirection()
	moveVector = Vector3.zero
	if direction.W then moveVector = moveVector + (workspace.CurrentCamera.CFrame.LookVector) end
	if direction.S then moveVector = moveVector - (workspace.CurrentCamera.CFrame.LookVector) end
	if direction.A then moveVector = moveVector - (workspace.CurrentCamera.CFrame.RightVector) end
	if direction.D then moveVector = moveVector + (workspace.CurrentCamera.CFrame.RightVector) end
	moveVector = moveVector.Unit * flySpeed
end

-- 🎮 Bắt phím
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end

	if input.KeyCode == Enum.KeyCode.R then
		flying = not flying
		if flying then
			humanoidRootPart.Anchored = false
		end
	end

	if input.KeyCode == Enum.KeyCode.W then direction.W = true end
	if input.KeyCode == Enum.KeyCode.A then direction.A = true end
	if input.KeyCode == Enum.KeyCode.S then direction.S = true end
	if input.KeyCode == Enum.KeyCode.D then direction.D = true end
	updateDirection()
end)

UserInputService.InputEnded:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.W then direction.W = false end
	if input.KeyCode == Enum.KeyCode.A then direction.A = false end
	if input.KeyCode == Enum.KeyCode.S then direction.S = false end
	if input.KeyCode == Enum.KeyCode.D then direction.D = false end
	updateDirection()
end)

-- 🌀 Luôn cập nhật khi bay
RunService.RenderStepped:Connect(function()
	if flying then
		if moveVector.Magnitude > 0 then
			humanoidRootPart.Velocity = moveVector
		else
			humanoidRootPart.Velocity = Vector3.zero
		end
	end
end)
