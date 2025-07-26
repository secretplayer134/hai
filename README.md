-- 📁 LocalScript (StarterPlayerScripts)

local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

local speed = 1000 -- Tốc độ bay xoáy
local radius = 20 -- Bán kính vòng xoáy
local spinning = false
local angle = 0

local function getHRP()
	local char = player.Character
	if char then
		return char:FindFirstChild("HumanoidRootPart")
	end
end

-- 🔄 Xoáy quanh tại chỗ
RunService.Heartbeat:Connect(function(dt)
	if spinning then
		local hrp = getHRP()
		if hrp then
			angle += dt * speed
			local offset = Vector3.new(
				math.cos(angle),
				math.sin(angle * 2), -- tạo chuyển động lên xuống
				math.sin(angle)
			) * radius

			hrp.Velocity = offset * 5 -- Tăng quán tính để bay loạn
			hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + offset)
		end
	end
end)

-- 🧨 Toggle với phím "Y"
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.Y then
		spinning = not spinning
	end
end)
