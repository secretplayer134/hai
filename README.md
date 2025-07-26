-- 🔍 Dò toàn bộ các RemoteEvent được gọi từ client
local mt = getrawmetatable(game)
setreadonly(mt, false)

local old = mt.__namecall

mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    if (method == "FireServer" or method == "InvokeServer") and typeof(self) == "Instance" and self:IsA("RemoteEvent") then
        print("📡 Remote Called: " .. self:GetFullName())
        print("📦 Args:", ...)
    end
    return old(self, ...)
end)
