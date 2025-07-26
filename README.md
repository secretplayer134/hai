-- 🧼 Script chỉ xóa những gì bạn đã inject qua executor
-- ⚠️ KHÔNG động vào GUI/systems mặc định của game

local lp = game.Players.LocalPlayer
local whitelistServices = {
    ["StarterPlayer"] = true,
    ["StarterGui"] = true,
    ["ReplicatedStorage"] = true,
    ["ReplicatedFirst"] = true,
    ["Workspace"] = true,
    ["Lighting"] = true,
    ["Players"] = true,
    ["SoundService"] = true
}

-- 🔍 Xóa script ngoài whitelist
for _, v in ipairs(game:GetDescendants()) do
    if (v:IsA("LocalScript") or v:IsA("ModuleScript") or v:IsA("Script")) then
        local service = v:FindFirstAncestorWhichIsA("Service")
        if service and not whitelistServices[service.Name] then
            pcall(function()
                v:Destroy()
            end)
        end
    end
end

-- 🧻 Xóa GUI không phải mặc định trong PlayerGui
for _, gui in ipairs(lp:WaitForChild("PlayerGui"):GetChildren()) do
    if not gui:IsA("PlayerScript") and not gui.Name:match("^%a+Gui$") then
        pcall(function()
            gui:Destroy()
        end)
    end
end

-- 🧺 Xóa Tool trong Backpack (nếu dùng script cho tool)
for _, tool in ipairs(lp:FindFirstChild("Backpack"):GetChildren()) do
    if tool:IsA("Tool") and not tool:FindFirstChildOfClass("Script") then
        pcall(function()
            tool:Destroy()
        end)
    end
end
