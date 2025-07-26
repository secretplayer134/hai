-- 📁 LocalScript trong StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

local enabled = false
local spinSpeed = 100000 -- tốc độ xoay (độ mỗi giây)
local moveSpeed = 10000000 -- tốc độ di chuyển

-- 🔁 Đảm bảo nhân vật sẵn sàng
player.CharacterAdded:Connect(function(char)
	character = char
	humanoid = char:WaitForChild("Humanoid")
	hrp = char:WaitForChild("HumanoidRootPart")
end)

-- 🎮 Nhấn Y để bật/tắt
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.Y then
		enabled = not enabled
		if enabled then
			humanoid.WalkSpeed = moveSpeed
			humanoid.AutoRotate = false
		else
			humanoid.WalkSpeed = 16
			humanoid.AutoRotate = true
		end
	end
end)

-- 🔁 Xoay vòng liên tục nếu bật
RunService.RenderStepped:Connect(function(dt)
	if enabled and hrp then
		local rotation = CFrame.Angles(0, math.rad(spinSpeed * dt), 0)
		hrp.CFrame = hrp.CFrame * rotation
	end
end)
