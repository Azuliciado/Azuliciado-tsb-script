--[[
    ╔══════════════════════════════════════════════════════════════════╗
    ║                                                                  ║
    ║        ░█▀▀█ ░▀▀█░█  ░█  ░█  ░█   ▀█▀ ░█▀▀█  ▀█▀  ░█▀▀█ ░█▀▀▄ ║
    ║        ░█▄▄█  ░▄▀ ░█  ░█  ░█   ░█  ░█  ░█     ░█   ░█▄▄█ ░█░░█ ║
    ║        ░█░░░ ░█▄▄ ░█▄▄░█▄▄▀█▄▄ ▄█▄ ░█  ░█▄▄█  ▄█▄  ░█░░░ ░█▄▄▀ ║
    ║                                                                  ║
    ║                    by  A Z U L I C I A D O                       ║
    ║                         v3  ·  2025                              ║
    ╚══════════════════════════════════════════════════════════════════╝

    ▸  LocalScript  →  StarterPlayerScripts
    ▸  DeathCounterLabel.lua  →  ServerScriptService  (separate, needs server)

    TABS:
      ① STATS       FPS & Ping HUD
      ② COMBAT      Kiba · E-Dash · Lethal · Back-Dash · Oreo
      ③ LOCK        Player Lock System
      ④ ANTI-LAG    Graphics · Particles · Anti-Lag V2 Loader
]]

-- ════════════════════════════════════════════════════════════════════
--  SERVICES
-- ════════════════════════════════════════════════════════════════════
local Players             = game:GetService("Players")
local RunService          = game:GetService("RunService")
local TweenService        = game:GetService("TweenService")
local UserInputService    = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Workspace           = game:GetService("Workspace")
local Stats               = game:GetService("Stats")
local Lighting            = game:GetService("Lighting")

local player = Players.LocalPlayer
local camera = Workspace.CurrentCamera

-- ════════════════════════════════════════════════════════════════════
--  COLOUR PALETTE  ·  Blue / Grey / Black / White
-- ════════════════════════════════════════════════════════════════════
local T = {
    -- Backgrounds (deep navy-blacks)
    bg0       = Color3.fromRGB( 5,   7,  14),   -- deepest void
    bg1       = Color3.fromRGB( 9,  12,  22),   -- main panel
    bg2       = Color3.fromRGB(13,  17,  32),   -- cards
    bg3       = Color3.fromRGB(18,  24,  44),   -- elevated cards
    bg4       = Color3.fromRGB(24,  32,  58),   -- hover / active bg

    -- Blues (electric → ice)
    blue      = Color3.fromRGB(50,  130, 255),  -- primary electric blue
    blueLight = Color3.fromRGB(90,  165, 255),  -- light blue
    blueDim   = Color3.fromRGB(30,   75, 160),  -- muted blue
    blueFrost = Color3.fromRGB(160, 210, 255),  -- icy frost blue
    blueGlow  = Color3.fromRGB(20,   80, 220),  -- glow tint

    -- Greys
    grey1     = Color3.fromRGB(40,  50,  75),   -- darkest grey (borders)
    grey2     = Color3.fromRGB(65,  80, 115),   -- mid grey
    grey3     = Color3.fromRGB(110, 130, 170),  -- text grey
    grey4     = Color3.fromRGB(160, 175, 210),  -- light grey text

    -- Whites
    white     = Color3.fromRGB(235, 240, 255),  -- warm white
    whitePure = Color3.fromRGB(255, 255, 255),

    -- Status
    green     = Color3.fromRGB( 60, 220, 140),
    red       = Color3.fromRGB(220,  70,  80),
    yellow    = Color3.fromRGB(240, 195,  60),

    -- On/Off pills
    onBg      = Color3.fromRGB(25,  75, 200),
    onText    = Color3.fromRGB(190, 220, 255),
    offBg     = Color3.fromRGB(12,  16,  30),
    offText   = Color3.fromRGB(70,  90, 130),
}

-- ════════════════════════════════════════════════════════════════════
--  TWEEN HELPERS
-- ════════════════════════════════════════════════════════════════════
local function tw(obj, props, t, style, dir)
    TweenService:Create(obj,
        TweenInfo.new(t or 0.2, style or Enum.EasingStyle.Quint, dir or Enum.EasingDirection.Out),
        props
    ):Play()
end
local function mkCorner(p, r)
    local c = Instance.new("UICorner", p); c.CornerRadius = UDim.new(0, r or 8)
end
local function mkStroke(p, col, thick, trans)
    local s = Instance.new("UIStroke", p)
    s.Color = col or T.grey1; s.Thickness = thick or 1
    s.Transparency = trans or 0
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    return s
end
local function mkGradient(p, c0, c1, rot)
    local g = Instance.new("UIGradient", p)
    g.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, c0),
        ColorSequenceKeypoint.new(1, c1),
    }
    g.Rotation = rot or 0
    return g
end
local function mkLabel(parent, text, size, font, color, xalign)
    local l = Instance.new("TextLabel", parent)
    l.BackgroundTransparency = 1
    l.Text = text; l.TextSize = size or 13
    l.Font = font or Enum.Font.Gotham
    l.TextColor3 = color or T.grey4
    l.TextXAlignment = xalign or Enum.TextXAlignment.Left
    return l
end
local function mkBtn(parent, text, size, font, color)
    local b = Instance.new("TextButton", parent)
    b.BackgroundTransparency = 1
    b.Text = text; b.TextSize = size or 13
    b.Font = font or Enum.Font.GothamBold
    b.TextColor3 = color or T.grey4
    b.AutoButtonColor = false
    return b
end

-- Glowing horizontal separator
local function mkDivider(parent, order)
    local d = Instance.new("Frame", parent)
    d.Size = UDim2.new(1, 0, 0, 1)
    d.BackgroundColor3 = T.grey1
    d.BackgroundTransparency = 0.3
    d.BorderSizePixel = 0
    d.LayoutOrder = order or 99
    mkGradient(d, Color3.fromRGB(5,7,14), T.blueDim, 0)
    return d
end

-- Section header label
local function mkSection(page, text, order)
    local f = Instance.new("Frame", page)
    f.Size = UDim2.new(1, 0, 0, 22)
    f.BackgroundTransparency = 1
    f.LayoutOrder = order or 1

    local l = mkLabel(f, text, 9, Enum.Font.GothamBlack, T.blueDim, Enum.TextXAlignment.Left)
    l.Size = UDim2.new(1, -16, 1, 0)
    l.Position = UDim2.new(0, 8, 0, 0)
    l.TextTransparency = 0
    l.ZIndex = 13
    -- small accent line
    local line = Instance.new("Frame", f)
    line.Size = UDim2.new(1, -8, 0, 1); line.Position = UDim2.new(0, 4, 1, -1)
    line.BackgroundColor3 = T.grey1; line.BorderSizePixel = 0; line.ZIndex = 13
    mkGradient(line, T.blue, Color3.fromRGB(5,7,14), 0)
    return f
end

-- Fancy pill toggle
local function mkPill(parent, onCb)
    local pill = Instance.new("TextButton", parent)
    pill.Size = UDim2.new(0, 52, 0, 24)
    pill.BackgroundColor3 = T.offBg
    pill.Text = "OFF"; pill.Font = Enum.Font.GothamBlack; pill.TextSize = 10
    pill.TextColor3 = T.offText; pill.AutoButtonColor = false; pill.ZIndex = 20
    mkCorner(pill, 12)
    mkStroke(pill, T.grey1, 1, 0.4)
    local on = false
    local function refresh()
        if on then
            tw(pill, {BackgroundColor3 = T.onBg, TextColor3 = T.blueFrost}); pill.Text = "ON"
            tw(pill:FindFirstChildOfClass("UIStroke") or mkStroke(pill, T.blue, 1, 0), {Color = T.blue, Transparency = 0})
        else
            tw(pill, {BackgroundColor3 = T.offBg, TextColor3 = T.offText}); pill.Text = "OFF"
            local s = pill:FindFirstChildOfClass("UIStroke")
            if s then tw(s, {Color = T.grey1, Transparency = 0.4}) end
        end
        if onCb then onCb(on) end
    end
    pill.MouseButton1Click:Connect(function() on = not on; refresh() end)
    local function setOn(v) on = v; refresh() end
    return pill, setOn
end

-- ════════════════════════════════════════════════════════════════════
--  SCREEN GUI
-- ════════════════════════════════════════════════════════════════════
local playerGui = player:WaitForChild("PlayerGui")
if playerGui:FindFirstChild("AzuliciadoGUI") then
    playerGui:FindFirstChild("AzuliciadoGUI"):Destroy()
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AzuliciadoGUI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.DisplayOrder = 15
screenGui.IgnoreGuiInset = true
screenGui.Parent = playerGui

-- ════════════════════════════════════════════════════════════════════
--  MAIN PANEL
-- ════════════════════════════════════════════════════════════════════
local PANEL_W  = 330
local PANEL_H  = 520
local HEADER_H = 72
local TABBAR_H = 40

local panel = Instance.new("Frame", screenGui)
panel.Name = "Panel"
panel.Size = UDim2.new(0, PANEL_W, 0, PANEL_H)
panel.Position = UDim2.new(0, 28, 0.5, -PANEL_H / 2)
panel.BackgroundColor3 = T.bg1
panel.BorderSizePixel = 0
panel.ClipsDescendants = true
panel.ZIndex = 10
mkCorner(panel, 16)
mkStroke(panel, T.grey1, 1, 0)

-- Subtle top-edge glow strip
local topGlow = Instance.new("Frame", panel)
topGlow.Size = UDim2.new(1, 0, 0, 2)
topGlow.BackgroundColor3 = T.blue
topGlow.BorderSizePixel = 0; topGlow.ZIndex = 20
mkGradient(topGlow, T.blueFrost, T.blueDim, 0)

-- Background gradient (gives depth)
local bgGrad = Instance.new("Frame", panel)
bgGrad.Size = UDim2.new(1, 0, 1, 0)
bgGrad.BackgroundColor3 = T.bg0
bgGrad.BorderSizePixel = 0; bgGrad.ZIndex = 9
mkGradient(bgGrad, T.bg1, T.bg0, 110)

