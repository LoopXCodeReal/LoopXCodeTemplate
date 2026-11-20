--[[
    ====================================================================
    LOOPXCODE UI LIBRARY — FULL DARK MODE & ALL-DRAGGABLE ROBLOX LUAU
    Fitur Utama:
      1. Tema Hitam Obsidian / Dark Mode Modern
      2. FPS & Ping HUD Pill -> BISA DIGESER BEBAS (Draggable)
      3. Quick Bar 1 (Expandable + Garis Putih Bawah) -> BISA DIGESER BEBAS
      4. Floating Logo LoopXCode -> BISA DIGESER & KLIK UNTUK MINIMIZE/OPEN
      5. Main Window 3-Kolom LoopXCode -> BISA DIGESER LEWAT HEADER
    ====================================================================
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Stats = game:GetService("Stats")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer

-- Safe GUI Parent (Mendukung Executor gethui / CoreGui / PlayerGui)
local function GetSafeGuiParent()
    local ok, res = pcall(function()
        return gethui and gethui() or CoreGui
    end)
    if ok and res then return res end
    return LocalPlayer:WaitForChild("PlayerGui")
end

local ParentGui = GetSafeGuiParent()
if ParentGui:FindFirstChild("LoopXCodeUI") then
    ParentGui.LoopXCodeUI:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LoopXCodeUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = ParentGui

-- Helper: Border Stroke
local function AddStroke(obj, thickness, color, transparency)
    local s = Instance.new("UIStroke")
    s.Thickness = thickness or 1.5
    s.Color = color or Color3.fromRGB(39, 39, 42)
    s.Transparency = transparency or 0
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    s.Parent = obj
    return s
end

-- Helper: Text Outline Stroke
local function AddTextStroke(txt, thickness)
    local s = Instance.new("UIStroke")
    s.Thickness = thickness or 1.4
    s.Color = Color3.fromRGB(0, 0, 0)
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Contextual
    s.Parent = txt
    return s
end

-- =====================================================================
-- UNIVERSAL DRAG SYSTEM (Smooth Drag + Deteksi Klik vs Geser)
-- =====================================================================
local function MakeDraggable(dragHandle, targetFrame, onClickCallback)
    local dragging = false
    local dragStart, startPos
    local movedDistance = 0

    dragHandle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = targetFrame.Position
            movedDistance = 0

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                    -- Jika gerakan kurang dari 6px, anggap sebagai KLIK biasa
                    if movedDistance < 6 and onClickCallback then
                        onClickCallback()
                    end
                end
            end)
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            movedDistance = delta.Magnitude
            targetFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
end

-- =====================================================================
-- 1. FPS & PING HUD PILL (BISA DIGESER BEBAS)
-- =====================================================================
local StatsPill = Instance.new("Frame")
StatsPill.Name = "LoopXCode_FPS_Ping"
StatsPill.Size = UDim2.new(0, 175, 0, 36)
StatsPill.Position = UDim2.new(0, 18, 0, 14)
StatsPill.BackgroundColor3 = Color3.fromRGB(16, 16, 20)
StatsPill.BackgroundTransparency = 0.1
StatsPill.Parent = ScreenGui
Instance.new("UICorner", StatsPill).CornerRadius = UDim.new(1, 0)
AddStroke(StatsPill, 1.5, Color3.fromRGB(63, 63, 70), 0)

local StatsLabel = Instance.new("TextLabel")
StatsLabel.Size = UDim2.new(1, 0, 1, 0)
StatsLabel.BackgroundTransparency = 1
StatsLabel.RichText = true
StatsLabel.Font = Enum.Font.GothamBold
StatsLabel.TextSize = 14
StatsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatsLabel.Text = '<font color="#FACC15">74</font> <font color="#A1A1AA">FPS</font>  |  <font color="#FACC15">95</font> <font color="#A1A1AA">ms</font>'
StatsLabel.Parent = StatsPill

-- Aktifkan Drag pada FPS & Ping Pill
MakeDraggable(StatsPill, StatsPill)

-- Update FPS & Ping secara real-time
local frames, lastTick = 0, tick()
RunService.RenderStepped:Connect(function()
    frames += 1
    if tick() - lastTick >= 0.5 then
        local fps = math.floor(frames / (tick() - lastTick))
        local ping = 60
        pcall(function()
            ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        end)
        StatsLabel.Text = string.format(
            '<font color="#FACC15">%d</font> <font color="#A1A1AA">FPS</font>  |  <font color="#FACC15">%d</font> <font color="#A1A1AA">ms</font>',
            fps, ping
        )
        frames = 0
        lastTick = tick()
    end
end)

-- =====================================================================
-- 2. QUICK BAR 1 (BISA DIGESER BEBAS + EXPANDABLE)
-- =====================================================================
local QuickBarFrame = Instance.new("Frame")
QuickBarFrame.Name = "LoopXCode_QuickBar1"
QuickBarFrame.Size = UDim2.new(0, 205, 0, 36)
QuickBarFrame.Position = UDim2.new(0, 205, 0, 14)
QuickBarFrame.BackgroundColor3 = Color3.fromRGB(14, 14, 18)
QuickBarFrame.BackgroundTransparency = 0.08
QuickBarFrame.Parent = ScreenGui
Instance.new("UICorner", QuickBarFrame).CornerRadius = UDim.new(0, 6)
AddStroke(QuickBarFrame, 1.5, Color3.fromRGB(63, 63, 70), 0)

local QuickBarTitle = Instance.new("TextLabel")
QuickBarTitle.Size = UDim2.new(1, -34, 1, -3)
QuickBarTitle.Position = UDim2.new(0, 12, 0, 0)
QuickBarTitle.BackgroundTransparency = 1
QuickBarTitle.Font = Enum.Font.FredokaOne
QuickBarTitle.TextSize = 15
QuickBarTitle.TextXAlignment = Enum.TextXAlignment.Left
QuickBarTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
QuickBarTitle.Text = "Quick Bar 1"
QuickBarTitle.Parent = QuickBarFrame
AddTextStroke(QuickBarTitle, 1.4)

local QuickChevron = Instance.new("TextLabel")
QuickChevron.Size = UDim2.new(0, 24, 1, -3)
QuickChevron.Position = UDim2.new(1, -28, 0, 0)
QuickChevron.BackgroundTransparency = 1
QuickChevron.Font = Enum.Font.GothamBold
QuickChevron.TextSize = 14
QuickChevron.TextColor3 = Color3.fromRGB(255, 255, 255)
QuickChevron.Text = "^"
QuickChevron.Parent = QuickBarFrame

-- Garis Putih Tebal di Bawah Quick Bar 1
local WhiteAccentLine = Instance.new("Frame")
WhiteAccentLine.Size = UDim2.new(1, 0, 0, 3)
WhiteAccentLine.Position = UDim2.new(0, 0, 1, -3)
WhiteAccentLine.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
WhiteAccentLine.BorderSizePixel = 0
WhiteAccentLine.Parent = QuickBarFrame

-- Drawer Shortcut di bawah Quick Bar 1
local QuickDrawer = Instance.new("Frame")
QuickDrawer.Size = UDim2.new(1, 0, 0, 74)
QuickDrawer.Position = UDim2.new(0, 0, 1, 6)
QuickDrawer.BackgroundColor3 = Color3.fromRGB(12, 12, 16)
QuickDrawer.Visible = false
QuickDrawer.Parent = QuickBarFrame
Instance.new("UICorner", QuickDrawer).CornerRadius = UDim.new(0, 6)
AddStroke(QuickDrawer, 1.5, Color3.fromRGB(39, 39, 42), 0)

local QuickGrid = Instance.new("UIGridLayout")
QuickGrid.CellSize = UDim2.new(0.46, 0, 0, 28)
QuickGrid.CellPadding = UDim2.new(0.04, 0, 0, 6)
QuickGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
QuickGrid.VerticalAlignment = Enum.VerticalAlignment.Center
QuickGrid.Parent = QuickDrawer

for _, shortcutName in ipairs({"Auto Steal", "Speed x2", "Anti-AFK", "Deliver"}) do
    local sBtn = Instance.new("TextButton")
    sBtn.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
    sBtn.Font = Enum.Font.GothamBold
    sBtn.TextSize = 11
    sBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    sBtn.Text = shortcutName
    sBtn.Parent = QuickDrawer
    Instance.new("UICorner", sBtn).CornerRadius = UDim.new(0, 4)
    AddStroke(sBtn, 1, Color3.fromRGB(63, 63, 70), 0)
end

local isQuickOpen = false
-- Bisa Digeser + Klik untuk Buka/Tutup Drawer
MakeDraggable(QuickBarFrame, QuickBarFrame, function()
    isQuickOpen = not isQuickOpen
    QuickDrawer.Visible = isQuickOpen
    QuickChevron.Text = isQuickOpen and "v" or "^"
end)

-- =====================================================================
-- 3. FLOATING LOGO LOOPXCODE (BISA DIGESER + KLIK UNTUK BUKA/TUTUP UI)
-- =====================================================================
local LogoToggleBtn = Instance.new("Frame")
LogoToggleBtn.Name = "LoopXCode_FloatingLogo"
LogoToggleBtn.Size = UDim2.new(0, 54, 0, 54)
LogoToggleBtn.Position = UDim2.new(0, 18, 1, -74)
LogoToggleBtn.BackgroundColor3 = Color3.fromRGB(12, 12, 16)
LogoToggleBtn.Parent = ScreenGui
Instance.new("UICorner", LogoToggleBtn).CornerRadius = UDim.new(0, 14)
AddStroke(LogoToggleBtn, 1.8, Color3.fromRGB(82, 82, 91), 0)

local LogoText = Instance.new("TextLabel")
LogoText.Size = UDim2.new(1, 0, 1, 0)
LogoText.BackgroundTransparency = 1
LogoText.Font = Enum.Font.FredokaOne
LogoText.TextSize = 18
LogoText.TextColor3 = Color3.fromRGB(255, 255, 255)
LogoText.Text = "LX"
LogoText.Parent = LogoToggleBtn
AddTextStroke(LogoText, 1.4)

-- =====================================================================
-- 4. MAIN 3-COLUMN "LOOPXCODE" WINDOW (DARK MODE & DRAGGABLE)
-- =====================================================================
local HubContainer = Instance.new("Frame")
HubContainer.Name = "LoopXCode_MainHub"
HubContainer.Size = UDim2.new(0, 740, 0, 370)
HubContainer.Position = UDim2.new(0.5, -370, 0.5, -185)
HubContainer.BackgroundTransparency = 1
HubContainer.Parent = ScreenGui

local isHubOpen = true
local function SetHubVisible(state)
    isHubOpen = state
    HubContainer.Visible = state
end

-- Hubungkan Logo Floating: Geser untuk pindah posisi, Klik untuk buka/tutup Hub
MakeDraggable(LogoToggleBtn, LogoToggleBtn, function()
    SetHubVisible(not isHubOpen)
end)

-- Center Window
local CenterWindow = Instance.new("Frame")
CenterWindow.Size = UDim2.new(0, 480, 1, 0)
CenterWindow.Position = UDim2.new(0.5, -240, 0, 0)
CenterWindow.BackgroundColor3 = Color3.fromRGB(9, 9, 12)
CenterWindow.BackgroundTransparency = 0.08
CenterWindow.ClipsDescendants = true
CenterWindow.Parent = HubContainer
Instance.new("UICorner", CenterWindow).CornerRadius = UDim.new(0, 8)
AddStroke(CenterWindow, 1.8, Color3.fromRGB(39, 39, 42), 0)

-- Dark Obsidian Header Bar
local HeaderBar = Instance.new("Frame")
HeaderBar.Size = UDim2.new(1, 0, 0, 38)
HeaderBar.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
HeaderBar.BorderSizePixel = 0
HeaderBar.Parent = CenterWindow
AddStroke(HeaderBar, 1.5, Color3.fromRGB(39, 39, 42), 0)

-- Aktifkan Drag Main Window melalui HeaderBar
MakeDraggable(HeaderBar, HubContainer)

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, 0, 1, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.FredokaOne
TitleLabel.TextSize = 18
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Text = "LoopXCode"
TitleLabel.Parent = HeaderBar
AddTextStroke(TitleLabel, 1.6)

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Position = UDim2.new(1, -30, 0.5, -12)
CloseBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 36)
CloseBtn.Font = Enum.Font.GothamBlack
CloseBtn.TextSize = 12
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Text = "X"
CloseBtn.Parent = HeaderBar
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 4)
AddStroke(CloseBtn, 1, Color3.fromRGB(63, 63, 70), 0)
CloseBtn.MouseButton1Click:Connect(function()
    SetHubVisible(false)
end)

