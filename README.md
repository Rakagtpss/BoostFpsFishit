--[[ 
 ULTRA EXTREME BOOSTER
 Mini-Size & Modern UI
 Floating FPS & Ping
 Delta Executor Compatible
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local Player = Players.LocalPlayer

-- STATE
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
-- MINI UI MODERN
-- =========================
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "MiniExtremeUI"

local main = Instance.new("Frame", gui)
main.Size = UDim2.fromOffset(180,250) -- kecil & compact
main.Position = UDim2.fromScale(0.7,0.05)
main.BackgroundColor3 = Color3.fromRGB(22,28,38) -- dark minimal
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0,10)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1,0,0.12,0)
title.BackgroundTransparency = 1
title.Text = "⚡ MINI EXTREME"
title.Font = Enum.Font.GothamBold
title.TextSize = 13
title.TextColor3 = Color3.fromRGB(200,230,255)

local function Button(txt,y)
    local b = Instance.new("TextButton", main)
    b.Size = UDim2.new(0.85,0,0.08,0)
    b.Position = UDim2.new(0.075,0,y,0)
    b.BackgroundColor3 = Color3.fromRGB(28,38,55)
    b.TextColor3 = Color3.fromRGB(220,240,255)
    b.Text = txt
    b.Font = Enum.Font.Gotham
    b.TextSize = 12
    b.BorderSizePixel = 0
    Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
    return b
end

-- BUTTONS
local btnFPS   = Button("FPS BOOST: OFF",0.15)
local btnPing  = Button("PING BOOST: OFF",0.25)
local btn3D    = Button("DISABLE 3D: OFF",0.35)
local btnLow   = Button("LOW GRAPHIC: OFF",0.45)
local btnUltra = Button("ULTRA EXTREME: OFF",0.55)
local btnHide  = Button("HIDE UI: OFF",0.65)

-- MINI FLOATING COUNTER
local miniGui = Instance.new("ScreenGui",game.CoreGui)
miniGui.Name = "MiniCounter"

local miniFrame = Instance.new("Frame",miniGui)
miniFrame.Size = UDim2.fromOffset(100,35)
miniFrame.Position = UDim2.fromScale(0.8,0.02)
miniFrame.BackgroundColor3 = Color3.fromRGB(12,22,35)
miniFrame.BorderSizePixel = 0
miniFrame.Active = true
miniFrame.Draggable = true
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0,6)

local miniLabel = Instance.new("TextLabel", miniFrame)
miniLabel.Size = UDim2.new(1,0,1,0)
miniLabel.BackgroundTransparency = 1
miniLabel.Font = Enum.Font.GothamBold
miniLabel.TextSize = 12
miniLabel.TextColor3 = Color3.fromRGB(200,220,255)
miniLabel.Text = "FPS: -- | PING: --"

-- =========================
-- FUNCTIONS
-- =========================
local function FPSBoost(on)
    for _,v in ipairs(Lighting:GetChildren()) do
        if v:IsA("PostEffect") then v.Enabled = not on end
    end
    Lighting.GlobalShadows = not on
    Lighting.FogEnd = on and 9e9 or 100000
end

local function PingBoost(on)
    pcall(function() settings().Network.IncomingReplicationLag = on and 0 or 0.1 end)
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
        elseif v:IsA("Decal") or v:IsA("Texture") then v.Transparency = 1
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then v.Enabled = false end
    end
end

local function SetFPS()
    if setfpscap then
        if State.UnlockFPS then setfpscap(9999) else setfpscap(State.FPSLock) end
    end
end

-- =========================
-- BUTTON EVENTS
-- =========================
btnFPS.MouseButton1Click:Connect(function()
    State.FPSBoost = not State.FPSBoost
    FPSBoost(State.FPSBoost)
    btnFPS.Text = "FPS BOOST: "..(State.FPSBoost and "ON" or "OFF")
end)

btnPing.MouseButton1Click:Connect(function()
    State.PingBoost = not State.PingBoost
    PingBoost(State.PingBoost)
    btnPing.Text = "PING BOOST: "..(State.PingBoost and "ON" or "OFF")
end)

btn3D.MouseButton1Click:Connect(function()
    State.Disable3D = not State.Disable3D
    Disable3D(State.Disable3D)
    btn3D.Text = "DISABLE 3D: "..(State.Disable3D and "ON" or "OFF")
end)

btnLow.MouseButton1Click:Connect(function()
    State.AutoLowGfx = not State.AutoLowGfx
    btnLow.Text = "LOW GRAPHIC: "..(State.AutoLowGfx and "ON" or "OFF")
end)

btnUltra.MouseButton1Click:Connect(function()
    State.UltraExtreme = not State.UltraExtreme
    FPSBoost(State.UltraExtreme)
    PingBoost(State.UltraExtreme)
    Disable3D(State.UltraExtreme)
    State.AutoLowGfx = State.UltraExtreme
    btnUltra.Text = "ULTRA EXTREME: "..(State.UltraExtreme and "ON" or "OFF")
end)

btnHide.MouseButton1Click:Connect(function()
    State.UIHidden = not State.UIHidden
    main.Visible = not State.UIHidden
    btnHide.Text = "HIDE UI: "..(State.UIHidden and "ON" or "OFF")
end)

-- =========================
-- LOOP AUTO LOW GRAPHIC
-- =========================
RunService.Heartbeat:Connect(function()
    if State.AutoLowGfx or State.UltraExtreme then AutoLowGraphic() end
end)

-- =========================
-- MINI FLOATING COUNTER
-- =========================
local frames,last = 0,tick()
RunService.RenderStepped:Connect(function()
    frames += 1
    if tick()-last >= 1 then
        local fps = frames
        frames = 0
        last = tick()
        local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        miniLabel.Text = "FPS:"..fps.." | PING:"..ping
    end
end)
tValue())
        stat.Text = "FPS: "..fps.." | Ping: "..ping.." ms"
    end
end)
