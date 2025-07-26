-- 📁 LocalScript (StarterPlayerScripts hoặc Executor)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local flingActive = false
local hrp = nil
local bt, bg = nil, nil

-- 🌀 Bật fling
local function enableFling()
	if not player.Character then return end
	hrp = player.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	-- Gỡ cũ nếu có
	if hrp:FindFirstChild("BodyThrust") then hrp.BodyThrust:Destroy() end
	if hrp:FindFirstChild("BodyGyro") then hrp.BodyGyro:Destroy() end

	-- BodyThrust: tạo lực đẩy cực mạnh
	bt = Instance.new("BodyThrust")
	bt.Force = Vector3.new(999999, 999999, 999999)
	bt.Location = hrp.Position
	bt.Parent = hrp

	-- BodyGyro: quay nhân vật cực nhanh
	bg = Instance.new("BodyGyro")
	bg.D = 0
	bg.P = 30000
	bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
	bg.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(50000), 0)
	bg.Parent = hrp

	flingActive = true
end

-- ❌ Tắt fling
local function disableFling()
	if bt then bt:Destroy() bt = nil end
	if bg then bg:Destroy() bg = nil end
	flingActive = false
end

-- ⌨️ Nhấn Y để bật/tắt fling
UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.Y then
		if flingActive then
			disableFling()
		else
			enableFling()
		end
	end
end)

-- 🔁 Khi respawn
player.CharacterAdded:Connect(function(char)
	wait(1)
	disableFling()
end)