-- Scrolling Content Area
local ContentScroll = Instance.new("ScrollingFrame")
ContentScroll.Size = UDim2.new(1, -16, 1, -84)
ContentScroll.Position = UDim2.new(0, 8, 0, 44)
ContentScroll.BackgroundTransparency = 1
ContentScroll.ScrollBarThickness = 4
ContentScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
ContentScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
ContentScroll.Parent = CenterWindow

local ContentLayout = Instance.new("UIListLayout")
ContentLayout.Padding = UDim.new(0, 6)
ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
ContentLayout.Parent = ContentScroll

-- Bottom Search Filter
local SearchBar = Instance.new("TextBox")
SearchBar.Size = UDim2.new(1, -72, 0, 26)
SearchBar.Position = UDim2.new(0, 56, 1, -32)
SearchBar.BackgroundColor3 = Color3.fromRGB(7, 7, 10)
SearchBar.Font = Enum.Font.GothamMedium
SearchBar.TextSize = 12
SearchBar.TextColor3 = Color3.fromRGB(255, 255, 255)
SearchBar.PlaceholderText = "Filter features..."
SearchBar.Text = ""
SearchBar.TextXAlignment = Enum.TextXAlignment.Left
SearchBar.Parent = CenterWindow
Instance.new("UICorner", SearchBar).CornerRadius = UDim.new(0, 4)
AddStroke(SearchBar, 1, Color3.fromRGB(39, 39, 42), 0)

