-- ⚠️ NGUY HIỂM: Không chạy nếu không sẵn sàng mất kết nối hoặc lag nặng
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- Thông số
local partsPerSecond = 100000
local totalCycles = 10000000
local size = Vector3.new(10, 10, 10)
local running = true

-- 🛑 Dừng lại nếu nhấn ESC
UserInputService.InputBegan:Connect(function(input, gpe)
	if input.KeyCode == Enum.KeyCode.Escape then
		running = false
	end
end)

-- Hàm tạo khối màu ngẫu nhiên
local function spawnRandomPart(position)
	local part = Instance.new("Part")
	part.Size = size
	part.Anchored = true
	part.Position = position
	part.Color = Color3.new(math.random(), math.random(), math.random()) -- màu ngẫu nhiên
	part.Material = Enum.Material.SmoothPlastic
	part.Parent = workspace
end

-- Tạo cực nhiều khối mỗi giây
coroutine.wrap(function()
	for cycle = 1, totalCycles do
		if not running then break end

		for i = 1, partsPerSecond do
			local offset = Vector3.new(
				math.random(-100, 100),
				math.random(5, 100),
				math.random(-100, 100)
			)
			local pos = hrp.Position + offset
			spawnRandomPart(pos)
		end

		task.wait(1) -- mỗi giây
	end
end)()
