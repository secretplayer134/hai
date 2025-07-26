-- ⚠️ XÓA TOÀN BỘ SCRIPT VÀ GUI BẠN ĐÃ EXECUTE ⚠️
-- Sử dụng trên executor như Xeno

for _, obj in ipairs(game:GetDescendants()) do
    if obj:IsA("LocalScript") or obj:IsA("ModuleScript") or obj:IsA("Script") then
        local success, result = pcall(function()
            if not obj:IsDescendantOf(game:GetService("StarterPlayer"))
            and not obj:IsDescendantOf(game:GetService("ReplicatedStorage"))
            and not obj:IsDescendantOf(game:GetService("StarterGui"))
            and not obj:IsDescendantOf(game:GetService("Players"))
            and not obj:IsDescendantOf(game:GetService("Workspace")) then
                obj:Destroy()
            end
        end)
    end
end

-- 🧼 Xóa GUI trong PlayerGui (nếu bạn có tạo GUI riêng)
for _, gui in ipairs(game.Players.LocalPlayer:WaitForChild("PlayerGui"):GetChildren()) do
    pcall(function()
        if not gui:IsA("PlayerScript") and not gui:IsA("StarterGui") then
            gui:Destroy()
        end
    end)
end

-- 🛠️ Xóa tất cả Tool đang cầm (nếu dùng script fly/follow v.v.)
for _, tool in ipairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
    pcall(function()
        tool:Destroy()
    end)
end

-- 💀 Nếu script vẫn bị loop, bạn cần rejoin game để clear hoàn toàn
