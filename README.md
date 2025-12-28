-- SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local Player = Players.LocalPlayer

-- =========================
-- STATE
-- =========================
local State = {
    FPSBoost = false,
    PingBoost = false,
    Disable3D = false,
    AutoLowGfx = false,
    UltraExtreme = false,
    UnlockFPS = false,
    FPSLock = 60,
    UIHidden = false
}

-- =========================
-- MAIN UI
-- =========================
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "UltraExtremeUI"

local main = Instance.new("Frame", gui)
main.Size = UDim2.fromScale(0.36, 0.50)
main.Position = UDim2.fromScale(0.32, 0.25)
main.BackgroundColor3 = Color3.fromRGB(10, 20, 38)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0,16)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1,0,0.12,0)
title.BackgroundTransparency = 1
title.Text = "⚡ ULTRA EXTREME BOOSTER"
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.TextColor3 = Color3.fromRGB(200,220,255)

local function Button(txt,y)
    local b = Instance.new("TextButton", main)
    b.Size = UDim2.new(0.9,0,0.085,0)
    b.Position = UDim2.new(0.05,0,y,0)
    b.BackgroundColor3 = Color3.fromRGB(18,34,65)
    b.TextColor3 = Color3.fromRGB(230,240,255)
    b.Text = txt
    b.Font = Enum.Font.Gotham
    b.TextSize = 13
    b.BorderSizePixel = 0
    Instance.new("UICorner", b).CornerRadius = UDim.new(0,12)
    return b
end

-- BUTTONS
local btnFPS   = Button("FPS BOOST : OFF", 0.14)
local btnPing  = Button("PING BOOST : OFF", 0.23)
local btn3D    = Button("DISABLE 3D : OFF", 0.32)
local btnLow   = Button("AUTO LOW GRAPHIC : OFF", 0.41)
local btnUltra = Button("ULTRA EXTREME : OFF", 0.50)
local btnUFPS  = Button("UNLOCK FPS : OFF", 0.59)
local btnLFPS  = Button("LOCK FPS (60)", 0.68)
local btnHide  = Button("HIDE UI : OFF", 0.77)

-- =========================
-- MINI FLOATING FPS/PING
-- =========================
local miniGui = Instance.new("ScreenGui", game.CoreGui)
miniGui.Name = "MiniFPSUI"

local miniFrame = Instance.new("Frame", miniGui)
miniFrame.Size = UDim2.fromOffset(120,40)
miniFrame.Position = UDim2.fromScale(0.8,0.05)
miniFrame.BackgroundColor3 = Color3.fromRGB(12,25,45)
miniFrame.BorderSizePixel = 0
miniFrame.Active = true
miniFrame.Draggable = true
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0,8)

local miniLabel = Instance.new("TextLabel", miniFrame)
miniLabel.Size = UDim2.new(1,0,1,0)
miniLabel.BackgroundTransparency = 1
miniLabel.Font = Enum.Font.GothamBold
miniLabel.TextSize = 13
miniLabel.TextColor3 = Color3.fromRGB(200,220,255)
miniLabel.Text = "FPS: -- | Ping: -- ms"

-- =========================
-- FUNCTIONS
-- =========================
local function FPSBoost(on)
    for _,v in ipairs(Lighting:GetChildren()) do
        if v:IsA("PostEffect") then
            v.Enabled = not on
        end
    end
    Lighting.GlobalShadows = not on
    Lighting.FogEnd = on and 9e9 or 100000
end

local function PingBoost(on)
    pcall(function()
        settings().Network.IncomingReplicationLag = on and 0 or 0.1
    end)
end

local function Disable3D(on)
    RunService:Set3dRenderingEnabled(not on)
end