-- ── HEADER ──────────────────────────────────────────────────────────
local header = Instance.new("Frame", panel)
header.Size = UDim2.new(1, 0, 0, HEADER_H)
header.BackgroundColor3 = T.bg2
header.BorderSizePixel = 0; header.ZIndex = 14; header.Active = true
mkGradient(header, T.bg3, T.bg1, 90)

-- Diagonal blue accent bar on the left of header
local headerAccent = Instance.new("Frame", header)
headerAccent.Size = UDim2.new(0, 3, 0.6, 0)
headerAccent.Position = UDim2.new(0, 0, 0.2, 0)
headerAccent.BackgroundColor3 = T.blue
headerAccent.BorderSizePixel = 0; headerAccent.ZIndex = 15
mkCorner(headerAccent, 2)
mkGradient(headerAccent, T.blueFrost, T.blue, 90)

-- Logo / title area
local logoIcon = mkLabel(header, "◈", 22, Enum.Font.GothamBlack, T.blue, Enum.TextXAlignment.Left)
logoIcon.Size = UDim2.new(0, 30, 0, 28); logoIcon.Position = UDim2.new(0, 14, 0, 10); logoIcon.ZIndex = 15

local titleMain = mkLabel(header, "AZULICIADO", 18, Enum.Font.GothamBlack, T.white, Enum.TextXAlignment.Left)
titleMain.Size = UDim2.new(1, -90, 0, 22); titleMain.Position = UDim2.new(0, 44, 0, 8); titleMain.ZIndex = 15

local titleSub = mkLabel(header, "combat utility  ·  v3  ·  by Azuliciado", 10, Enum.Font.Gotham, T.grey3, Enum.TextXAlignment.Left)
titleSub.Size = UDim2.new(1, -90, 0, 14); titleSub.Position = UDim2.new(0, 44, 0, 30); titleSub.ZIndex = 15

-- Status dot (shows active feature count)
local statusDot = Instance.new("Frame", header)
statusDot.Size = UDim2.new(0, 8, 0, 8); statusDot.Position = UDim2.new(0, 44, 0, 48)
statusDot.BackgroundColor3 = T.blue; statusDot.BorderSizePixel = 0; statusDot.ZIndex = 15
mkCorner(statusDot, 4)

local statusTxt = mkLabel(header, "all systems ready", 9, Enum.Font.Gotham, T.grey3, Enum.TextXAlignment.Left)
statusTxt.Size = UDim2.new(1, -90, 0, 12); statusTxt.Position = UDim2.new(0, 56, 0, 48); statusTxt.ZIndex = 15

-- Close button
local closeBtn = mkBtn(header, "✕", 13, Enum.Font.GothamBold, T.grey3)
closeBtn.Size = UDim2.new(0, 30, 0, 30); closeBtn.Position = UDim2.new(1, -38, 0, 8)
closeBtn.BackgroundColor3 = T.bg3; closeBtn.BackgroundTransparency = 0
closeBtn.ZIndex = 15; closeBtn.AutoButtonColor = false
mkCorner(closeBtn, 8)
mkStroke(closeBtn, T.grey1, 1, 0.3)
closeBtn.MouseButton1Click:Connect(function() panel.Visible = false end)
closeBtn.MouseEnter:Connect(function() tw(closeBtn, {BackgroundColor3 = T.red, TextColor3 = T.white}) end)
closeBtn.MouseLeave:Connect(function() tw(closeBtn, {BackgroundColor3 = T.bg3, TextColor3 = T.grey3}) end)

-- Header divider
local hDiv = Instance.new("Frame", panel)
hDiv.Size = UDim2.new(1, 0, 0, 1); hDiv.Position = UDim2.new(0, 0, 0, HEADER_H)
hDiv.BackgroundColor3 = T.grey1; hDiv.BorderSizePixel = 0; hDiv.ZIndex = 14
mkGradient(hDiv, T.blue, T.bg0, 0)

-- ── TAB BAR ─────────────────────────────────────────────────────────
local tabBarY = HEADER_H + 1
local tabBar = Instance.new("Frame", panel)
tabBar.Size = UDim2.new(1, 0, 0, TABBAR_H)
tabBar.Position = UDim2.new(0, 0, 0, tabBarY)
tabBar.BackgroundColor3 = T.bg2; tabBar.BorderSizePixel = 0; tabBar.ZIndex = 13
mkGradient(tabBar, T.bg2, T.bg1, 90)

local TAB_DEFS = {
    {name = "STATS",     icon = "◉"},
    {name = "COMBAT",    icon = "◈"},
    {name = "LOCK",      icon = "◎"},
    {name = "ANTI-LAG",  icon = "◆"},
}
local tabBtns      = {}
local tabIndicators = {}
local tabW = PANEL_W / #TAB_DEFS

for i, def in ipairs(TAB_DEFS) do
    local btn = Instance.new("TextButton", tabBar)
    btn.Size = UDim2.new(0, tabW - 2, 1, -4)
    btn.Position = UDim2.new(0, (i-1)*tabW + 1, 0, 2)
    btn.BackgroundColor3 = T.bg2; btn.BackgroundTransparency = 1
    btn.Text = def.icon .. "  " .. def.name
    btn.Font = Enum.Font.GothamBold; btn.TextSize = 10
    btn.TextColor3 = T.grey3; btn.AutoButtonColor = false; btn.ZIndex = 14
    mkCorner(btn, 6)

    -- Bottom indicator line
    local ind = Instance.new("Frame", btn)
    ind.Size = UDim2.new(0.7, 0, 0, 2)
    ind.Position = UDim2.new(0.15, 0, 1, -2)
    ind.BackgroundColor3 = T.blue; ind.BorderSizePixel = 0
    ind.BackgroundTransparency = 1; ind.ZIndex = 15
    mkCorner(ind, 1)
    mkGradient(ind, T.blueFrost, T.blue, 0)

    tabBtns[i] = btn
    tabIndicators[i] = ind
end

-- Tab divider
local tabDiv = Instance.new("Frame", panel)
tabDiv.Size = UDim2.new(1, 0, 0, 1)
tabDiv.Position = UDim2.new(0, 0, 0, tabBarY + TABBAR_H)
tabDiv.BackgroundColor3 = T.grey1; tabDiv.BorderSizePixel = 0; tabDiv.ZIndex = 13

-- ── CONTENT PAGES ───────────────────────────────────────────────────
local contentY = tabBarY + TABBAR_H + 2
local contentH = PANEL_H - contentY

local tabPages = {}
for i = 1, #TAB_DEFS do
    local page = Instance.new("ScrollingFrame", panel)
    page.Name = "Page" .. i
    page.Size = UDim2.new(1, -4, 0, contentH - 4)
    page.Position = UDim2.new(0, 2, 0, contentY + 2)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 3
    page.ScrollBarImageColor3 = T.blueDim
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.Visible = false; page.ZIndex = 12

    local pad = Instance.new("UIPadding", page)
    pad.PaddingLeft = UDim.new(0, 6); pad.PaddingRight = UDim.new(0, 6)
    pad.PaddingTop = UDim.new(0, 6); pad.PaddingBottom = UDim.new(0, 6)

    local layout = Instance.new("UIListLayout", page)
    layout.Padding = UDim.new(0, 5)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        page.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 12)
    end)

    tabPages[i] = page
end

local activeTab = 0
local function switchTab(idx)
    if activeTab == idx then return end
    activeTab = idx
    for i, page in ipairs(tabPages) do
        page.Visible = (i == idx)
        if i == idx then
            tw(tabBtns[i], {TextColor3 = T.blueFrost, BackgroundTransparency = 0, BackgroundColor3 = T.bg3})
            tw(tabIndicators[i], {BackgroundTransparency = 0})
        else
            tw(tabBtns[i], {TextColor3 = T.grey3, BackgroundTransparency = 1})
            tw(tabIndicators[i], {BackgroundTransparency = 1})
        end
    end
end

for i, btn in ipairs(tabBtns) do
    btn.MouseButton1Click:Connect(function() switchTab(i) end)
    btn.MouseEnter:Connect(function()
        if activeTab ~= i then tw(btn, {TextColor3 = T.grey4, BackgroundTransparency = 0.6, BackgroundColor3 = T.bg3}) end
    end)
    btn.MouseLeave:Connect(function()
        if activeTab ~= i then tw(btn, {TextColor3 = T.grey3, BackgroundTransparency = 1}) end
    end)
end

-- ════════════════════════════════════════════════════════════════════
--  CARD / ROW BUILDERS
-- ════════════════════════════════════════════════════════════════════

-- Feature card (the main row type)
local function mkCard(page, h, order)
    local card = Instance.new("Frame", page)
    card.Size = UDim2.new(1, 0, 0, h)
    card.BackgroundColor3 = T.bg2
    card.BorderSizePixel = 0; card.LayoutOrder = order or 1; card.ZIndex = 13
    mkCorner(card, 9)
    mkStroke(card, T.grey1, 1, 0.5)
    card.MouseEnter:Connect(function() tw(card, {BackgroundColor3 = T.bg3}) end)
    card.MouseLeave:Connect(function() tw(card, {BackgroundColor3 = T.bg2}) end)
    return card
end

-- Blue left-edge accent stripe on cards
local function mkStripe(card)
    local s = Instance.new("Frame", card)
    s.Size = UDim2.new(0, 2, 0.55, 0); s.Position = UDim2.new(0, 0, 0.225, 0)
    s.BackgroundColor3 = T.blue; s.BorderSizePixel = 0; s.ZIndex = 14
    mkCorner(s, 1); mkGradient(s, T.blueFrost, T.blue, 90)
    return s
end

