repeat task.wait() until game.Players.LocalPlayer and game.Players.LocalPlayer:FindFirstChild("NRPBS")

local player = game.Players.LocalPlayer
local health = player.NRPBS:WaitForChild("Health")
local UIS = game:GetService("UserInputService")

-- 🔁 Theo dõi khi bị chết để respawn ngay lập tức
health:GetPropertyChangedSignal("Value"):Connect(function()
    if health.Value <= 0 then
        task.wait(0.1)
        local loadChar = game.ReplicatedStorage:FindFirstChild("Events") and game.ReplicatedStorage.Events:FindFirstChild("LoadCharacter")
        if loadChar then
            loadChar:FireServer()
        else
            warn("⚠️ Không tìm thấy LoadCharacter trong ReplicatedStorage.Events")
        end
    end
end)

-- 🎯 Nhấn Y để tự kill bản thân
UIS.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.Y then
        local char = player.Character
        if char and char:FindFirstChild("Humanoid") then
            char.Humanoid.Health = 0
        end
    end
end)
