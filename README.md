-- 📁 LocalScript (StarterPlayerScripts hoặc Executor)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local spinning = false
local bav = nil
local hrp = nil

-- 🧍 Theo dõi nhân vật
local function setupCharacter(char)
	hrp = char:WaitForChild("HumanoidRootPart")
end

if player.Character then
	setupCharacter(player.Character)
end

player.CharacterAdded:Connect(setupCharacter)

-- 🎮 Bật/tắt lộn bằng phím Y
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.Y and hrp then
		spinning = not spinning

		if spinning then
			print("✅ Flip ON")
			bav = Instance.new("BodyAngularVelocity")
			bav.Name = "XFlip"
			bav.AngularVelocity = Vector3.new(500, 0, 0) -- Xoay cực nhanh quanh trục X
			bav.MaxTorque = Vector3.new(math.huge, 0, 0)
			bav.P = math.huge
			bav.Parent = hrp
		else
			print("❌ Flip OFF")
			if bav then
				bav:Destroy()
				bav = nil
			end
		end
	end
end)