-- Standard feature row with pill toggle
local function mkFeatureCard(page, title, sub, order, onToggle)
    local card = mkCard(page, 52, order)
    mkStripe(card)

    local nl = mkLabel(card, title, 12, Enum.Font.GothamBold, T.white)
    nl.Size = UDim2.new(1, -80, 0, 18); nl.Position = UDim2.new(0, 14, 0, 8); nl.ZIndex = 14

    local sl = mkLabel(card, sub, 10, Enum.Font.Gotham, T.grey3)
    sl.Size = UDim2.new(1, -80, 0, 14); sl.Position = UDim2.new(0, 14, 0, 28); sl.ZIndex = 14

    local pill, setOn = mkPill(card, onToggle)
    pill.Position = UDim2.new(1, -62, 0.5, -12); pill.ZIndex = 14

    return card, setOn
end

-- ════════════════════════════════════════════════════════════════════
--  PAGE 1 · STATS
-- ════════════════════════════════════════════════════════════════════
do
    local page = tabPages[1]
    mkSection(page, "FPS & PING OVERLAY", 1)

    local hudCard = mkCard(page, 90, 2)
    mkStripe(hudCard)

    -- Mini preview badge
    local badge = Instance.new("Frame", hudCard)
    badge.Size = UDim2.new(0, 110, 0, 58); badge.Position = UDim2.new(0, 14, 0.5, -29)
    badge.BackgroundColor3 = T.bg0; badge.BorderSizePixel = 0; badge.ZIndex = 14
    mkCorner(badge, 8); mkStroke(badge, T.grey1, 1, 0.3)
    local bAccent = Instance.new("Frame", badge); bAccent.Size = UDim2.new(1,0,0,2)
    bAccent.BackgroundColor3 = T.blue; bAccent.BorderSizePixel = 0; bAccent.ZIndex = 15
    mkGradient(bAccent, T.blueFrost, T.blue, 0)
    local bTitle = mkLabel(badge, "◉  AzuliStats", 9, Enum.Font.GothamBold, T.blue, Enum.TextXAlignment.Center)
    bTitle.Size = UDim2.new(1,0,0,16); bTitle.Position = UDim2.new(0,0,0,4); bTitle.ZIndex = 15
    local bFps  = mkLabel(badge, "FPS:  --", 13, Enum.Font.GothamBold, T.green, Enum.TextXAlignment.Left)
    bFps.Size = UDim2.new(1,-8,0,18); bFps.Position = UDim2.new(0,6,0,22); bFps.ZIndex = 15
    local bPing = mkLabel(badge, "Ping: --", 13, Enum.Font.GothamBold, T.blueLight, Enum.TextXAlignment.Left)
    bPing.Size = UDim2.new(1,-8,0,18); bPing.Position = UDim2.new(0,6,0,40); bPing.ZIndex = 15

    local nl = mkLabel(hudCard, "FPS & PING HUD", 12, Enum.Font.GothamBold, T.white)
    nl.Size = UDim2.new(0, 130, 0, 18); nl.Position = UDim2.new(0, 136, 0, 10); nl.ZIndex = 14
    local sl = mkLabel(hudCard, "Draggable overlay\nColour-coded · real-time", 10, Enum.Font.Gotham, T.grey3)
    sl.Size = UDim2.new(0, 130, 0, 28); sl.Position = UDim2.new(0, 136, 0, 30); sl.ZIndex = 14
    local statLbl = mkLabel(hudCard, "● Inactive", 9, Enum.Font.GothamBold, T.grey3)
    statLbl.Size = UDim2.new(0, 100, 0, 14); statLbl.Position = UDim2.new(0, 136, 0, 66); statLbl.ZIndex = 14

    -- HUD logic
    local hudGui, hudConn = nil, nil
    local function destroyHud()
        if hudGui then hudGui:Destroy(); hudGui = nil end
        if hudConn then hudConn:Disconnect(); hudConn = nil end
    end
    local function createHud()
        destroyHud()
        hudGui = Instance.new("Frame", screenGui)
        hudGui.Size = UDim2.new(0, 170, 0, 105); hudGui.Position = UDim2.new(1, -184, 0, 12)
        hudGui.BackgroundColor3 = T.bg1; hudGui.BackgroundTransparency = 0.05
        hudGui.BorderSizePixel = 0; hudGui.Active = true; hudGui.ZIndex = 30
        mkCorner(hudGui, 12); mkStroke(hudGui, T.grey1, 1, 0.3)

        local hTop = Instance.new("Frame", hudGui); hTop.Size = UDim2.new(1,0,0,2)
        hTop.BackgroundColor3 = T.blue; hTop.BorderSizePixel = 0; hTop.ZIndex = 31
        mkGradient(hTop, T.blueFrost, T.blue, 0); mkCorner(hTop, 2)

        local hBg = Instance.new("Frame", hudGui); hBg.Size = UDim2.new(1,0,1,0)
        hBg.BackgroundColor3 = T.bg0; hBg.BorderSizePixel = 0; hBg.ZIndex = 29
        mkGradient(hBg, T.bg1, T.bg0, 100)

        local htl = mkLabel(hudGui, "◉  AzuliStats", 11, Enum.Font.GothamBold, T.blue, Enum.TextXAlignment.Center)
        htl.Size = UDim2.new(1,0,0,20); htl.Position = UDim2.new(0,0,0,5); htl.ZIndex = 31

        local hDivF = Instance.new("Frame", hudGui); hDivF.Size = UDim2.new(0.85,0,0,1)
        hDivF.Position = UDim2.new(0.075,0,0,26); hDivF.BackgroundColor3 = T.grey1
        hDivF.BorderSizePixel = 0; hDivF.ZIndex = 31
        mkGradient(hDivF, T.blue, T.bg0, 0)

        local hFps  = mkLabel(hudGui, "FPS:   --", 20, Enum.Font.GothamBold, T.green)
        hFps.Size = UDim2.new(1,-12,0,28); hFps.Position = UDim2.new(0,8,0,32); hFps.ZIndex = 31
        local hPing = mkLabel(hudGui, "Ping:  --", 20, Enum.Font.GothamBold, T.blueLight)
        hPing.Size = UDim2.new(1,-12,0,28); hPing.Position = UDim2.new(0,8,0,60); hPing.ZIndex = 31
        local hFoot = mkLabel(hudGui, "by Azuliciado  •  drag to move", 8, Enum.Font.Gotham, T.grey2, Enum.TextXAlignment.Center)
        hFoot.Size = UDim2.new(1,0,0,12); hFoot.Position = UDim2.new(0,0,0,90); hFoot.ZIndex = 31

        -- Drag
        local dragging, dragInput, mPos, fPos = false,nil,nil,nil
        hudGui.InputBegan:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging=true; mPos=i.Position; fPos=hudGui.Position
                i.Changed:Connect(function() if i.UserInputState==Enum.UserInputState.End then dragging=false end end)
            end
        end)
        hudGui.InputChanged:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseMovement then dragInput=i end
        end)
        UserInputService.InputChanged:Connect(function(i)
            if i==dragInput and dragging then
                local d=i.Position-mPos
                hudGui.Position=UDim2.new(fPos.X.Scale,fPos.X.Offset+d.X,fPos.Y.Scale,fPos.Y.Offset+d.Y)
            end
        end)

        -- Update loop
        local ft = {}
        hudConn = RunService.RenderStepped:Connect(function(dt)
            table.insert(ft, dt); if #ft>10 then table.remove(ft,1) end
            local sum=0; for _,v in ipairs(ft) do sum=sum+v end
            local fps = math.floor(1/(sum/#ft)+0.5)
            local ping=0; pcall(function() ping=math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue()) end)
            hFps.TextColor3  = fps>=55 and T.green or (fps>=30 and T.yellow or T.red)
            hPing.TextColor3 = ping<80 and T.blueLight or (ping<150 and T.yellow or T.red)
            hFps.Text  = "FPS:   " .. fps
            hPing.Text = "Ping:  " .. ping .. "ms"
            bFps.Text  = "FPS:  " .. fps;  bFps.TextColor3  = hFps.TextColor3
            bPing.Text = "Ping: " .. ping .. "ms"; bPing.TextColor3 = hPing.TextColor3
        end)
    end

    local pillHud, _ = mkPill(hudCard, function(on)
        if on then
            createHud()
            statLbl.Text = "● Active"; statLbl.TextColor3 = T.green
        else
            destroyHud()
            statLbl.Text = "● Inactive"; statLbl.TextColor3 = T.grey3
            bFps.Text = "FPS:  --"; bPing.Text = "Ping: --"
        end
    end)
    pillHud.Position = UDim2.new(1, -62, 0.5, -12); pillHud.ZIndex = 14

    -- Credits card
    mkSection(page, "ABOUT", 3)
    local aboutCard = mkCard(page, 56, 4)
    mkStripe(aboutCard)
    local al1 = mkLabel(aboutCard, "◈  AZULICIADO", 13, Enum.Font.GothamBlack, T.blue)
    al1.Size = UDim2.new(1,-20,0,20); al1.Position = UDim2.new(0,14,0,8); al1.ZIndex = 14
    local al2 = mkLabel(aboutCard, "combat utility suite  ·  v3  ·  2025", 10, Enum.Font.Gotham, T.grey3)
    al2.Size = UDim2.new(1,-20,0,14); al2.Position = UDim2.new(0,14,0,28); al2.ZIndex = 14
    local al3 = mkLabel(aboutCard, "made by  Azuliciado", 9, Enum.Font.GothamBold, T.grey2)
    al3.Size = UDim2.new(1,-20,0,14); al3.Position = UDim2.new(0,14,0,40); al3.ZIndex = 14
    -- small spinning star decoration
    local star = mkLabel(aboutCard, "✦", 18, Enum.Font.GothamBlack, T.blue, Enum.TextXAlignment.Right)
    star.Size = UDim2.new(0,28,0,28); star.Position = UDim2.new(1,-40,0,14); star.ZIndex = 14
end

-- ════════════════════════════════════════════════════════════════════
--  PAGE 2 · COMBAT  (Azuliciado)
-- ════════════════════════════════════════════════════════════════════
do
    local page = tabPages[2]

    -- ── Combat state ─────────────────────────────────────────────────
    local CS = {
        kiba   = {enabled=false, key=Enum.KeyCode.Q, conn=nil, cd=false},
        edash  = {enabled=false, key=Enum.KeyCode.E, conn=nil},
        lethal = {enabled=false, key=Enum.KeyCode.F, conn=nil},
        bdash  = {enabled=false, key=nil,            conn=nil},
        oreo   = {enabled=false},
    }
    local bdashSlot   = 1
    local listeningFor = nil
    local aimlockConn  = nil
    local oreoLast1, oreoLast2 = 0, 0
    local oreoCamLocked = false

    local OREO_ANIM1 = {["rbxassetid://13532604085"]=true,["rbxassetid://10469639222"]=true}
    local OREO_ANIM2 = {["rbxassetid://10503381238"]=true}
    local toolGroups = {
        {"Flowing Water","Lethal Whirlwind Stream","Hunter's Grasp","Prey's Peril"},
        {"Doom Dive","Crowd Buster","Hammer Heel","Binding Cloth"},
        {"Machine Gun Blows","Ignition Burst","Blitz Shot","Jet Dive"},
        {"Flash Strike","Whirlwind Kick","Scatter","Explosive Shuriken"},
        {"Homerun","Beatdown","Grand Slam","Foul Ball"},
        {"Quick Slice","Atmos Cleave","Pinpoint Cut","Split Second Counter"},
        {"Crushing Pull","Windstorm Fury","Stone Coffin","Expulsive Push"},
        {"Bullet Barrage","Vanishing Kick","Whirlwind Drop","Head First"},
        {"Normal Punch","Consecutive Punches","Shove","Uppercut"},
    }

    local function getChar()  return player.Character end
    local function getHRP()   local c=getChar(); return c and c:FindFirstChild("HumanoidRootPart") end
    local function fireRemote()
        local c=getChar(); if c and c:FindFirstChild("Communicate") then
            c.Communicate:FireServer(unpack({{Dash=Enum.KeyCode.W,Key=Enum.KeyCode.Q,Goal="KeyPress"}})) end
    end
    local function getNearestLive(from)
        local best,dist=nil,15; local live=Workspace:FindFirstChild("Live"); if not live then return nil end
        local my=getChar()
        for _,m in ipairs(live:GetChildren()) do
            if m:IsA("Model") and m~=my then
                local r=m:FindFirstChild("HumanoidRootPart") or m:FindFirstChild("Head") or m:FindFirstChild("UpperTorso")
                if r then local d=(from-r.Position).Magnitude; if d<dist then dist=d;best=m end end
            end
        end; return best
    end
    local function getNearestPlayerRoot(from)
        local best,dist=nil,50
        for _,p in pairs(Players:GetPlayers()) do
            if p~=player and p.Character then
                local r=p.Character:FindFirstChild("HumanoidRootPart")
                if r then local d=(r.Position-from).Magnitude; if d<dist then dist=d;best=r end end
            end
        end; return best
    end
    local function getClosestCombatPlayer(from)
        local best,dist=nil,math.huge
        for _,p in ipairs(Players:GetPlayers()) do
            if p~=player and p.Character then
                local r=p.Character:FindFirstChild("HumanoidRootPart")
                if r then local d=(from-r.Position).Magnitude; if d<dist then dist=d;best=p end end
            end
        end; return best
    end

    local function startAimlock(target)
        if aimlockConn then aimlockConn:Disconnect() end
        local char=getChar(); local t0=tick()
        aimlockConn=RunService.RenderStepped:Connect(function()
            local cam=Workspace.CurrentCamera
            if not(target and target.Parent and target:FindFirstChild("HumanoidRootPart")) then return end
            if not(char and char:FindFirstChild("HumanoidRootPart")) then return end
            local elapsed=tick()-t0; local tp=target.HumanoidRootPart.Position; local cp=cam.CFrame.Position
            if elapsed<0.15 then
                cam.CFrame=CFrame.new(cp,tp)*CFrame.Angles(math.rad(math.random(-15,25)),math.rad(math.random(-15,25)),0)
            else cam.CFrame=CFrame.new(cp,tp) end
            char.HumanoidRootPart.CFrame=CFrame.new(char.HumanoidRootPart.Position,cam.CFrame.Position+cam.CFrame.LookVector)
        end)
        task.delay(0.65,function() if aimlockConn then aimlockConn:Disconnect();aimlockConn=nil end end)
    end

    local function doKiba()
        local hrp=getHRP(); if not hrp then return end
        local target=getNearestLive(hrp.Position)
        if not(target and target:FindFirstChild("HumanoidRootPart")) then return end
        task.spawn(function()
            local tp=target.HumanoidRootPart.Position+Vector3.new(0,5,0)
            local t=TweenService:Create(hrp,TweenInfo.new(0.1,Enum.EasingStyle.Linear),{CFrame=CFrame.new(tp)})
            t:Play();t.Completed:Wait()
            local h=getHRP(); if not h then return end
            local att=Instance.new("Attachment");att.Parent=h
            local ap=Instance.new("AlignPosition"); ap.Mode=Enum.PositionAlignmentMode.OneAttachment
            ap.Attachment0=att;ap.Position=tp;ap.MaxForce=1e5;ap.MaxVelocity=1e4;ap.Responsiveness=200;ap.Parent=h
            task.delay(0.76,function() ap:Destroy();att:Destroy() end)
            fireRemote();startAimlock(target)
        end)
    end

    local function triggerToolRemote(tool)
        local r=getChar() and getChar():FindFirstChild("Communicate")
        if r then r:FireServer(unpack({{Tool=tool,Goal="Console Move"}})) end
    end
    local function doBackdash()
        VirtualInputManager:SendKeyEvent(true,Enum.KeyCode.S,false,game)
        task.spawn(function()
            VirtualInputManager:SendKeyEvent(true,Enum.KeyCode.Q,false,game)
            task.wait(0.05)
            VirtualInputManager:SendKeyEvent(false,Enum.KeyCode.Q,false,game)
        end)
        task.spawn(function()
            local bp=player:FindFirstChild("Backpack"); if not bp then return end
            for _,g in ipairs(toolGroups) do
                local tool=bp:FindFirstChild(g[bdashSlot])
                if tool then triggerToolRemote(tool);break end
            end
        end)
        task.delay(1,function() VirtualInputManager:SendKeyEvent(false,Enum.KeyCode.S,false,game) end)
    end

    -- Oreo helpers
    local function oreoNearestTorso()
        local char=getChar(); if not char then return nil end
        local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return nil end
        local best,mn=nil,10
        for _,m in ipairs(Workspace:GetChildren()) do
            if m:IsA("Model") and m~=char then
                local torso=m:FindFirstChild("Torso") or m:FindFirstChild("UpperTorso")
                if torso and (m:FindFirstChildWhichIsA("Humanoid") or m.Name=="Weakest Dummy") then
                    local d=(torso.Position-hrp.Position).Magnitude
                    if d<=mn then mn=d;best=torso end
                end
            end
        end; return best
    end
    local function oreoSwipeBack(char,cb)
        local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
        local cam=Workspace.CurrentCamera; local off=cam.CFrame.Position-hrp.Position
        local dir=Vector3.new(off.X,0,off.Z).Unit
        TweenService:Create(cam,TweenInfo.new(0.2),{CFrame=CFrame.new(hrp.Position-dir*off.Magnitude+Vector3.new(0,2,0),hrp.Position+Vector3.new(0,1.5,0))}):Play()
        if cb then task.delay(0.2,cb) end
    end
    local function oreoRotateRight(char)
        local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
        local cam=Workspace.CurrentCamera
        hrp.CFrame=hrp.CFrame*CFrame.Angles(0,math.rad(44.6),0)
        local dir=hrp.CFrame.LookVector; local dist=(cam.CFrame.Position-hrp.Position).Magnitude
        TweenService:Create(cam,TweenInfo.new(0.15),{CFrame=CFrame.new(hrp.Position-dir*dist+Vector3.new(0,2,0),hrp.Position+Vector3.new(0,1.5,0))}):Play()
        oreoCamLocked=true; task.delay(0.2,function() oreoCamLocked=false end)
    end

    RunService.Heartbeat:Connect(function()
        if not CS.oreo.enabled then return end
        local char=getChar(); if not char then return end
        local hum=char:FindFirstChildWhichIsA("Humanoid"); if not hum then return end
        local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
        for _,anim in pairs(hum:GetPlayingAnimationTracks()) do
            local id=anim.Animation and anim.Animation.AnimationId
            if OREO_ANIM1[id] and tick()-oreoLast1>1.2 then
                oreoLast1=tick()
                task.delay(0.1,function() local c2=getChar(); if c2 then oreoRotateRight(c2) end end)
            elseif OREO_ANIM2[id] and tick()-oreoLast2>1.5 then
                oreoLast2=tick()
                task.delay(0.3,function()
                    local c2=getChar(); if not c2 then return end
                    local h2=c2:FindFirstChild("HumanoidRootPart"); if not h2 then return end
                    local torso=oreoNearestTorso()
                    if torso then
                        local tp=torso.Position+Vector3.new(0,1.5,0)
                        TweenService:Create(h2,TweenInfo.new(0.25),{CFrame=CFrame.new(tp,tp+h2.CFrame.LookVector)}):Play()
                        task.wait(0.26)
                    end
                    h2.Velocity=Vector3.new(h2.Velocity.X,60,h2.Velocity.Z)
                    local rem=c2:FindFirstChild("Communicate")
                    if rem then pcall(function() rem:FireServer(unpack({{Dash=Enum.KeyCode.W,Key=Enum.KeyCode.Q,Goal="KeyPress"}})) end) end
                    VirtualInputManager:SendKeyEvent(true,Enum.KeyCode.W,false,game)
                    task.wait(0.23)
                    oreoSwipeBack(c2,function() task.wait(0.3); VirtualInputManager:SendKeyEvent(false,Enum.KeyCode.W,false,game) end)
                end)
            end
        end
    end)

    local function rebuildCombatConn(key)
        local s=CS[key]; if s.conn then s.conn:Disconnect();s.conn=nil end
        if not s.enabled then return end
        if key=="kiba" then
            s.conn=UserInputService.InputBegan:Connect(function(inp,gp)
                if gp or listeningFor or s.cd then return end
                if inp.KeyCode~=s.key then return end
                s.cd=true;doKiba();task.delay(4,function() s.cd=false end)
            end)
        elseif key=="edash" then
            s.conn=UserInputService.InputBegan:Connect(function(inp,gp)
                if gp or listeningFor then return end; if inp.KeyCode~=s.key then return end
                local char=getChar(); if not char then return end
                local root=char:FindFirstChild("HumanoidRootPart"); if not root then return end
                fireRemote()
                task.delay(0.3,function()
                    if not root or not root.Parent then return end
                    root.CFrame=CFrame.new(root.Position,root.Position+(-root.CFrame.LookVector))
                    local c; c=RunService.RenderStepped:Connect(function()
                        local t=getNearestPlayerRoot(root.Position)
                        if t then local p=root.Position;root.CFrame=CFrame.new(p,Vector3.new(t.Position.X,p.Y,t.Position.Z)) end
                    end); task.delay(1,function() if c then c:Disconnect() end end)
                end)
            end)
        elseif key=="lethal" then
            s.conn=UserInputService.InputBegan:Connect(function(inp,gp)
                if gp or listeningFor then return end; if inp.KeyCode~=s.key then return end
                local hrp=getHRP(); if not hrp then return end
                local cam=Workspace.CurrentCamera; local startCF=cam.CFrame; local prog,last=0,tick()
                local sc;sc=RunService.RenderStepped:Connect(function()
                    local now=tick(); prog=math.min(prog+(now-last)/0.2,1);last=now
                    cam.CFrame=startCF*CFrame.Angles(0,math.rad(360)*prog,0)
                    if prog>=1 then sc:Disconnect()
                        local cl=getClosestCombatPlayer(hrp.Position)
                        if cl and cl.Character then
                            local tgt=cl.Character:FindFirstChild("HumanoidRootPart")
                            if tgt then local lc;lc=RunService.RenderStepped:Connect(function()
                                if tgt and tgt.Parent then cam.CFrame=CFrame.new(cam.CFrame.Position,tgt.Position) end
                            end); task.delay(0.5,function() if lc then lc:Disconnect() end end) end
                        end
                    end
                end); fireRemote()
            end)
        elseif key=="bdash" then
            if not s.key then return end
            s.conn=UserInputService.InputBegan:Connect(function(inp,gp)
                if gp or listeningFor then return end; if inp.KeyCode~=s.key then return end
                doBackdash()
            end)
        end
    end

    local function setCE(key,v)
        CS[key].enabled=v; rebuildCombatConn(key)
        if not v and key=="kiba" and aimlockConn then aimlockConn:Disconnect();aimlockConn=nil end
    end
    player.CharacterAdded:Connect(function()
        for key,s in pairs(CS) do if s.enabled then rebuildCombatConn(key) end end
    end)

    -- ── Keybind overlay ───────────────────────────────────────────────
    local bindOverlay = Instance.new("Frame", screenGui)
    bindOverlay.Size=UDim2.new(1,0,1,0); bindOverlay.BackgroundColor3=Color3.new(0,0,0)
    bindOverlay.BackgroundTransparency=0.45; bindOverlay.BorderSizePixel=0; bindOverlay.ZIndex=60; bindOverlay.Visible=false

    local bindCard=Instance.new("Frame",bindOverlay)
    bindCard.Size=UDim2.new(0,240,0,90); bindCard.Position=UDim2.new(0.5,-120,0.5,-45)
    bindCard.BackgroundColor3=T.bg2; bindCard.BorderSizePixel=0; bindCard.Active=true; bindCard.ZIndex=61
    mkCorner(bindCard,14); mkStroke(bindCard,T.blue,1,0.1)

    local bTop=Instance.new("Frame",bindCard); bTop.Size=UDim2.new(1,0,0,2)
    bTop.BackgroundColor3=T.blue; bTop.BorderSizePixel=0; bTop.ZIndex=62
    mkGradient(bTop,T.blueFrost,T.blue,0)

    local bTitle=mkLabel(bindCard,"Press any key…",14,Enum.Font.GothamBlack,T.white,Enum.TextXAlignment.Center)
    bTitle.Size=UDim2.new(1,-20,0,36);bTitle.Position=UDim2.new(0,10,0,14);bTitle.ZIndex=62
    local bSub=mkLabel(bindCard,"ESC or click outside to cancel",10,Enum.Font.Gotham,T.grey3,Enum.TextXAlignment.Center)
    bSub.Size=UDim2.new(1,0,0,22);bSub.Position=UDim2.new(0,0,0,62);bSub.ZIndex=62

    local keyBtnRefs={}

    local function closeBind()
        if listeningFor and keyBtnRefs[listeningFor] then
            tw(keyBtnRefs[listeningFor],{TextColor3=T.blueLight})
        end
        listeningFor=nil; bindOverlay.Visible=false
    end

    UserInputService.InputBegan:Connect(function(inp,gp)
        if not listeningFor then return end
        if inp.UserInputType~=Enum.UserInputType.Keyboard then return end
        if inp.KeyCode==Enum.KeyCode.Escape then closeBind();return end
        local name=tostring(inp.KeyCode):gsub("Enum%.KeyCode%.","")
        CS[listeningFor].key=inp.KeyCode
        if keyBtnRefs[listeningFor] then keyBtnRefs[listeningFor].Text=name end
        rebuildCombatConn(listeningFor); closeBind()
    end)
    bindOverlay.InputBegan:Connect(function(inp)
        if inp.UserInputType==Enum.UserInputType.MouseButton1 then
            local pos=inp.Position;local abs=bindCard.AbsolutePosition;local sz=bindCard.AbsoluteSize
            if not(pos.X>=abs.X and pos.X<=abs.X+sz.X and pos.Y>=abs.Y and pos.Y<=abs.Y+sz.Y) then closeBind() end
        end
    end)

    -- ── Row builder for combat ────────────────────────────────────────
    mkSection(page,"AZULICIADO FEATURES",1)

    local defs = {
        {key="kiba",   label="KIBA",        sub="auto-tween gap-close",  defKey="Q"},
        {key="edash",  label="E-DASH",       sub="reverse + track",       defKey="E"},
        {key="lethal", label="LETHAL DASH",  sub="spin  +  cam lock",     defKey="F"},
    }

    for i,def in ipairs(defs) do
        local card = mkCard(page,54,i+1)
        mkStripe(card)

        local nl=mkLabel(card,def.label,12,Enum.Font.GothamBold,T.white)
        nl.Size=UDim2.new(0,110,0,18);nl.Position=UDim2.new(0,14,0,9);nl.ZIndex=14
        local sl=mkLabel(card,def.sub,10,Enum.Font.Gotham,T.grey3)
        sl.Size=UDim2.new(0,150,0,14);sl.Position=UDim2.new(0,14,0,29);sl.ZIndex=14

        -- key badge
        local kb=mkBtn(card,def.defKey,10,Enum.Font.GothamBold,T.blueLight)
        kb.Size=UDim2.new(0,44,0,24);kb.Position=UDim2.new(1,-112,0.5,-12)
        kb.BackgroundColor3=T.bg3;kb.BackgroundTransparency=0;kb.ZIndex=14;kb.AutoButtonColor=false
        mkCorner(kb,6);mkStroke(kb,T.blueDim,1,0.3)
        keyBtnRefs[def.key]=kb
        kb.MouseButton1Click:Connect(function()
            listeningFor=def.key; tw(kb,{TextColor3=T.white})
            bTitle.Text=def.label.."  —  press a key"; bindOverlay.Visible=true
        end)
        kb.MouseEnter:Connect(function() tw(kb,{BackgroundColor3=T.bg4}) end)
        kb.MouseLeave:Connect(function() tw(kb,{BackgroundColor3=T.bg3}) end)

        -- pill
        local pill=Instance.new("TextButton",card)
        pill.Size=UDim2.new(0,50,0,24);pill.Position=UDim2.new(1,-58,0.5,-12)
        pill.BackgroundColor3=T.offBg;pill.TextColor3=T.offText
        pill.Text="OFF";pill.Font=Enum.Font.GothamBold;pill.TextSize=10
        pill.AutoButtonColor=false;pill.ZIndex=14;mkCorner(pill,12);mkStroke(pill,T.grey1,1,0.4)
        local on=false
        local function refresh()
            if on then tw(pill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});pill.Text="ON"
                local s=pill:FindFirstChildOfClass("UIStroke");if s then tw(s,{Color=T.blue,Transparency=0}) end
            else tw(pill,{BackgroundColor3=T.offBg,TextColor3=T.offText});pill.Text="OFF"
                local s=pill:FindFirstChildOfClass("UIStroke");if s then tw(s,{Color=T.grey1,Transparency=0.4}) end
            end
        end
        pill.MouseButton1Click:Connect(function() on=not on;refresh();setCE(def.key,on) end)
    end

    -- Backdash row
    mkSection(page,"BACK DASH",5)
    do
        local card=mkCard(page,78,6); mkStripe(card)
        local nl=mkLabel(card,"BACK DASH",12,Enum.Font.GothamBold,T.white)
        nl.Size=UDim2.new(0,110,0,18);nl.Position=UDim2.new(0,14,0,8);nl.ZIndex=14
        local sl=mkLabel(card,"tool slot selector  (1–4)",10,Enum.Font.Gotham,T.grey3)
        sl.Size=UDim2.new(0,170,0,14);sl.Position=UDim2.new(0,14,0,26);sl.ZIndex=14

        local slotBtns={}
        for i=1,4 do
            local sb=Instance.new("TextButton",card)
            sb.Size=UDim2.new(0,26,0,22);sb.Position=UDim2.new(0,10+(i-1)*32,0,50)
            sb.Text=tostring(i);sb.Font=Enum.Font.GothamBold;sb.TextSize=11
            sb.AutoButtonColor=false;sb.ZIndex=14
            sb.BackgroundColor3=(i==1) and T.onBg or T.offBg
            sb.TextColor3=(i==1) and T.blueFrost or T.offText
            mkCorner(sb,5);slotBtns[i]=sb
            sb.MouseButton1Click:Connect(function()
                bdashSlot=i
                for j=1,4 do
                    tw(slotBtns[j],{BackgroundColor3=(j==i) and T.onBg or T.offBg,TextColor3=(j==i) and T.blueFrost or T.offText})
                end
            end)
        end

        local kb=mkBtn(card,"---",10,Enum.Font.GothamBold,T.blueLight)
        kb.Size=UDim2.new(0,44,0,24);kb.Position=UDim2.new(1,-112,0,8)
        kb.BackgroundColor3=T.bg3;kb.BackgroundTransparency=0;kb.ZIndex=14;kb.AutoButtonColor=false
        mkCorner(kb,6);mkStroke(kb,T.blueDim,1,0.3)
        keyBtnRefs["bdash"]=kb
        kb.MouseButton1Click:Connect(function()
            listeningFor="bdash";tw(kb,{TextColor3=T.white})
            bTitle.Text="BACK DASH  —  press a key";bindOverlay.Visible=true
        end)
        kb.MouseEnter:Connect(function() tw(kb,{BackgroundColor3=T.bg4}) end)
        kb.MouseLeave:Connect(function() tw(kb,{BackgroundColor3=T.bg3}) end)

        local pill=Instance.new("TextButton",card)
        pill.Size=UDim2.new(0,50,0,24);pill.Position=UDim2.new(1,-58,0,8)
        pill.BackgroundColor3=T.offBg;pill.TextColor3=T.offText
        pill.Text="OFF";pill.Font=Enum.Font.GothamBold;pill.TextSize=10
        pill.AutoButtonColor=false;pill.ZIndex=14;mkCorner(pill,12);mkStroke(pill,T.grey1,1,0.4)
        local on=false
        local function refresh()
            if on then tw(pill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});pill.Text="ON"
            else tw(pill,{BackgroundColor3=T.offBg,TextColor3=T.offText});pill.Text="OFF" end
        end
        pill.MouseButton1Click:Connect(function() on=not on;refresh();setCE("bdash",on) end)
    end

    -- Oreo row
    mkSection(page,"OREO",7)
    do
        local card=mkCard(page,52,8); mkStripe(card)
        local nl=mkLabel(card,"OREO",12,Enum.Font.GothamBold,T.white)
        nl.Size=UDim2.new(0,100,0,18);nl.Position=UDim2.new(0,14,0,9);nl.ZIndex=14
        local sl=mkLabel(card,"animation-triggered  ·  no keybind",10,Enum.Font.Gotham,T.grey3)
        sl.Size=UDim2.new(1,-80,0,14);sl.Position=UDim2.new(0,14,0,29);sl.ZIndex=14

        local pill=Instance.new("TextButton",card)
        pill.Size=UDim2.new(0,50,0,24);pill.Position=UDim2.new(1,-62,0.5,-12)
        pill.BackgroundColor3=T.offBg;pill.TextColor3=T.offText
        pill.Text="OFF";pill.Font=Enum.Font.GothamBold;pill.TextSize=10
        pill.AutoButtonColor=false;pill.ZIndex=14;mkCorner(pill,12);mkStroke(pill,T.grey1,1,0.4)
        local on=false
        local function refresh()
            if on then tw(pill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});pill.Text="ON"
            else tw(pill,{BackgroundColor3=T.offBg,TextColor3=T.offText});pill.Text="OFF" end
        end
        pill.MouseButton1Click:Connect(function() on=not on;refresh();setCE("oreo",on) end)
    end
