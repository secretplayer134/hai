-- 📁 LocalScript trong StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- 🏁 Theo dõi lại khi nhân vật respawn
player.CharacterAdded:Connect(function(newChar)
	char = newChar
	hrp = char:WaitForChild("HumanoidRootPart")
end)

local isChasing = false
local target = nil
local speed = 200
local offsetDirection = 1

-- 🔁 Tìm người chơi gần nhất
local function getNearestPlayer()
	local closest = nil
	local shortestDist = math.huge

	for _, other in pairs(Players:GetPlayers()) do
		if other ~= player and other.Character and other.Character:FindFirstChild("HumanoidRootPart") then
			local dist = (other.Character.HumanoidRootPart.Position - hrp.Position).Magnitude
			if dist < shortestDist then
				shortestDist = dist
				closest = other
			end
		end
	end

	return closest
end

-- ⌨️ Bật/tắt khi nhấn K
UIS.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.K then
		isChasing = not isChasing
		if isChasing then
			target = getNearestPlayer()
		else
			target = nil
		end
	end
end)

-- 🚀 Di chuyển tới lui nhanh liên tục khi theo sát
RunService.Heartbeat:Connect(function(dt)
	if isChasing and target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
		local targetPos = target.Character.HumanoidRootPart.Position
		local oscillation = math.sin(tick() * 10) * 10 -- lắc mạnh trái phải

		local direction = (targetPos - hrp.Position).Unit
		local moveDirection = direction + Vector3.new(oscillation, math.sin(tick() * 20) * 10, 0