local function AutoLowGraphic()
    for _,v in ipairs(Workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.Plastic
            v.Reflectance = 0
            v.CastShadow = false
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v.Transparency = 1
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end

local function SetFPS()
    if setfpscap then
        if State.UnlockFPS then
            setfpscap(9999)
        else
            setfpscap(State.FPSLock)
        end
    end
end

-- =========================
-- BUTTON EVENTS
-- =========================
btnFPS.MouseButton1Click:Connect(function()
    State.FPSBoost = not State.FPSBoost
    FPSBoost(State.FPSBoost)
    btnFPS.Text = "FPS BOOST : "..(State.FPSBoost and "ON" or "OFF")
end)

btnPing.MouseButton1Click:Connect(function()
    State.PingBoost = not State.PingBoost
    PingBoost(State.PingBoost)
    btnPing.Text = "PING BOOST : "..(State.PingBoost and "ON" or "OFF")
end)

btn3D.MouseButton1Click:Connect(function()
    State.Disable3D = not State.Disable3D
    Disable3D(State.Disable3D)
    btn3D.Text = "DISABLE 3D : "..(State.Disable3D and "ON" or "OFF")
end)

btnLow.MouseButton1Click:Connect(function()
    State.AutoLowGfx = not State.AutoLowGfx
    btnLow.Text = "AUTO LOW GRAPHIC : "..(State.AutoLowGfx and "ON" or "OFF")
end)

btnUltra.MouseButton1Click:Connect(function()
    State.UltraExtreme = not State.UltraExtreme
    FPSBoost(State.UltraExtreme)
    PingBoost(State.UltraExtreme)
    Disable3D(State.UltraExtreme)
    State.AutoLowGfx = State.UltraExtreme
    btnUltra.Text = "ULTRA EXTREME : "..(State.UltraExtreme and "ON" or "OFF")
end)

btnUFPS.MouseButton1Click:Connect(function()
    State.UnlockFPS = not State.UnlockFPS
    SetFPS()
    btnUFPS.Text = "UNLOCK FPS : "..(State.UnlockFPS and "ON" or "OFF")
end)

btnLFPS.MouseButton1Click:Connect(function()
    State.FPSLock = (State.FPSLock == 60 and 90) or (State.FPSLock == 90 and 120) or 60
    SetFPS()
    btnLFPS.Text = "LOCK FPS ("..State.FPSLock..")"
end)

btnHide.MouseButton1Click:Connect(function()
    State.UIHidden = not State.UIHidden
    main.Visible = not State.UIHidden
    btnHide.Text = "HIDE UI : "..(State.UIHidden and "ON" or "OFF")
end)

-- =========================
-- LOOPING AUTO LOW GRAPHIC & ULTRA EXTREME
-- =========================
RunService.Heartbeat:Connect(function()
    if State.AutoLowGfx or State.UltraExtreme then
        AutoLowGraphic()
    end
end)

-- =========================
-- FPS & PING REALTIME
-- =========================
local frames = 0
local last = tick()

RunService.RenderStepped:Connect(function()
    frames += 1
    if tick() - last >= 1 then
        local fps = frames
        frames = 0
        last = tick()
        local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        miniLabel.Text = "FPS: "..fps.." | Ping: "..ping.." ms"
    end
end)
local btnLow   = Button("AUTO LOW GRAPHIC : OFF", 0.41)
local btnUltra = Button("ULTRA EXTREME : OFF", 0.50)
local btnUFPS  = Button("UNLOCK FPS : OFF", 0.59)
local btnLFPS  = Button("LOCK FPS (60)", 0.68)

local stat = Instance.new("TextLabel", main)
stat.Size = UDim2.new(1,0,0.12,0)
stat.Position = UDim2.new(0,0,0.82,0)
stat.BackgroundTransparency = 1
stat.Font = Enum.Font.Gotham
stat.TextSize = 12
stat.TextColor3 = Color3.fromRGB(180,220,255)
stat.Text = "FPS: -- | Ping: -- ms"

-- =========================
-- FUNCTIONS
-- =========================
local function FPSBoost(on)
    for _,v in ipairs(Lighting:GetChildren()) do
        if v:IsA("PostEffect") then
            v.Enabled = not on
        end
    end
    Lighting.GlobalShadows = not on
    Lighting.FogEnd = on and 9e9 or 100000
end

local function PingBoost(on)
    pcall(function()
        settings().Network.IncomingReplicationLag = on and 0 or 0.1
    end)
end

local function Disable3D(on)
    RunService:Set3dRenderingEnabled(not on)
end

local function AutoLowGraphic()
    for _,v in ipairs(Workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.Plastic
            v.Reflectance = 0
            v.CastShadow = false
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v.Transparency = 1
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end

local function SetFPS()
    if setfpscap then
        if State.UnlockFPS then
            setfpscap(9999)
        else
            setfpscap(State.FPSLock)
        end
    end
end

-- =========================
-- BUTTON EVENTS
-- =========================
btnFPS.MouseButton1Click:Connect(function()
    State.FPSBoost = not State.FPSBoost
    FPSBoost(State.FPSBoost)
    btnFPS.Text = "FPS BOOST : " .. (State.FPSBoost and "ON" or "OFF")
end)

btnPing.MouseButton1Click:Connect(function()
    State.PingBoost = not State.PingBoost
    PingBoost(State.PingBoost)
    btnPing.Text = "PING BOOST : " .. (State.PingBoost and "ON" or "OFF")
end)

btn3D.MouseButton1Click:Connect(function()
    State.Disable3D = not State.Disable3D
    Disable3D(State.Disable3D)
    btn3D.Text = "DISABLE 3D : " .. (State.Disable3D and "ON" or "OFF")
end)

btnLow.MouseButton1Click:Connect(function()
    State.AutoLowGfx = not State.AutoLowGfx
    if State.AutoLowGfx then AutoLowGraphic() end
    btnLow.Text = "AUTO LOW GRAPHIC : " .. (State.AutoLowGfx and "ON" or "OFF")
end)

btnUltra.MouseButton1Click:Connect(function()
    State.UltraExtreme = not State.UltraExtreme
    FPSBoost(State.UltraExtreme)
    PingBoost(State.UltraExtreme)
    Disable3D(State.UltraExtreme)
    if State.UltraExtreme then AutoLowGraphic() end
    btnUltra.Text = "ULTRA EXTREME : " .. (State.UltraExtreme and "ON" or "OFF")
end)

btnUFPS.MouseButton1Click:Connect(function()
    State.UnlockFPS = not State.UnlockFPS
    SetFPS()
    btnUFPS.Text = "UNLOCK FPS : " .. (State.UnlockFPS and "ON" or "OFF")
end)

btnLFPS.MouseButton1Click:Connect(function()
    State.FPSLock = (State.FPSLock == 60 and 90) or (State.FPSLock == 90 and 120) or 60
    SetFPS()
    btnLFPS.Text = "LOCK FPS ("..State.FPSLock..")"
end)

-- =========================
-- FPS & PING REALTIME
-- =========================
local frames = 0
local last = tick()

RunService.RenderStepped:Connect(function()
    frames += 1
    if tick() - last >= 1 then
        local fps = frames
        frames = 0
        last = tick()
        local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        stat.Text = "FPS: "..fps.." | Ping: "..ping.." ms"
    end
end)