end

-- ════════════════════════════════════════════════════════════════════
--  PAGE 3 · LOCK
-- ════════════════════════════════════════════════════════════════════
do
    local page = tabPages[3]

    local lockEnabled=false; local lockMode="Closest"; local axisMode="Both"
    local viewMode="Both"; local stickyMode=false; local lockedTarget=nil
    local lockSearchText=""; local CAM_X_OFFSET=1.2; local lockConn=nil

    local function getClosestLT()
        local best,dist=nil,math.huge
        for _,p in pairs(Players:GetPlayers()) do
            if p~=player and p.Character then
                local r=p.Character:FindFirstChild("HumanoidRootPart")
                local h=p.Character:FindFirstChildOfClass("Humanoid")
                if r and h and h.Health>0 then
                    local m=(camera.CFrame.Position-r.Position).Magnitude
                    if m<dist then dist=m;best=p.Character end
                end
            end
        end; return best
    end
    local function getLTByName(name)
        if not name or name=="" then return nil end
        local s=string.lower(name)
        for _,p in pairs(Players:GetPlayers()) do
            if p~=player and p.Character then
                if string.find(string.lower(p.Name),s,1,true) then
                    local r=p.Character:FindFirstChild("HumanoidRootPart")
                    local h=p.Character:FindFirstChildOfClass("Humanoid")
                    if r and h and h.Health>0 then return p.Character end
                end
            end
        end; return nil
    end
    local function applyLock(tPos)
        local cf=camera.CFrame;local cp=cf.Position;local rd=tPos-cp
        if rd.Magnitude<0.01 then return end
        local fwd=rd.Unit;local up=Vector3.new(0,1,0);local cm=fwd:Cross(up).Magnitude
        local right=cm<0.001 and cf.RightVector or fwd:Cross(up)/cm
        local aimPos=tPos+right*CAM_X_OFFSET*(rd.Magnitude/50)
        if (aimPos-cp).Magnitude<0.01 then return end
        camera.CFrame=CFrame.new(cp,aimPos)
    end
    local function lockOn(tc)
        if not tc then return end
        local tp=tc:FindFirstChild("HumanoidRootPart");if not tp then return end
        local char=player.Character;if not char then return end
        local tPos=tp.Position
        if viewMode=="Camera" or viewMode=="Both" then
            local cp=camera.CFrame.Position;local fp=tPos
            if axisMode=="X" then fp=Vector3.new(tPos.X,cp.Y,tPos.Z)
            elseif axisMode=="Y" then fp=Vector3.new(cp.X,tPos.Y,cp.Z) end
            applyLock(fp)
        end
        if viewMode=="Body" or viewMode=="Both" then
            local root=char:FindFirstChild("HumanoidRootPart")
            if root then
                local btp=tPos
                if axisMode=="X" then btp=Vector3.new(tPos.X,root.Position.Y,tPos.Z)
                elseif axisMode=="Y" then btp=Vector3.new(root.Position.X,tPos.Y,root.Position.Z) end
                root.CFrame=CFrame.new(root.Position,btp)
            end
        end
    end

    -- Status card
    mkSection(page,"LOCK STATUS",1)
    local stCard=mkCard(page,60,2); mkStripe(stCard)
    local stMain=mkLabel(stCard,"◎  LOCK  —  OFF",13,Enum.Font.GothamBlack,T.red)
    stMain.Size=UDim2.new(1,-70,0,20);stMain.Position=UDim2.new(0,14,0,8);stMain.ZIndex=14
    local stSub=mkLabel(stCard,"Target: none",10,Enum.Font.Gotham,T.grey3)
    stSub.Size=UDim2.new(1,-70,0,14);stSub.Position=UDim2.new(0,14,0,30);stSub.ZIndex=14
    local stKey=mkLabel(stCard,"E = toggle  ·  R = behind  ·  T = up",9,Enum.Font.Gotham,T.grey2)
    stKey.Size=UDim2.new(1,-70,0,12);stKey.Position=UDim2.new(0,14,0,46);stKey.ZIndex=14

    local function toggleLock(on)
        lockEnabled=on
        if on then
            stMain.Text="◉  LOCK  —  ACTIVE";stMain.TextColor3=T.green
            lockedTarget=lockMode=="Closest" and getClosestLT() or getLTByName(lockSearchText)
            if lockConn then lockConn:Disconnect() end
            lockConn=RunService.RenderStepped:Connect(function()
                if not lockEnabled then return end
                if not lockedTarget or not lockedTarget:IsDescendantOf(Workspace) then
                    lockedTarget=lockMode=="Closest" and getClosestLT() or getLTByName(lockSearchText)
                end
                if lockedTarget then
                    stSub.Text="Target: "..lockedTarget.Name
                    if not stickyMode then
                        lockedTarget=lockMode=="Closest" and getClosestLT() or getLTByName(lockSearchText)
                    end
                    lockOn(lockedTarget)
                end
            end)
        else
            stMain.Text="◎  LOCK  —  OFF";stMain.TextColor3=T.red
            lockedTarget=nil;stSub.Text="Target: none"
            if lockConn then lockConn:Disconnect();lockConn=nil end
        end
    end

    local enablePill=Instance.new("TextButton",stCard)
    enablePill.Size=UDim2.new(0,50,0,24);enablePill.Position=UDim2.new(1,-60,0.5,-12)
    enablePill.BackgroundColor3=T.offBg;enablePill.TextColor3=T.offText
    enablePill.Text="OFF";enablePill.Font=Enum.Font.GothamBold;enablePill.TextSize=10
    enablePill.AutoButtonColor=false;enablePill.ZIndex=14;mkCorner(enablePill,12);mkStroke(enablePill,T.grey1,1,0.4)
    local lockPillOn=false
    enablePill.MouseButton1Click:Connect(function()
        lockPillOn=not lockPillOn
        if lockPillOn then tw(enablePill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});enablePill.Text="ON"
        else tw(enablePill,{BackgroundColor3=T.offBg,TextColor3=T.offText});enablePill.Text="OFF" end
        toggleLock(lockPillOn)
    end)

    UserInputService.InputBegan:Connect(function(inp,gp)
        if gp then return end
        if inp.KeyCode==Enum.KeyCode.E then
            lockPillOn=not lockPillOn
            if lockPillOn then tw(enablePill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});enablePill.Text="ON"
            else tw(enablePill,{BackgroundColor3=T.offBg,TextColor3=T.offText});enablePill.Text="OFF" end
            toggleLock(lockPillOn)
        elseif inp.KeyCode==Enum.KeyCode.R then
            if lockedTarget and lockedTarget:FindFirstChild("HumanoidRootPart") then
                local char=player.Character
                if char and char:FindFirstChild("HumanoidRootPart") then
                    local tr=lockedTarget.HumanoidRootPart
                    char.HumanoidRootPart.CFrame=CFrame.new(tr.Position-tr.CFrame.LookVector*5,tr.Position)
                end
            end
        elseif inp.KeyCode==Enum.KeyCode.T then
            local char=player.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame+Vector3.new(0,100,0)
            end
        end
    end)

    -- Mode
    mkSection(page,"LOCK MODE",3)
    local modeCard=mkCard(page,38,4); mkStripe(modeCard)
    local mBtns={}
    local function mkModeBtn(label,xOff,cb)
        local b=mkBtn(modeCard,label,10,Enum.Font.GothamBold,T.grey3)
        b.Size=UDim2.new(0,78,0,26);b.Position=UDim2.new(0,xOff,0.5,-13)
        b.BackgroundColor3=T.bg3;b.BackgroundTransparency=0;b.ZIndex=14;b.AutoButtonColor=false
        mkCorner(b,7);mkStroke(b,T.grey1,1,0.4)
        b.MouseButton1Click:Connect(function() cb(); for _,mb in ipairs(mBtns) do tw(mb,{TextColor3=T.grey3,BackgroundColor3=T.bg3}) end;tw(b,{TextColor3=T.blueFrost,BackgroundColor3=T.bg4}) end)
        b.MouseEnter:Connect(function() tw(b,{BackgroundColor3=T.bg4}) end)
        b.MouseLeave:Connect(function() if b.TextColor3~=T.blueFrost then tw(b,{BackgroundColor3=T.bg3}) end end)
        table.insert(mBtns,b); return b
    end
    local bClose=mkModeBtn("◉ Closest",  8, function() lockMode="Closest" end)
    local bUser =mkModeBtn("◎ Username",94, function() lockMode="Username" end)
    local bStick=mkModeBtn("⬡ Sticky", 180, function()
        stickyMode=not stickyMode
        bStick.Text=stickyMode and "⬡ Sticky ON" or "⬡ Sticky"
    end)
    tw(bClose,{TextColor3=T.blueFrost,BackgroundColor3=T.bg4})

    -- Search
    mkSection(page,"USERNAME SEARCH",5)
    local searchBox=Instance.new("TextBox",page)
    searchBox.Size=UDim2.new(1,0,0,36);searchBox.BackgroundColor3=T.bg2;searchBox.BorderSizePixel=0
    searchBox.PlaceholderText="Enter exact username…";searchBox.PlaceholderColor3=T.grey2
    searchBox.Text="";searchBox.TextColor3=T.white;searchBox.TextSize=12;searchBox.Font=Enum.Font.Gotham
    searchBox.ClearTextOnFocus=false;searchBox.LayoutOrder=6;searchBox.ZIndex=13
    mkCorner(searchBox,8);mkStroke(searchBox,T.grey1,1,0.3)
    local sbPad=Instance.new("UIPadding",searchBox);sbPad.PaddingLeft=UDim.new(0,12)
    searchBox.FocusLost:Connect(function() lockSearchText=searchBox.Text end)

    -- View / Axis mode
    local function mkModeGroup(page, title, order, options, onChange)
        mkSection(page,title,order)
        local card=mkCard(page,38,order+1); mkStripe(card)
        local btns={}
        for i,opt in ipairs(options) do
            local b=mkBtn(card,opt.label,10,Enum.Font.GothamBold,T.grey3)
            b.Size=UDim2.new(0,82,0,26);b.Position=UDim2.new(0,6+(i-1)*88,0.5,-13)
            b.BackgroundColor3=T.bg3;b.BackgroundTransparency=0;b.ZIndex=14;b.AutoButtonColor=false
            mkCorner(b,7);mkStroke(b,T.grey1,1,0.4)
            b.MouseButton1Click:Connect(function()
                onChange(opt.val)
                for _,bb in ipairs(btns) do tw(bb,{TextColor3=T.grey3,BackgroundColor3=T.bg3}) end
                tw(b,{TextColor3=T.blueFrost,BackgroundColor3=T.bg4})
            end)
            b.MouseEnter:Connect(function() tw(b,{BackgroundColor3=T.bg4}) end)
            b.MouseLeave:Connect(function() if b.TextColor3~=T.blueFrost then tw(b,{BackgroundColor3=T.bg3}) end end)
            table.insert(btns,b)
            if i==3 then tw(b,{TextColor3=T.blueFrost,BackgroundColor3=T.bg4}) end  -- default to "Both"
        end
        return card,btns
    end

    mkModeGroup(page,"VIEW MODE",7,
        {{label="Camera",val="Camera"},{label="Body",val="Body"},{label="Both",val="Both"}},
        function(v) viewMode=v end)
    mkModeGroup(page,"AXIS MODE",9,
        {{label="X Axis",val="X"},{label="Y Axis",val="Y"},{label="Both",val="Both"}},
        function(v) axisMode=v end)

    -- Utilities
    mkSection(page,"UTILITIES",11)
    local utCard=mkCard(page,38,12); mkStripe(utCard)
    local function utBtn(label,xOff,cb)
        local b=mkBtn(utCard,label,10,Enum.Font.GothamBold,T.grey4)
        b.Size=UDim2.new(0,138,0,26);b.Position=UDim2.new(0,xOff,0.5,-13)
        b.BackgroundColor3=T.bg3;b.BackgroundTransparency=0;b.ZIndex=14;b.AutoButtonColor=false
        mkCorner(b,7);mkStroke(b,T.blueDim,1,0.3)
        b.MouseButton1Click:Connect(cb)
        b.MouseEnter:Connect(function() tw(b,{BackgroundColor3=T.bg4,TextColor3=T.white}) end)
        b.MouseLeave:Connect(function() tw(b,{BackgroundColor3=T.bg3,TextColor3=T.grey4}) end)
    end
    utBtn("⬆ Teleport Up",6,function()
        local char=player.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame+Vector3.new(0,100,0)
        end
    end)
    utBtn("⬅ Teleport Behind",150,function()
        if lockedTarget and lockedTarget:FindFirstChild("HumanoidRootPart") then
            local char=player.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                local tr=lockedTarget.HumanoidRootPart
                char.HumanoidRootPart.CFrame=CFrame.new(tr.Position-tr.CFrame.LookVector*5,tr.Position)
            end
        end
    end)
