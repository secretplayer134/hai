repeat task.wait() until game.Players.LocalPlayer and game.Players.LocalPlayer:FindFirstChild("NRPBS")

local player = game.Players.LocalPlayer
local health = player.NRPBS:WaitForChild("Health")

health:GetPropertyChangedSignal("Value"):Connect(function()
    if health.Value <= 0 then
        -- 🔥 Bỏ qua 5s chờ bằng cách gọi Remote ngay lập tức
        game.ReplicatedStorage.LoadCharacter:FireServer()
    end
end)