local SearchLabel = Instance.new("TextLabel")
SearchLabel.Size = UDim2.new(0, 46, 0, 26)
SearchLabel.Position = UDim2.new(0, 8, 1, -32)
SearchLabel.BackgroundTransparency = 1
SearchLabel.Font = Enum.Font.GothamBold
SearchLabel.TextSize = 11
SearchLabel.TextColor3 = Color3.fromRGB(200, 200, 210)
SearchLabel.Text = "Search"
SearchLabel.Parent = CenterWindow

-- Component Builders (Toggle, Dropdown, Dark Side Columns)
local FeatureRows = {}

local function AddToggle(name, defaultState, callback)
    local Row = Instance.new("Frame")
    Row.Size = UDim2.new(1, -6, 0, 34)
    Row.BackgroundColor3 = Color3.fromRGB(17, 17, 22)
    Row.Parent = ContentScroll
    Instance.new("UICorner", Row).CornerRadius = UDim.new(0, 5)
    AddStroke(Row, 1.2, Color3.fromRGB(34, 34, 42), 0)

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -70, 1, 0)
    Label.Position = UDim2.new(0, 12, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.FredokaOne
    Label.TextSize = 14
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.Text = name
    Label.Parent = Row
    AddTextStroke(Label, 1.2)

    local SwitchBg = Instance.new("TextButton")
    SwitchBg.Size = UDim2.new(0, 46, 0, 22)
    SwitchBg.Position = UDim2.new(1, -56, 0.5, -11)
    SwitchBg.BackgroundColor3 = Color3.fromRGB(9, 9, 12)
    SwitchBg.Text = ""
    SwitchBg.Parent = Row
    Instance.new("UICorner", SwitchBg).CornerRadius = UDim.new(0, 4)
    AddStroke(SwitchBg, 1.2, Color3.fromRGB(50, 50, 60), 0)

    local Knob = Instance.new("Frame")
    Knob.Size = UDim2.new(0, 16, 0, 16)
    Knob.Position = defaultState and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)
    Knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    Knob.Parent = SwitchBg
    Instance.new("UICorner", Knob).CornerRadius = UDim.new(0, 3)

    local state = defaultState
    SwitchBg.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(Knob, TweenInfo.new(0.15), {
            Position = state and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8),
            BackgroundColor3 = state and Color3.fromRGB(74, 222, 128) or Color3.fromRGB(255, 255, 255)
        }):Play()
        if callback then callback(state) end
    end)

    table.insert(FeatureRows, { Frame = Row, Name = string.lower(name) })