end

-- ════════════════════════════════════════════════════════════════════
--  PAGE 4 · ANTI-LAG
-- ════════════════════════════════════════════════════════════════════
do
    local page = tabPages[4]
    local gcConn = nil

    local function mkLagRow(pg, title, sub, order, onToggle)
        local card=mkCard(pg,52,order); mkStripe(card)
        local nl=mkLabel(card,title,12,Enum.Font.GothamBold,T.white)
        nl.Size=UDim2.new(1,-80,0,18);nl.Position=UDim2.new(0,14,0,8);nl.ZIndex=14
        local sl=mkLabel(card,sub,10,Enum.Font.Gotham,T.grey3)
        sl.Size=UDim2.new(1,-80,0,14);sl.Position=UDim2.new(0,14,0,28);sl.ZIndex=14
        local pill=Instance.new("TextButton",card)
        pill.Size=UDim2.new(0,50,0,24);pill.Position=UDim2.new(1,-60,0.5,-12)
        pill.BackgroundColor3=T.offBg;pill.TextColor3=T.offText;pill.Text="OFF"
        pill.Font=Enum.Font.GothamBold;pill.TextSize=10;pill.AutoButtonColor=false;pill.ZIndex=14
        mkCorner(pill,12);mkStroke(pill,T.grey1,1,0.4)
        local on=false
        local function refresh()
            if on then tw(pill,{BackgroundColor3=T.onBg,TextColor3=T.blueFrost});pill.Text="ON"
                local s=pill:FindFirstChildOfClass("UIStroke");if s then tw(s,{Color=T.blue,Transparency=0}) end
            else tw(pill,{BackgroundColor3=T.offBg,TextColor3=T.offText});pill.Text="OFF"
                local s=pill:FindFirstChildOfClass("UIStroke");if s then tw(s,{Color=T.grey1,Transparency=0.4}) end
            end
            if onToggle then onToggle(on) end
        end
        pill.MouseButton1Click:Connect(function() on=not on;refresh() end)
        return card
    end

    mkSection(page,"GRAPHICS",1)
    mkLagRow(page,"Disable Shadows",    "Removes global shadows",     2, function(on) pcall(function() Lighting.GlobalShadows=not on end) end)
    mkLagRow(page,"Extend Fog",         "Pushes fog end to 100k",     3, function(on) pcall(function() Lighting.FogEnd=on and 100000 or 10000 end) end)
    mkLagRow(page,"Force Quality Lvl 1","Lowest graphics preset",      4, function(on)
        if on then pcall(function() UserSettings():GetService("UserGameSettings").SavedQualityLevel=Enum.SavedQualitySetting.QualityLevel1 end) end
    end)

    mkSection(page,"PARTICLES & MESH",5)
    mkLagRow(page,"Throttle Particles", "Caps emitter rate to 20",    6, function(on)
        if on then pcall(function()
            for _,v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("ParticleEmitter") then v.Rate=math.min(v.Rate,20) end
            end
        end) end
    end)
    mkLagRow(page,"Disable Mesh Heads", "Disables mesh accessories",  7, function(on)
        pcall(function()
            Workspace.MeshPartHeadsAndAccessories=on and Enum.MeshPartHeadsAndAccessories.Disabled or Enum.MeshPartHeadsAndAccessories.Enabled
        end)
    end)

    mkSection(page,"MEMORY",8)
    mkLagRow(page,"Auto GC  (30s)",     "Runs garbage collector",     9, function(on)
        if gcConn then gcConn:Disconnect();gcConn=nil end
        if on then
            local last=0; gcConn=RunService.Heartbeat:Connect(function()
                local now=tick(); if now-last>30 then last=now; if gcinfo()>50000 then collectgarbage("collect") end end
            end)
        end
    end)

    -- Anti-Lag V2 loader
    mkSection(page,"ANTI-LAG V2  ·  External Loader",10)
    local loaderCard=mkCard(page,88,11); mkStripe(loaderCard)

    local ll1=mkLabel(loaderCard,"Anti-Lag V2",13,Enum.Font.GothamBold,T.white)
    ll1.Size=UDim2.new(1,-80,0,18);ll1.Position=UDim2.new(0,14,0,8);ll1.ZIndex=14
    local ll2=mkLabel(loaderCard,"by ItsLouisPlayz  ·  removes grass,\ntrees and walls for better FPS",10,Enum.Font.Gotham,T.grey3)
    ll2.Size=UDim2.new(1,-80,0,28);ll2.Position=UDim2.new(0,14,0,28);ll2.ZIndex=14

    -- Object toggles
    local flagLabels = {"Grass","Trees","Walls"}
    local flagKeys   = {"Remove_Grass","Remove_Trees","Remove_Walls"}
    local flagDefaults = {true, true, false}
    for i,lbl in ipairs(flagLabels) do
        local fb=mkBtn(loaderCard,lbl,10,Enum.Font.GothamBold,flagDefaults[i] and T.blueFrost or T.offText)
        fb.Size=UDim2.new(0,54,0,20);fb.Position=UDim2.new(0,10+(i-1)*62,0,66)
        fb.BackgroundColor3=flagDefaults[i] and T.onBg or T.offBg;fb.BackgroundTransparency=0
        fb.ZIndex=14;fb.AutoButtonColor=false;mkCorner(fb,5)
        local state=flagDefaults[i]
        if flagKeys[i]=="Remove_Grass"  then Remove_Grass=state end
        if flagKeys[i]=="Remove_Trees"  then Remove_Trees=state end
        if flagKeys[i]=="Remove_Walls"  then Remove_Walls=state end
        fb.MouseButton1Click:Connect(function()
            state=not state
            tw(fb,{BackgroundColor3=state and T.onBg or T.offBg,TextColor3=state and T.blueFrost or T.offText})
            if flagKeys[i]=="Remove_Grass" then Remove_Grass=state end
            if flagKeys[i]=="Remove_Trees" then Remove_Trees=state end
            if flagKeys[i]=="Remove_Walls" then Remove_Walls=state end
        end)
    end

    -- LOAD button
    local loadBtn=mkBtn(loaderCard,"LOAD",11,Enum.Font.GothamBlack,T.white)
    loadBtn.Size=UDim2.new(0,56,0,28);loadBtn.Position=UDim2.new(1,-66,0,8)
    loadBtn.BackgroundColor3=T.blueDim;loadBtn.BackgroundTransparency=0;loadBtn.ZIndex=14;loadBtn.AutoButtonColor=false
    mkCorner(loadBtn,8);mkStroke(loadBtn,T.blue,1,0.2)

    local loadedFlag=false
    loadBtn.MouseButton1Click:Connect(function()
        if loadedFlag then return end; loadedFlag=true
        loadBtn.Text="…";loadBtn.TextColor3=T.grey3
        task.spawn(function()
            local ok,err=pcall(function()
                Remove_Grass=Remove_Grass~=nil and Remove_Grass or true
                Remove_Trees=Remove_Trees~=nil and Remove_Trees or true
                Remove_Walls=Remove_Walls~=nil and Remove_Walls or false
                loadstring(game:HttpGet("https://raw.githubusercontent.com/louismich4el/ItsLouisPlayz-Scripts/refs/heads/main/Anti%20Lag%20V2.lua"))()
            end)
            if ok then
                loadBtn.Text="✓";loadBtn.TextColor3=T.green
                tw(loadBtn,{BackgroundColor3=Color3.fromRGB(0,60,30)})
            else
                loadedFlag=false;loadBtn.Text="LOAD";loadBtn.TextColor3=T.white
                tw(loadBtn,{BackgroundColor3=T.blueDim})
                warn("[AzuliciadoGUI] Anti-Lag load error:",err)
            end
        end)
    end)
    loadBtn.MouseEnter:Connect(function() if not loadedFlag then tw(loadBtn,{BackgroundColor3=T.blue}) end end)
    loadBtn.MouseLeave:Connect(function() if not loadedFlag then tw(loadBtn,{BackgroundColor3=T.blueDim}) end end)
