-- 📁 LocalScript (StarterPlayerScripts hoặc executor)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- ⚙️ Biến trạng thái
local spinning = false
local bav = nil

-- 🔘 Toggle bằng phím Y
UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.Y then
		spinning = not spinning

		if spinning then
			print("✅ Flip ON")
			bav = Instance.new("BodyAngularVelocity")
			bav.AngularVelocity = Vector3.new(500, 0, 0) -- Lộn về phía trước cực nhanh
			bav.MaxTorque = Vector3.new(math.huge, 0, 0)
			bav.P = math.huge
			bav.Name = "XFlip"
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