end

local function AddDropdown(name, options, defaultText, callback)
    local Row = Instance.new("Frame")
    Row.Size = UDim2.new(1, -6, 0, 34)
    Row.BackgroundColor3 = Color3.fromRGB(17, 17, 22)
    Row.Parent = ContentScroll
    Instance.new("UICorner", Row).CornerRadius = UDim.new(0, 5)
    AddStroke(Row, 1.2, Color3.fromRGB(34, 34, 42), 0)

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(0.55, 0, 1, 0)
    Label.Position = UDim2.new(0, 12, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.FredokaOne
    Label.TextSize = 14
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.Text = name
    Label.Parent = Row
    AddTextStroke(Label, 1.2)

    local DropBtn = Instance.new("TextButton")
    DropBtn.Size = UDim2.new(0, 125, 0, 24)
    DropBtn.Position = UDim2.new(1, -133, 0.5, -12)
    DropBtn.BackgroundColor3 = Color3.fromRGB(9, 9, 12)
    DropBtn.Font = Enum.Font.GothamBold
    DropBtn.TextSize = 11
    DropBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    DropBtn.Text = defaultText .. "  v"
    DropBtn.Parent = Row
    Instance.new("UICorner", DropBtn).CornerRadius = UDim.new(0, 4)
    AddStroke(DropBtn, 1.2, Color3.fromRGB(50, 50, 60), 0)

    local idx = 1
    DropBtn.MouseButton1Click:Connect(function()
        idx = (idx % #options) + 1
        DropBtn.Text = options[idx] .. "  v"
        if callback then callback(options[idx]) end
    end)

    table.insert(FeatureRows, { Frame = Row, Name = string.lower(name) })
end

SearchBar:GetPropertyChangedSignal("Text"):Connect(function()
    local q = string.lower(SearchBar.Text)
    for _, item in ipairs(FeatureRows) do
        item.Frame.Visible = (q == "" or string.find(item.Name, q, 1, true) ~= nil)
    end
end)

local function BuildDarkSideColumn(tabs, xOffset)
    local Col = Instance.new("Frame")
    Col.Size = UDim2.new(0, 118, 1, 0)
    Col.Position = UDim2.new(0, xOffset, 0, 0)
    Col.BackgroundTransparency = 1
    Col.Parent = HubContainer

    local Layout = Instance.new("UIListLayout")
    Layout.Padding = UDim.new(0, 6)
    Layout.VerticalAlignment = Enum.VerticalAlignment.Center
    Layout.Parent = Col

    for _, tabName in ipairs(tabs) do
        local Btn = Instance.new("TextButton")
        Btn.Size = UDim2.new(1, 0, 0, 34)
        Btn.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
        Btn.Font = Enum.Font.FredokaOne
        Btn.TextSize = 14
        Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        Btn.Text = tabName
        Btn.Parent = Col
        Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 6)
        AddStroke(Btn, 1.4, Color3.fromRGB(48, 48, 56), 0)
        AddTextStroke(Btn, 1.3)
    end
end

BuildDarkSideColumn({"Farm", "Event", "Player", "Predictor", "Server", "Misc", "Auto Hop"}, 0)
BuildDarkSideColumn({"Admin Abuse", "Discord", "About Me", "Quick & Keys", "Settings", "Config"}, 622)

AddToggle("Auto Steal", false, function(v) print("Auto Steal:", v) end)
AddDropdown("Move To Egg", {"Teleport", "Fast Tween", "Walk"}, "Teleport")
AddDropdown("Delivery", {"Fast Tween", "Instant", "Safe Path"}, "Fast Tween")
AddDropdown("Teleport Zones", {"7 selected", "All Zones", "Spawn Only"}, "7 selected")
AddDropdown("Steal Priority", {"Highest Value", "Closest Egg", "Rare First"}, "Highest Value")
AddDropdown("Target Areas", {"15 selected", "Main Island", "Event Zone"}, "15 selected")
AddDropdown("Min Rarity", {"Eternal", "Mythical", "Legendary", "Common"}, "Eternal")
