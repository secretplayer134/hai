-- 📁 LocalScript (StarterPlayerScripts)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

local flyingInCircle = false
local angle = 0
local radius = 10 -- bán kính vòng tròn
local speed = 100 -- tốc độ quay (cực nhanh)
local heightOffset = 10 -- độ cao khi quay vòng

-- 📌 Gốc toạ độ trung tâm (gốc quay)
local centerPosition = hrp.Position

-- 🎮 Bật/tắt bằng phím Y
UserInputService.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.Y then
		flyingInCircle = not flyingInCircle
		if flyingInCircle then
			centerPosition = hrp.Position
		end
	end
end)

-- 🌀 Vòng lặp bay theo hình tròn
RunService.RenderStepped:Connect(function(dt)
	if flyingInCircle then
		angle += dt * speed
		local x = math.cos(angle) * radius
		local z = math.sin(angle) * radius
		local y = heightOffset
		hrp.Velocity = Vector3.new(0, 0, 0) -- reset velocity
		hrp.CFrame = CFrame.new(centerPosition + Vector3.new(x, y, z), centerPosition)
	end
end)
