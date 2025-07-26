local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local lp = Players.LocalPlayer
local char = lp.Character or lp.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

local flingPart = nil
local flingOn = false

-- 🧱 Tạo Part fling
local function createFlingPart()
	local part = Instance.new("Part")
	part.Size = Vector3.new(5, 1, 5)
	part.Anchored = false
	part.CanCollide = true
	part.Transparency = 1 -- Vô hình
	part.Massless = true
	part.Name = "FlingPart"
	part.Position = hrp.Position
	part.Parent = workspace

	-- Gắn part vào HRP
	local weld = Instance.new("WeldConstraint")
	weld.Part0 = hrp
	weld.Part1 = part
	weld.Parent = part

	-- Tạo vòng quay
	local bav = Instance.new("BodyAngularVelocity")
	bav.AngularVelocity = Vector3.new(0, 999999, 0)
	bav.MaxTorque = Vector3.new(0, math.huge, 0)
	bav.P = math.huge
	bav.Parent = part

	return part
end

-- 🔘 Toggle fling với phím Y
UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.Y then
		flingOn = not flingOn
		if flingOn then
			print("✅ Fling ON")
			flingPart = createFlingPart()
		else
			print("❌ Fling OFF")
			if flingPart then
				flingPart:Destroy()
				flingPart = nil
			end
		end
	end
end)

-- 🔁 Gắn lại nếu respawn
lp.CharacterAdded:Connect(function(newChar)
	char = newChar
	hrp = char:WaitForChild("HumanoidRootPart")
end)
