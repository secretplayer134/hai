-- Respawn ngay lập tức khi nhấn J
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- Bỏ delay respawn (nếu có)
pcall(function()
    Players.CharacterAutoLoads = false
end)

-- Hàm respawn
local function instantRespawn()
    player:LoadCharacter()
end

-- Khi nhấn phím J
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.J then
        instantRespawn()
    end
end)