end

-- ════════════════════════════════════════════════════════════════════
--  PANEL DRAG  (drag by header)
-- ════════════════════════════════════════════════════════════════════
do
    local dragging,dragInput,dragStart,startPos=false,nil,nil,nil
    header.InputBegan:Connect(function(inp)
        if inp.UserInputType==Enum.UserInputType.MouseButton1 then
            dragging=true;dragStart=inp.Position;startPos=panel.Position
            inp.Changed:Connect(function() if inp.UserInputState==Enum.UserInputState.End then dragging=false end end)
        end
    end)
    header.InputChanged:Connect(function(inp)
        if inp.UserInputType==Enum.UserInputType.MouseMovement then dragInput=inp end
    end)
    UserInputService.InputChanged:Connect(function(inp)
        if inp==dragInput and dragging and dragStart and startPos then
            local d=inp.Position-dragStart
            panel.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y)
        end
    end)
end

-- ════════════════════════════════════════════════════════════════════
--  FLOATING TOGGLE BUTTON
-- ════════════════════════════════════════════════════════════════════
local toggleBtn=Instance.new("TextButton",screenGui)
toggleBtn.Size=UDim2.new(0,42,0,42);toggleBtn.Position=UDim2.new(0,28,0.5,-21)
toggleBtn.BackgroundColor3=T.bg2;toggleBtn.TextColor3=T.blue
toggleBtn.Text="◈";toggleBtn.Font=Enum.Font.GothamBlack;toggleBtn.TextSize=20
toggleBtn.AutoButtonColor=false;toggleBtn.ZIndex=50
mkCorner(toggleBtn,10);mkStroke(toggleBtn,T.grey1,1,0.2)
mkGradient(toggleBtn,T.bg3,T.bg1,90)

