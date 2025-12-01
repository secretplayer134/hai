local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local flying = false
local flySpeed = 100
local followDistance = 2
local following = false
local currentTarget = nil
local noclipEnabled = false
local vToggled = false

player.CameraMaxZoomDistance = 1e9
player.CameraMinZoomDistance = 0.5

local teleportLocations = {
    V1 = Vector3.new(10000, 0, 0),
    V2 = Vector3.new(100, 442, -10)
}

local bg, bv, flyConn
local function createFlyParts(hrp)
    bg = Instance.new("BodyGyro")
    bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.P = 10000
    bg.CFrame = hrp.CFrame
    bg.Parent = hrp

    bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    bv.Velocity = Vector3.new(0, 0, 0)
    bv.Parent = hrp
end

local function startFlying()
    if flying then return end
    flying = true
    local character = player.Character or player.CharacterAdded:Wait()
    local hrp = character:WaitForChild("HumanoidRootPart")
    createFlyParts(hrp)
    flyConn = RunService.RenderStepped:Connect(function()
        if not flying then return end
        local cam = workspace.CurrentCamera
        bg.CFrame = cam.CFrame
        bv.Velocity = cam.CFrame.LookVector * flySpeed
    end)
end

local function stopFlying()
    flying = false
    if flyConn then flyConn:Disconnect() end
    if bg then bg:Destroy() end
    if bv then bv:Destroy() end
end

local function getClosestPlayerInSight()
    local camera = workspace.CurrentCamera
    local closestPlayer, smallestAngle = nil, math.huge
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = p.Character.HumanoidRootPart
            local angle = math.acos(camera.CFrame.LookVector:Dot((hrp.Position - camera.CFrame.Position).Unit))
            if angle < math.rad(30) and angle < smallestAngle then
                smallestAngle = angle
                closestPlayer = p
            end
        end
    end
    return closestPlayer
end

RunService.RenderStepped:Connect(function()
    if following and currentTarget and currentTarget.Character then
        local hrp = currentTarget.Character:FindFirstChild("HumanoidRootPart")
        local myHRP = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if hrp and myHRP then
            myHRP.CFrame = CFrame.new(hrp.Position - hrp.CFrame.LookVector * followDistance, hrp.Position)
        end
    end
end)

local function teleportToMouse()
    local char = player.Character
    if char and mouse.Hit then
        char.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.p + Vector3.new(0,3,0))
    end
end

local noclipConn
local function setNoclip(state)
    noclipEnabled = state
    if noclipConn then noclipConn:Disconnect() end
    if noclipEnabled then
        noclipConn = RunService.Stepped:Connect(function()
            for _, v in pairs((player.Character or {}):GetDescendants()) do
                if v:IsA("BasePart") then v.CanCollide = false end
            end
        end)
    end
end

---------------------------------------------------------------------------
-- GUI + CHECKBOX HỆ THỐNG
---------------------------------------------------------------------------

local HotkeyEnabled = {
    E = true,
    R = true,
    T = true,
    Y = true,
    V = true,
    RightShift = true
}

local function createGUI()
    local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
    gui.Name = "FlyMenuGui"
    gui.ResetOnSpawn = false

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.new(0, 330, 0, 420)
    frame.Position = UDim2.new(1, -330, 0, 242)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,30)

    local function label(text, y)
        local lbl = Instance.new("TextLabel", frame)
        lbl.Size = UDim2.new(1, -40, 0, 25)
        lbl.Position = UDim2.new(0, 5, 0, y)
        lbl.BackgroundTransparency = 1
        lbl.TextColor3 = Color3.new(1,1,1)
        lbl.Font = Enum.Font.Gotham
        lbl.TextSize = 14
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Text = text
        return lbl
    end

    local function checkbox(key, y)
        local box = Instance.new("TextButton", frame)
        box.Size = UDim2.new(0, 20, 0, 20)
        box.Position = UDim2.new(0, 300, 0, y + 2)
        box.BackgroundColor3 = Color3.fromRGB(60,60,60)
        box.Font = Enum.Font.GothamBold
        box.TextSize = 16
        box.TextColor3 = Color3.fromRGB(255,255,255)
        box.Text = "✔"
        box.MouseButton1Click:Connect(function()
            HotkeyEnabled[key] = not HotkeyEnabled[key]
            box.Text = HotkeyEnabled[key] and "✔" or ""
        end)
    end

    local Y = 5
    label("E to follow", Y) checkbox("E", Y)
    Y += 30
    label("R to fly", Y) checkbox("R", Y)
    Y += 30
    label("T to turn on/off noclip ", Y) checkbox("T", Y)
    Y += 30
    label("Y to teleport", Y) checkbox("Y", Y)
    Y += 30
    label("V tp to void, V again tp to ground (For TSB)", Y) checkbox("V", Y)
    Y += 30
    label("RightShift to turn on/off menu", Y) checkbox("RightShift", Y)

end

createGUI()

---------------------------------------------------------------------------
-- INPUT HOTKEY TỰ ĐỘNG CHECK CHECKBOX TRƯỚC
---------------------------------------------------------------------------

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end

    if input.KeyCode == Enum.KeyCode.R and HotkeyEnabled.R then
        flying = not flying
        (flying and startFlying or stopFlying)()

    elseif input.KeyCode == Enum.KeyCode.V and HotkeyEnabled.V then
        local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            if not vToggled then hrp.CFrame = CFrame.new(teleportLocations.V1) else hrp.CFrame = CFrame.new(teleportLocations.V2) end
            vToggled = not vToggled
        end

    elseif input.KeyCode == Enum.KeyCode.Y and HotkeyEnabled.Y then
        teleportToMouse()

    elseif input.KeyCode == Enum.KeyCode.T and HotkeyEnabled.T then
        setNoclip(not noclipEnabled)

    elseif input.KeyCode == Enum.KeyCode.E and HotkeyEnabled.E then
        if not following then currentTarget = getClosestPlayerInSight() following = currentTarget ~= nil else following = false currentTarget = nil end

    elseif input.KeyCode == Enum.KeyCode.RightShift and HotkeyEnabled.RightShift then
        local g = player.PlayerGui:FindFirstChild("FlyMenuGui")
        if g then g.Frame.Visible = not g.Frame.Visible end
    end
end)
