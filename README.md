-- 📁 Dán vào Executor rồi Execute

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")

-- Giao diện đơn giản
local ScreenGui = Instance.new("ScreenGui", CoreGui)
ScreenGui.Name = "RemoteSpammer"

local Frame = Instance.new("Frame", ScreenGui)
Frame.Position = UDim2.new(0.05, 0, 0.3, 0)
Frame.Size = UDim2.new(0, 300, 0, 300)
Frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Frame.BorderSizePixel = 0

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "🔧 Remote Spammer"
Title.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18

local DropDown = Instance.new("TextBox", Frame)
DropDown.PlaceholderText = "🔎 Nhập tên Remote hoặc để trống spam hết"
DropDown.Size = UDim2.new(1, -20, 0, 30)
DropDown.Position = UDim2.new(0, 10, 0, 40)
DropDown.Text = ""
DropDown.TextColor3 = Color3.new(1,1,1)
DropDown.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local SpeedBox = Instance.new("TextBox", Frame)
SpeedBox.PlaceholderText = "⏱️ Tốc độ (VD: 1000)"
SpeedBox.Size = UDim2.new(1, -20, 0, 30)
SpeedBox.Position = UDim2.new(0, 10, 0, 80)
SpeedBox.Text = "1000"
SpeedBox.TextColor3 = Color3.new(1,1,1)
SpeedBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local ToggleButton = Instance.new("TextButton", Frame)
ToggleButton.Text = "▶️ BẮT ĐẦU"
ToggleButton.Size = UDim2.new(1, -20, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 0, 130)
ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 127)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 20

local Status = Instance.new("TextLabel", Frame)
Status.Position = UDim2.new(0, 10, 0, 180)
Status.Size = UDim2.new(1, -20, 0, 100)
Status.TextWrapped = true
Status.TextYAlignment = Enum.TextYAlignment.Top
Status.BackgroundTransparency = 1
Status.TextColor3 = Color3.fromRGB(200, 200, 200)
Status.Text = "🕵️ Đang chờ bạn nhấn BẮT ĐẦU"
Status.Font = Enum.Font.SourceSans
Status.TextSize = 16

-- Tìm remote
local allRemotes = {}
for _, v in pairs(ReplicatedStorage:GetDescendants()) do
    if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
        table.insert(allRemotes, v)
    end
end

local running = false

ToggleButton.MouseButton1Click:Connect(function()
    running = not running
    ToggleButton.Text = running and "⏸️ DỪNG" or "▶️ BẮT ĐẦU"
    ToggleButton.BackgroundColor3 = running and Color3.fromRGB(170, 0, 0) or Color3.fromRGB(0, 170, 127)
    if running then
        task.spawn(function()
            local targetName = DropDown.Text
            local speed = tonumber(SpeedBox.Text) or 1000
            Status.Text = "🚀 Đang spam " .. speed .. " lần/giây..."
            while running do
                for _, remote in pairs(allRemotes) do
                    if targetName == "" or remote.Name:lower():find(targetName:lower()) then
                        for i = 1, speed do
                            task.spawn(function()
                                pcall(function()
                                    if remote:IsA("RemoteEvent") then
                                        remote:FireServer("spam", math.random(), true, nil)
                                    elseif remote:IsA("RemoteFunction") then
                                        remote:InvokeServer("spam", math.random(), false)
                                    end
                                end)
                            end)
                        end
                    end
                end
                wait(1)
            end
            Status.Text = "⏸️ Đã dừng spam"
        end)
    end
end)
