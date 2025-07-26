-- 📁 LocalScript (StarterPlayerScripts hoặc executor)

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- 📦 Hàm tạo cầu thang
local function createStair()
	local startPos = hrp.Position + hrp.CFrame.LookVector * 10 -- cách người chơi 10 studs phía trước

	for i = 0, 4 do -- 5 bậc
		local step = Instance.new("Part")
		step.Size = Vector3.new(5, 1, 5)
		step.Anchored = true
		step.Position = startPos + Vector3.new(0, i, i * 5)
		step.Material = Enum.Material.SmoothPlastic
		step.BrickColor = BrickColor.new("Really red")
		step.Name = "StairStep"
		step.Parent = workspace
	end
end

-- 🎮 Phím nhấn L để tạo cầu thang
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.L then
		createStair()
	end
end)
