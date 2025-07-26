-- 👤 LocalPlayer & Character
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local lp = Players.LocalPlayer
local char = lp.Character or lp.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- 🛠️ Làm nhân vật vô hình (trừ HumanoidRootPart)
for _, part in pairs(char:GetDescendants()) do
	if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
		part.Transparency = 1
	elseif part:IsA("Decal") then
		part:Destroy()
	end
end

-- 📌 Tạo lực fling
local bav = Instance.new("BodyAngularVelocity")
bav.AngularVelocity = Vector3.new(0, 1000000, 0)
bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
bav.P = math.huge
bav.Parent = hrp

local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
bv.P = math.huge
bv.Velocity = Vector3.new(0, 0, 0)
bv.Parent = hrp

-- 🔄 Theo người gần nhất
local function getClosestPlayer()
	local closest = nil
	local shortest = math.huge
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= lp and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
			local distance = (plr.Character.HumanoidRootPart.Position - hrp.Position).Magnitude
			if distance < shortest then
				shortest = distance
				closest = plr
			end
		end
	end
	return closest
end

-- 🟢 Toggle fling bằng phím Y
local flingEnabled = false
local flingLoop

UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.KeyCode == Enum.KeyCode.Y then
		flingEnabled = not flingEnabled
		if flingEnabled then
			print("✅ Fling ON")
			flingLoop = RunService.RenderStepped:Connect(function()
				local target = getClosestPlayer()
				if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
					local targetHRP = target.Character.HumanoidRootPart
					-- Di chuyển nhân vật của bạn đến vị trí target
					hrp.CFrame = targetHRP.CFrame
					bv.Velocity = Vector3.new(1e5, 1e5, 1e5)
				end
			end)
		else
			print("❌ Fling OFF")
			if flingLoop then
				flingLoop:Disconnect()
				flingLoop = nil
			end
			bv.Velocity = Vector3.new(0, 0, 0)
		end
	end
end)