toggleBtn.MouseButton1Click:Connect(function()
    panel.Visible=not panel.Visible
    tw(toggleBtn,{TextColor3=panel.Visible and T.blueFrost or T.blue})
end)
toggleBtn.MouseEnter:Connect(function() tw(toggleBtn,{BackgroundColor3=T.bg4,TextColor3=T.blueFrost}) end)
toggleBtn.MouseLeave:Connect(function() tw(toggleBtn,{BackgroundColor3=T.bg2,TextColor3=panel.Visible and T.blueFrost or T.blue}) end)

-- ════════════════════════════════════════════════════════════════════
--  INTRO SPLASH  (made by Azuliciado)
-- ════════════════════════════════════════════════════════════════════
task.spawn(function()
    -- Small "made by" toast — slides in from bottom, lingers, fades out
    local toast = Instance.new("Frame", screenGui)
    toast.Size = UDim2.new(0, 220, 0, 44)
    toast.Position = UDim2.new(0.5, -110, 1, 10)   -- starts just below screen
    toast.BackgroundColor3 = T.bg2
    toast.BackgroundTransparency = 0
    toast.BorderSizePixel = 0
    toast.ZIndex = 200
    mkCorner(toast, 12)
    mkStroke(toast, T.grey1, 1, 0.2)

    -- top accent line
    local ta = Instance.new("Frame", toast)
    ta.Size = UDim2.new(1, 0, 0, 2); ta.BackgroundColor3 = T.blue
    ta.BorderSizePixel = 0; ta.ZIndex = 201
    mkGradient(ta, T.blueFrost, T.blue, 0); mkCorner(ta, 2)

    local icon = mkLabel(toast, "◈", 15, Enum.Font.GothamBlack, T.blue, Enum.TextXAlignment.Left)
    icon.Size = UDim2.new(0, 20, 1, -6); icon.Position = UDim2.new(0, 12, 0, 3); icon.ZIndex = 201

    local line1 = mkLabel(toast, "made by  Azuliciado", 12, Enum.Font.GothamBold, T.white, Enum.TextXAlignment.Left)
    line1.Size = UDim2.new(1, -44, 0, 16); line1.Position = UDim2.new(0, 36, 0, 8); line1.ZIndex = 201

    local line2 = mkLabel(toast, "combat utility  ·  v3", 9, Enum.Font.Gotham, T.grey3, Enum.TextXAlignment.Left)
    line2.Size = UDim2.new(1, -44, 0, 13); line2.Position = UDim2.new(0, 36, 0, 24); line2.ZIndex = 201

    -- Slide up
    TweenService:Create(toast,
        TweenInfo.new(0.45, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
        {Position = UDim2.new(0.5, -110, 1, -58)}
    ):Play()

    task.wait(2.2)

    -- Fade + slide out
    TweenService:Create(toast,
        TweenInfo.new(0.35, Enum.EasingStyle.Quint, Enum.EasingDirection.In),
        {Position = UDim2.new(0.5, -110, 1, 10), BackgroundTransparency = 1}
    ):Play()
    for _, obj in ipairs({icon, line1, line2}) do
        TweenService:Create(obj,
            TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.In),
            {TextTransparency = 1}
        ):Play()
    end
    TweenService:Create(ta,
        TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.In),
        {BackgroundTransparency = 1}
    ):Play()

    task.wait(0.4)
    toast:Destroy()
end)

-- ════════════════════════════════════════════════════════════════════
--  BOOT
-- ════════════════════════════════════════════════════════════════════
switchTab(1)
panel.Visible=true
print("◈  AZULICIADO v3  ·  made by Azuliciado  ·  loaded ✓")

--[[
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  DEATH COUNTER LABEL  →  keep in  ServerScriptService  as a Script

  It must run server-side so BillboardGuis are visible to every
  client.  It cannot be merged into this LocalScript.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
]]
