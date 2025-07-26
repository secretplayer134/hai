-- 📁 LocalScript (StarterPlayerScripts hoặc executor)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- 📦 Hàm tạo khối dẹp
local function createFlatBlock()
	local part = Instance.new("Part")
	part.Size = Vector3.new(10, 2, 10)
	part.Anchored = true
	part.Material = Enum.Material.SmoothPlastic
	part.BrickColor = BrickColor.Gray()
	part.Name = "FlatBlock"

	-- Đặt phía trước mặt người chơi
	local forward = hrp.CFrame.LookVector * 10
	local position = hrp.Position + forward
	part.Position = Vector3.new(position.X, hrp.Position.Y - 3, position.Z) -- đặt thấp hơn 3 stud cho vừa tầm

	part.Parent = workspace
end

-- 🎮 Phím L để tạo khối
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.L then
		createFlatBlock()
	end
end)
