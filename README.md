-- 📁 LocalScript (StarterPlayerScripts)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- 🔁 Gán lại khi respawn
player.CharacterAdded:Connect(function(char)
	character = char
	hrp = char:WaitForChild("HumanoidRootPart")
end)

-- ⚙️ Cài đặt
local radius = 6 -- Phạm vi phát hiện gần bạn
local forceMagnitude = 5000 -- Độ mạnh của cú đẩy

-- 🔁 Kiểm tra liên tục
RunService.Heartbeat:Connect(function()
	for _, otherPlayer in ipairs(Players:GetPlayers()) do
		if otherPlayer ~= player and otherPlayer.Character then
			local otherHRP = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
			if otherHRP and (hrp.Position - otherHRP.Position).Magnitude <= radius then
				-- 🔀 Tạo lực đẩy ngẫu nhiên cực mạnh
				local randomDirection = Vector3.new(
					math.random(-100, 100),
					math.random(50, 150),  -- đảm bảo có độ cao
					math.random(-100, 100)
			 ).Unit * forceMagnitude
				
				otherHRP.Velocity = randomDirection
			end
		end
	end
end)
