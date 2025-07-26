-- 📁 LocalScript (StarterPlayerScripts hoặc executor)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local flingActive = false
local invisClone = nil
local hrp = nil
local bt = nil

-- 🔁 Hàm tạo invis fling character
local function createInvisClone()
	local character = player.Character
	if not character then return end

	hrp = character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	-- 🧱 Clone nhân vật để làm vật thể fling
	invisClone = character:Clone()
	invisClone.Name = "InvisFlingPart"
	invisClone.Parent = workspace

	-- 🧍‍♂️ Gỡ humanoid để clone không bị reset
	local hum = invisClone:FindFirstChildOfClass("Humanoid")
	if hum then hum:Destroy() end

	-- 🪞 Làm clone vô hình
	for _, part in ipairs(invisClone:GetDescendants()) do
		if part:IsA("BasePart") then
			part.Anchored = false
			part.CanCollide = true
			part.Transparency = 1
		end
	end

	local cloneHRP = invisClone:FindFirstChild("HumanoidRootPart")
	if not cloneHRP then return end

	-- ⚡ Tạo lực cực mạnh
	bt = Instance.new("BodyThrust")
	bt.Force = Vector3.new(999999, 999999, 999999)
	bt.Location = cloneHRP.Position
	bt.Parent = cloneHRP

	-- 🔁 Di chuyển clone theo bạn và xoay vòng
	RunService.Heartbeat:Connect(function()
		if flingActive and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local realHRP = player.Character.HumanoidRootPart
			cloneHRP.CFrame = realHRP.CFrame * CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(tick() * 1000), 0)
		end
	end)
end

-- ✅ Bật invis fling
local function enableInvisFling()
	if flingActive then return end
	createInvisClone()
	flingActive = true
end

-- ❌ Tắt invis fling
local function disableInvisFling()
	if invisClone then invisClone:Destroy() invisClone = nil end
	if bt then bt:Destroy() bt = nil end
	flingActive = false
end

-- ⌨️ Nhấn Y để bật/tắt
UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.Y then
		if flingActive then
			disableInvisFling()
		else
			enableInvisFling()
		end
	end
end)

-- 🔁 Reset thì dừng fling
player.CharacterAdded:Connect(function()
	wait(1)
	disableInvisFling()
end)
