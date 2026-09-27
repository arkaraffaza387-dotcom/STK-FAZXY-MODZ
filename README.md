--[[
    ╔══════════════════════════════════════════════════════════╗
    ║        ZetGames STK - SURVIVE THE KILLER                 ║
    ║        Multi-Key • 20+ Fitur • Toggle Menu               ║
    ║        Kompatibel: HP, Tablet, Laptop, PC                ║
    ╚══════════════════════════════════════════════════════════╝
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local CoreGui = game:GetService("CoreGui")

-- ================== DETEKSI PERANGKAT ==================
local isMobile = UserInputService.TouchEnabled
local screenSize = Workspace.CurrentCamera and Workspace.CurrentCamera.ViewportSize or Vector2.new(1920, 1080)
local scaleFactor = math.clamp(math.min(screenSize.X / 1920, screenSize.Y / 1080), 0.7, 1.5)

-- ================== TEMA WARNA ==================
local Theme = {
    Background = Color3.fromRGB(18, 18, 24),
    Header = Color3.fromRGB(25, 25, 35),
    Accent = Color3.fromRGB(0, 200, 255),
    AccentDark = Color3.fromRGB(0, 140, 200),
    Success = Color3.fromRGB(0, 220, 130),
    Danger = Color3.fromRGB(255, 60, 90),
    Warning = Color3.fromRGB(255, 180, 0),
    Text = Color3.fromRGB(240, 240, 250),
    TextDim = Color3.fromRGB(150, 150, 170),
    ButtonOff = Color3.fromRGB(35, 35, 45),
    ButtonOn = Color3.fromRGB(0, 180, 110),
    ToggleBtn = Color3.fromRGB(0, 200, 255),
}

-- ================== SISTEM KEY ==================
local function buildKeys()
    local keys = {}
    local function sc(...)
        local args = {...}
        local out = {}
        for _, c in ipairs(args) do table.insert(out, string.char(c)) end
        return table.concat(out)
    end

    keys[#keys+1] = {key = sc(65,122,102,101,114,77,111,100,122), expires = {year = 9999, month = 12, day = 31}, name = "AzferModz", tier = "Premium", status = "Permanent"}
    keys[#keys+1] = {key = sc(70,114,101,101,80,114,101,109,45,66,121,45,70,97,122,120,121), expires = {year = 9999, month = 12, day = 31}, name = "FreePrem-By-Fazxy", tier = "Free", status = "Permanent"}
    keys[#keys+1] = {key = sc(65,122,102,101,114,67,111,100,101), expires = {year = 2026, month = 11, day = 26}, name = "AzferCode", tier = "Code", status = "26 Nov 2026"}
    keys[#keys+1] = {key = sc(65,122,102,101,114,72,99), expires = {year = 2027, month = 1, day = 27}, name = "AzferHc", tier = "Code", status = "27 Jan 2027"}
    keys[#keys+1] = {key = sc(70,97,122,120,121,70,114,101,101), expires = {year = 2027, month = 9, day = 10}, name = "FazxyFree", tier = "Code", status = "10 Sept 2027"}
    keys[#keys+1] = {key = sc(65,122,102,101,114,70,114,101,101), expires = {year = 2026, month = 9, day = 5}, name = "AzferFree", tier = "Free", status = "5 Sept 2026"}
    return keys
end

local VALID_KEYS = buildKeys()
local URL_GET_KEY = "https://arkaraffaza387-dotcom.github.io/Key-Zero/"

local function getCurrentDate()
    local d = os.date("*t")
    return {year = d.year, month = d.month, day = d.day}
end

local function checkKey(inputKey)
    local current = getCurrentDate()
    for _, kd in ipairs(VALID_KEYS) do
        if inputKey == kd.key then
            local exp = kd.expires
            local isExpired = false
            if current.year > exp.year then isExpired = true
            elseif current.year == exp.year and current.month > exp.month then isExpired = true
            elseif current.year == exp.year and current.month == exp.month and current.day > exp.day then isExpired = true
            end
            if isExpired then
                return false, "❌ Key kadaluarsa ("..kd.status..")"
            else
                if exp.year == 9999 then
                    return true, "✅ "..kd.name.." ("..kd.tier.." - "..kd.status..")"
                else
                    return true, "✅ "..kd.name.." (aktif s/d "..string.format("%02d/%02d/%04d", exp.day, exp.month, exp.year)..")"
                end
            end
        end
    end
    return false, "❌ Key salah atau tidak terdaftar!"
end

-- ================== UTILITAS ==================
local function createNotification(text, duration, color)
    local gui = Instance.new("ScreenGui")
    gui.Name = "ZetNotif"
    gui.ResetOnSpawn = false
    gui.Parent = CoreGui
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 340 * scaleFactor, 0, 55 * scaleFactor)
    frame.Position = UDim2.new(0.5, -170 * scaleFactor, 0.05, 0)
    frame.BackgroundColor3 = Theme.Header
    frame.BorderSizePixel = 0
    frame.BackgroundTransparency = 1
    frame.Parent = gui
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = color or Theme.Accent
    stroke.Thickness = 2
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1,-20,1,0)
    label.Position = UDim2.new(0,10,0,0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Theme.Text
    label.Font = Enum.Font.GothamBold
    label.TextScaled = true
    label.TextWrapped = true
    label.Parent = frame
    TweenService:Create(frame, TweenInfo.new(0.3), {BackgroundTransparency = 0}):Play()
    task.spawn(function()
        task.wait(duration or 3)
        TweenService:Create(frame, TweenInfo.new(0.3), {BackgroundTransparency = 1}):Play()
        task.wait(0.3)
        gui:Destroy()
    end)
end

local function openWebsite(url)
    if syn and syn.open_web then syn.open_web(url)
    elseif open_web then open_web(url)
    elseif setclipboard then
        setclipboard(url)
        createNotification("Link disalin ke clipboard!", 4, Theme.Warning)
    else
        createNotification("Buka: "..url, 5, Theme.Warning)
    end
end

-- ================== DRAGGABLE ==================
local function makeDraggable(gui, handle)
    local dragging, dragStart, startPos = false, nil, nil
    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
        end
    end)
    handle.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    handle.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

-- ================== KEY GUI (ZetGames STK) ==================
local function createKeyGUI()
    if CoreGui:FindFirstChild("ZetKeyGUI") then CoreGui.ZetKeyGUI:Destroy() end
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ZetKeyGUI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = CoreGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 400 * scaleFactor, 0, 340 * scaleFactor)
    mainFrame.Position = UDim2.new(0.5, -200 * scaleFactor, 0.5, -170 * scaleFactor)
    mainFrame.BackgroundColor3 = Theme.Background
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = screenGui
    Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 12)
    local stroke = Instance.new("UIStroke", mainFrame)
    stroke.Color = Theme.Accent
    stroke.Thickness = 2

    local header = Instance.new("TextButton")
    header.Size = UDim2.new(1,0,0,50 * scaleFactor)
    header.BackgroundColor3 = Theme.Header
    header.Text = "🔒  ZETGAMES STK - LOGIN"
    header.TextColor3 = Theme.Accent
    header.Font = Enum.Font.GothamBold
    header.TextScaled = true
    header.BorderSizePixel = 0
    header.Parent = mainFrame
    Instance.new("UICorner", header).CornerRadius = UDim.new(0, 12)

    local subtitle = Instance.new("TextLabel")
    subtitle.Size = UDim2.new(1,0,0,25 * scaleFactor)
    subtitle.Position = UDim2.new(0,0,0,55 * scaleFactor)
    subtitle.BackgroundTransparency = 1
    subtitle.Text = "Masukkan key untuk melanjutkan"
    subtitle.TextColor3 = Theme.TextDim
    subtitle.Font = Enum.Font.Gotham
    subtitle.TextScaled = true
    subtitle.Parent = mainFrame

    local keyInput = Instance.new("TextBox")
    keyInput.Size = UDim2.new(1,-40 * scaleFactor,0,50 * scaleFactor)
    keyInput.Position = UDim2.new(0,20 * scaleFactor,0,95 * scaleFactor)
    keyInput.BackgroundColor3 = Theme.ButtonOff
    keyInput.TextColor3 = Theme.Text
    keyInput.PlaceholderText = "Masukkan Key..."
    keyInput.PlaceholderColor3 = Theme.TextDim
    keyInput.Font = Enum.Font.Gotham
    keyInput.TextScaled = true
    keyInput.ClearTextOnFocus = false
    keyInput.Parent = mainFrame
    Instance.new("UICorner", keyInput).CornerRadius = UDim.new(0, 8)
    local kStroke = Instance.new("UIStroke", keyInput)
    kStroke.Color = Theme.AccentDark
    kStroke.Thickness = 1

    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(1,-40 * scaleFactor,0,35 * scaleFactor)
    statusLabel.Position = UDim2.new(0,20 * scaleFactor,0,155 * scaleFactor)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = ""
    statusLabel.TextColor3 = Theme.Danger
    statusLabel.Font = Enum.Font.GothamMedium
    statusLabel.TextScaled = true
    statusLabel.TextWrapped = true
    statusLabel.Parent = mainFrame

    local loginBtn = Instance.new("TextButton")
    loginBtn.Size = UDim2.new(0.5,-25 * scaleFactor,0,50 * scaleFactor)
    loginBtn.Position = UDim2.new(0,20 * scaleFactor,0,205 * scaleFactor)
    loginBtn.BackgroundColor3 = Theme.Success
    loginBtn.Text = "✓ LOGIN"
    loginBtn.TextColor3 = Theme.Text
    loginBtn.Font = Enum.Font.GothamBold
    loginBtn.TextScaled = true
    loginBtn.Parent = mainFrame
    Instance.new("UICorner", loginBtn).CornerRadius = UDim.new(0, 8)

    local getKeyBtn = Instance.new("TextButton")
    getKeyBtn.Size = UDim2.new(0.5,-25 * scaleFactor,0,50 * scaleFactor)
    getKeyBtn.Position = UDim2.new(0.5,5 * scaleFactor,0,205 * scaleFactor)
    getKeyBtn.BackgroundColor3 = Theme.Accent
    getKeyBtn.Text = "🔑 GET KEY"
    getKeyBtn.TextColor3 = Theme.Text
    getKeyBtn.Font = Enum.Font.GothamBold
    getKeyBtn.TextScaled = true
    getKeyBtn.Parent = mainFrame
    Instance.new("UICorner", getKeyBtn).CornerRadius = UDim.new(0, 8)

    local info = Instance.new("TextLabel")
    info.Size = UDim2.new(1,-40 * scaleFactor,0,60 * scaleFactor)
    info.Position = UDim2.new(0,20 * scaleFactor,0,265 * scaleFactor)
    info.BackgroundTransparency = 1
    info.Text = "Key tersedia di website key generator.\nAzferFree sudah EXPIRED sejak 5 Sept 2026."
    info.TextColor3 = Theme.TextDim
    info.Font = Enum.Font.Gotham
    info.TextScaled = true
    info.TextWrapped = true
    info.Parent = mainFrame

    makeDraggable(mainFrame, header)

    loginBtn.MouseButton1Click:Connect(function()
        local valid, msg = checkKey(keyInput.Text)
        if valid then
            statusLabel.TextColor3 = Theme.Success
            statusLabel.Text = msg
            task.wait(1.2)
            screenGui:Destroy()
            createNotification(msg, 3, Theme.Success)
            task.wait(0.5)
            loadCheatGUI()
        else
            statusLabel.TextColor3 = Theme.Danger
            statusLabel.Text = msg
        end
    end)

    getKeyBtn.MouseButton1Click:Connect(function()
        openWebsite(URL_GET_KEY)
    end)
end

-- ================== CHEAT SETTINGS ==================
local CheatSettings = {
    AutoKillAll = false, KillAura = false, KillAuraRange = 100,
    ESP = false, SpeedHack = false, SpeedMultiplier = 25,
    JumpPower = false, JumpPowerValue = 120,
    Fly = false, FlySpeed = 80, Noclip = false, InfiniteJump = false,
    GodMode = false, DestroyMap = false, GrabAllItems = false, TeleportAllToMe = false,
    AntiAFK = true, FullBright = false, NoFog = false,
    TimeChanger = false, TimeValue = 12, GravityControl = false, GravityValue = 50,
    TargetPlayerName = "",
}

-- ================== FUNGSI CHEAT ==================
local function getChar(p) return p and p.Character end
local function getHum(c) return c and c:FindFirstChildOfClass("Humanoid") end
local function getRoot(c) return c and c:FindFirstChild("HumanoidRootPart") end

function autoKillAll()
    if not CheatSettings.AutoKillAll then return end
    local char = getChar(LocalPlayer)
    local hum = getHum(char)
    if not hum or hum.Health <= 0 then return end
    local isKiller = false
    local ls = LocalPlayer:FindFirstChild("leaderstats")
    if ls then
        local role = ls:FindFirstChild("Role") or ls:FindFirstChild("role") or ls:FindFirstChild("Team")
        if role and tostring(role.Value):lower():find("killer") then isKiller = true end
    end
    if not isKiller then
        for _, tool in ipairs(char:GetChildren()) do
            if tool:IsA("Tool") and (tool.Name:lower():find("knife") or tool.Name:lower():find("kill")) then
                isKiller = true; break
            end
        end
    end
    if not isKiller then return end
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local tHum = getHum(getChar(player))
            if tHum and tHum.Health > 0 then tHum.Health = 0; task.wait(0.03) end
        end
    end
end

function killAura()
    if not CheatSettings.KillAura then return end
    local root = getRoot(getChar(LocalPlayer))
    if not root then return end
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local tChar = getChar(player)
            local tHum = getHum(tChar)
            local tRoot = getRoot(tChar)
            if tHum and tRoot and tHum.Health > 0 then
                if (root.Position - tRoot.Position).Magnitude <= CheatSettings.KillAuraRange then tHum.Health = 0 end
            end
        end
    end
end

local espCache = {}
function updateESP()
    for _, obj in pairs(espCache) do if obj and obj.Parent then obj:Destroy() end end
    espCache = {}
    if not CheatSettings.ESP then return end
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local char = getChar(player)
            if char and char:FindFirstChild("Head") then
                local bb = Instance.new("BillboardGui")
                bb.Name = "ESP_"..player.Name
                bb.Adornee = char.Head
                bb.Size = UDim2.new(0, 200, 0, 50)
                bb.StudsOffset = Vector3.new(0, 3, 0)
                bb.AlwaysOnTop = true
                bb.Parent = char.Head
                local lbl = Instance.new("TextLabel")
                lbl.Size = UDim2.new(1,0,1,0)
                lbl.BackgroundTransparency = 1
                lbl.Text = player.Name
                lbl.TextColor3 = Color3.fromRGB(255, 80, 80)
                lbl.TextStrokeTransparency = 0
                lbl.TextStrokeColor3 = Color3.new(0,0,0)
                lbl.Font = Enum.Font.GothamBold
                lbl.TextScaled = true
                lbl.Parent = bb
                table.insert(espCache, bb)
            end
        end
    end
end

function applySpeed()
    local hum = getHum(getChar(LocalPlayer))
    if hum then hum.WalkSpeed = CheatSettings.SpeedHack and CheatSettings.SpeedMultiplier or 16 end
end

function applyJump()
    local hum = getHum(getChar(LocalPlayer))
    if hum then
        if CheatSettings.JumpPower then hum.UseJumpPower = true; hum.JumpPower = CheatSettings.JumpPowerValue
        else hum.UseJumpPower = false; hum.JumpHeight = 7.2 end
    end
end

local flyConn = nil
function toggleFly()
    local root = getRoot(getChar(LocalPlayer))
    if not root then return end
    if not CheatSettings.Fly then
        if flyConn then flyConn:Disconnect() flyConn = nil end
        for _, v in ipairs({root:FindFirstChild("ZetBG"), root:FindFirstChild("ZetBV")}) do if v then v:Destroy() end end
        return
    end
    local bg = Instance.new("BodyGyro")
    bg.Name = "ZetBG"; bg.P = 9e4; bg.maxTorque = Vector3.new(9e9,9e9,9e9); bg.cframe = root.CFrame; bg.Parent = root
    local bv = Instance.new("BodyVelocity")
    bv.Name = "ZetBV"; bv.velocity = Vector3.new(0,0,0); bv.maxForce = Vector3.new(9e9,9e9,9e9); bv.Parent = root
    flyConn = RunService.RenderStepped:Connect(function()
        if not CheatSettings.Fly then return end
        local dir = Vector3.new(0,0,0)
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir += root.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir -= root.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir -= root.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir += root.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir += Vector3.new(0,1,0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then dir -= Vector3.new(0,1,0) end
        if isMobile and _G.FlyTouchDirection then dir += _G.FlyTouchDirection end
        bv.velocity = dir * CheatSettings.FlySpeed
        bg.cframe = root.CFrame
    end)
end

function applyNoclip()
    local char = getChar(LocalPlayer)
    if not char then return end
    for _, part in ipairs(char:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = not CheatSettings.Noclip end
    end
end

local infJumpConn = nil
function toggleInfJump()
    if CheatSettings.InfiniteJump then
        if not infJumpConn then
            infJumpConn = UserInputService.JumpRequest:Connect(function()
                local hum = getHum(getChar(LocalPlayer))
                if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
            end)
        end
    else
        if infJumpConn then infJumpConn:Disconnect() infJumpConn = nil end
    end
end

local godConn = nil
function toggleGodMode()
    local hum = getHum(getChar(LocalPlayer))
    if not hum then return end
    if CheatSettings.GodMode then
        hum.MaxHealth = math.huge; hum.Health = math.huge
        hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
        if not godConn then
            godConn = hum.HealthChanged:Connect(function()
                if hum.Health < math.huge then hum.Health = math.huge end
            end)
        end
    else
        hum.MaxHealth = 100; hum.Health = 100
        hum:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
        if godConn then godConn:Disconnect() godConn = nil end
    end
end

function destroyMap()
    if not CheatSettings.DestroyMap then return end
    local playerChars = {}
    for _, p in ipairs(Players:GetPlayers()) do if p.Character then playerChars[p.Character] = true end end
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            local skip = false
            for char, _ in pairs(playerChars) do
                if obj:IsDescendantOf(char) then skip = true; break end
            end
            if not skip then obj:Destroy() end
        end
    end
    CheatSettings.DestroyMap = false
end

function grabAll()
    if not CheatSettings.GrabAllItems then return end
    local bp = LocalPlayer:FindFirstChild("Backpack")
    if not bp then return end
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("Tool") and obj.Parent ~= bp then
            local skip = false
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character and obj:IsDescendantOf(p.Character) then skip = true; break end
            end
            if not skip then obj.Parent = bp end
        end
    end
    CheatSettings.GrabAllItems = false
end

function tpAllToMe()
    if not CheatSettings.TeleportAllToMe then return end
    local myRoot = getRoot(getChar(LocalPlayer))
    if not myRoot then return end
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local tRoot = getRoot(getChar(p))
            if tRoot then tRoot.CFrame = CFrame.new(myRoot.Position + Vector3.new(math.random(-5,5), 3, math.random(-5,5))) end
        end
    end
    CheatSettings.TeleportAllToMe = false
end

function tpToPlayer()
    if CheatSettings.TargetPlayerName == "" then return end
    local target = Players:FindFirstChild(CheatSettings.TargetPlayerName)
    if target then
        local tRoot = getRoot(getChar(target))
        local myRoot = getRoot(getChar(LocalPlayer))
        if tRoot and myRoot then myRoot.CFrame = tRoot.CFrame + Vector3.new(0, 3, 0) end
    end
end

function antiAFK()
    if not CheatSettings.AntiAFK then return end
    local vu = game:GetService("VirtualUser")
    LocalPlayer.Idled:Connect(function()
        vu:CaptureController(); vu:ClickButton2(Vector2.new())
    end)
    CheatSettings.AntiAFK = false
end

function applyFullBright()
    if CheatSettings.FullBright then
        Lighting.Ambient = Color3.fromRGB(255,255,255); Lighting.Brightness = 2
    else
        Lighting.Ambient = Color3.fromRGB(70,70,70); Lighting.Brightness = 1
    end
end

function applyNoFog()
    if CheatSettings.NoFog then Lighting.FogEnd = 1e10; Lighting.FogStart = 1e10
    else Lighting.FogEnd = 100000; Lighting.FogStart = 0 end
end

function applyTime()
    if CheatSettings.TimeChanger then Lighting.ClockTime = CheatSettings.TimeValue end
end

function applyGravity()
    Workspace.Gravity = CheatSettings.GravityControl and CheatSettings.GravityValue or 196.2
end

-- ================== MAIN CHEAT GUI (ZetGames STK) ==================
local menuVisible = true
local toggleBtnRef = nil
local mainFrameRef = nil

function loadCheatGUI()
    if CoreGui:FindFirstChild("ZetCheatGUI") then CoreGui.ZetCheatGUI:Destroy() end

    local gui = Instance.new("ScreenGui")
    gui.Name = "ZetCheatGUI"
    gui.ResetOnSpawn = false
    gui.Parent = CoreGui

    -- ===== FLOATING TOGGLE BUTTON (Buka/Tutup Menu) =====
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Name = "ZetToggleBtn"
    toggleBtn.Size = UDim2.new(0, 65 * scaleFactor, 0, 65 * scaleFactor)
    toggleBtn.Position = UDim2.new(0, 20 * scaleFactor, 0.5, -32 * scaleFactor)
    toggleBtn.BackgroundColor3 = Theme.ToggleBtn
    toggleBtn.Text = "ZG"
    toggleBtn.TextColor3 = Color3.new(1,1,1)
    toggleBtn.Font = Enum.Font.GothamBlack
    toggleBtn.TextScaled = true
    toggleBtn.Parent = gui
    Instance.new("UICorner", toggleBtn).CornerRadius = UDim.new(1, 0)
    local toggleStroke = Instance.new("UIStroke", toggleBtn)
    toggleStroke.Color = Color3.new(1,1,1)
    toggleStroke.Thickness = 2
    toggleBtnRef = toggleBtn

    makeDraggable(toggleBtn, toggleBtn)

    -- ===== MAIN MENU =====
    local main = Instance.new("Frame")
    main.Name = "ZetMainMenu"
    main.Size = UDim2.new(0, 440 * scaleFactor, 0, 540 * scaleFactor)
    main.Position = UDim2.new(0.5, -220 * scaleFactor, 0.5, -270 * scaleFactor)
    main.BackgroundColor3 = Theme.Background
    main.BorderSizePixel = 0
    main.Parent = gui
    Instance.new("UICorner", main).CornerRadius = UDim.new(0, 12)
    local stroke = Instance.new("UIStroke", main)
    stroke.Color = Theme.Accent
    stroke.Thickness = 2
    mainFrameRef = main

    local header = Instance.new("TextButton")
    header.Size = UDim2.new(1,0,0,45 * scaleFactor)
    header.BackgroundColor3 = Theme.Header
    header.Text = "⚡ ZETGAMES STK - SURVIVE THE KILLER"
    header.TextColor3 = Theme.Accent
    header.Font = Enum.Font.GothamBold
    header.TextScaled = true
    header.BorderSizePixel = 0
    header.Parent = main
    Instance.new("UICorner", header).CornerRadius = UDim.new(0, 12)
    makeDraggable(main, header)

    -- Tombol close (X) — sembunyikan menu
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 35 * scaleFactor, 0, 35 * scaleFactor)
    closeBtn.Position = UDim2.new(1, -40 * scaleFactor, 0, 5 * scaleFactor)
    closeBtn.BackgroundColor3 = Theme.Danger
    closeBtn.Text = "✕"
    closeBtn.TextColor3 = Theme.Text
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextScaled = true
    closeBtn.Parent = main
    Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 8)

    -- Fungsi toggle menu
    local function setMenuVisible(vis)
        menuVisible = vis
        if vis then
            main.Visible = true
            main.Size = UDim2.new(0, 0, 0, 0)
            TweenService:Create(main, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                Size = UDim2.new(0, 440 * scaleFactor, 0, 540 * scaleFactor)
            }):Play()
            TweenService:Create(toggleBtn, TweenInfo.new(0.2), {BackgroundColor3 = Theme.ToggleBtn}):Play()
        else
            TweenService:Create(main, TweenInfo.new(0.2), {
                Size = UDim2.new(0, 0, 0, 0)
            }):Play()
            TweenService:Create(toggleBtn, TweenInfo.new(0.2), {BackgroundColor3 = Theme.Danger}):Play()
            task.delay(0.22, function() if not menuVisible then main.Visible = false end end)
        end
    end

    closeBtn.MouseButton1Click:Connect(function() setMenuVisible(false) end)

    -- Klik tombol floating untuk toggle
    local toggleClickCooldown = false
    toggleBtn.MouseButton1Click:Connect(function()
        if toggleClickCooldown then return end
        toggleClickCooldown = true
        setMenuVisible(not menuVisible)
        task.wait(0.3)
        toggleClickCooldown = false
    end)

    -- Tab system
    local tabContainer = Instance.new("Frame")
    tabContainer.Size = UDim2.new(1, 0, 0, 35 * scaleFactor)
    tabContainer.Position = UDim2.new(0, 0, 0, 50 * scaleFactor)
    tabContainer.BackgroundTransparency = 1
    tabContainer.Parent = main

    local tabs = {"Main", "Visual", "Player", "Misc"}
    local tabButtons = {}
    local pageFrames = {}

    local tabLayout = Instance.new("UIListLayout")
    tabLayout.FillDirection = Enum.FillDirection.Horizontal
    tabLayout.Padding = UDim.new(0, 5 * scaleFactor)
    tabLayout.Parent = tabContainer

    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, -10 * scaleFactor, 1, -100 * scaleFactor)
    scroll.Position = UDim2.new(0, 5 * scaleFactor, 0, 90 * scaleFactor)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 5
    scroll.ScrollBarImageColor3 = Theme.Accent
    scroll.CanvasSize = UDim2.new(0, 0, 0, 800 * scaleFactor)
    scroll.Parent = main

    for _, tabName in ipairs(tabs) do
        local page = Instance.new("Frame")
        page.Size = UDim2.new(1, 0, 0, 0)
        page.BackgroundTransparency = 1
        page.Visible = false
        page.Parent = scroll
        pageFrames[tabName] = page
    end
    pageFrames["Main"].Visible = true

    for _, tabName in ipairs(tabs) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 90 * scaleFactor, 1, 0)
        btn.BackgroundColor3 = Theme.ButtonOff
        btn.Text = tabName
        btn.TextColor3 = Theme.Text
        btn.Font = Enum.Font.GothamBold
        btn.TextScaled = true
        btn.Parent = tabContainer
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        tabButtons[tabName] = btn
        btn.MouseButton1Click:Connect(function()
            for _, page in pairs(pageFrames) do page.Visible = false end
            for _, b in pairs(tabButtons) do b.BackgroundColor3 = Theme.ButtonOff; b.TextColor3 = Theme.Text end
            pageFrames[tabName].Visible = true
            btn.BackgroundColor3 = Theme.AccentDark
        end)
    end
    tabButtons["Main"].BackgroundColor3 = Theme.AccentDark

    local function addToggle(parent, name, settingKey, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, 0, 0, 38 * scaleFactor)
        btn.BackgroundColor3 = Theme.ButtonOff
        btn.Text = name
        btn.TextColor3 = Theme.Text
        btn.Font = Enum.Font.GothamMedium
        btn.TextScaled = true
        btn.TextXAlignment = Enum.TextXAlignment.Left
        btn.Parent = parent
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        local pad = Instance.new("UIPadding", btn); pad.PaddingLeft = UDim.new(0, 12)

        local indicator = Instance.new("Frame")
        indicator.Size = UDim2.new(0, 50 * scaleFactor, 0, 24 * scaleFactor)
        indicator.Position = UDim2.new(1, -60 * scaleFactor, 0.5, -12 * scaleFactor)
        indicator.BackgroundColor3 = Color3.fromRGB(80,80,90)
        indicator.Parent = btn
        Instance.new("UICorner", indicator).CornerRadius = UDim.new(0, 12)

        local indText = Instance.new("TextLabel")
        indText.Size = UDim2.new(1,0,1,0)
        indText.BackgroundTransparency = 1
        indText.Text = "OFF"
        indText.TextColor3 = Theme.Text
        indText.Font = Enum.Font.GothamBold
        indText.TextScaled = true
        indText.Parent = indicator

        local function refresh()
            if CheatSettings[settingKey] then
                indicator.BackgroundColor3 = Theme.ButtonOn; indText.Text = "ON"
            else
                indicator.BackgroundColor3 = Color3.fromRGB(80,80,90); indText.Text = "OFF"
            end
        end

        btn.MouseButton1Click:Connect(function()
            CheatSettings[settingKey] = not CheatSettings[settingKey]
            refresh()
            if callback then callback(CheatSettings[settingKey]) end
        end)
        refresh()
    end

    local function addSlider(parent, name, settingKey, min, max, callback)
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(1, 0, 0, 55 * scaleFactor)
        frame.BackgroundColor3 = Theme.ButtonOff
        frame.Parent = parent
        Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 6)

        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, -20 * scaleFactor, 0, 22 * scaleFactor)
        label.Position = UDim2.new(0, 10 * scaleFactor, 0, 2 * scaleFactor)
        label.BackgroundTransparency = 1
        label.Text = name..": "..tostring(CheatSettings[settingKey])
        label.TextColor3 = Theme.Text
        label.Font = Enum.Font.GothamMedium
        label.TextScaled = true
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = frame

        local slider = Instance.new("TextButton")
        slider.Size = UDim2.new(1, -20 * scaleFactor, 0, 20 * scaleFactor)
        slider.Position = UDim2.new(0, 10 * scaleFactor, 0, 28 * scaleFactor)
        slider.BackgroundColor3 = Color3.fromRGB(60,60,70)
        slider.Text = ""
        slider.Parent = frame
        Instance.new("UICorner", slider).CornerRadius = UDim.new(0, 6)

        local fill = Instance.new("Frame")
        fill.Size = UDim2.new((CheatSettings[settingKey]-min)/(max-min), 0, 1, 0)
        fill.BackgroundColor3 = Theme.Accent
        fill.Parent = slider
        Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 6)

        local dragging = false
        local function updateFromInput(input)
            local rel = math.clamp((input.Position.X - slider.AbsolutePosition.X) / slider.AbsoluteSize.X, 0, 1)
            local val = math.floor(min + (max - min) * rel)
            CheatSettings[settingKey] = val
            fill.Size = UDim2.new(rel, 0, 1, 0)
            label.Text = name..": "..tostring(val)
            if callback then callback(val) end
        end
        slider.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true; updateFromInput(input)
            end
        end)
        slider.InputChanged:Connect(function(input)
            if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                updateFromInput(input)
            end
        end)
        slider.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
    end

    -- MAIN TAB
    local mainPage = pageFrames["Main"]
    local mainLayout = Instance.new("UIListLayout", mainPage)
    mainLayout.Padding = UDim.new(0, 6 * scaleFactor)
    mainLayout.SortOrder = Enum.SortOrder.LayoutOrder
    addToggle(mainPage, "🔪 Auto Kill All (Killer)", "AutoKillAll")
    addToggle(mainPage, "⚔ Kill Aura", "KillAura")
    addSlider(mainPage, "Kill Aura Range", "KillAuraRange", 10, 500)
    addToggle(mainPage, "🛡 God Mode", "GodMode")
    addToggle(mainPage, "💥 Destroy Map (sekali)", "DestroyMap", function() destroyMap() end)
    addToggle(mainPage, "📦 Grab All Items", "GrabAllItems", function() grabAll() end)
    addToggle(mainPage, "🌀 Teleport All To Me", "TeleportAllToMe", function() tpAllToMe() end)

    -- VISUAL TAB
    local visualPage = pageFrames["Visual"]
    local visualLayout = Instance.new("UIListLayout", visualPage)
    visualLayout.Padding = UDim.new(0, 6 * scaleFactor)
    visualLayout.SortOrder = Enum.SortOrder.LayoutOrder
    addToggle(visualPage, "👁 ESP (Nama Pemain)", "ESP", function() updateESP() end)
    addToggle(visualPage, "🌞 Full Bright", "FullBright", function() applyFullBright() end)
    addToggle(visualPage, "🌫 No Fog", "NoFog", function() applyNoFog() end)
    addToggle(visualPage, "🕐 Time Changer", "TimeChanger", function() applyTime() end)
    addSlider(visualPage, "Time Value", "TimeValue", 0, 24, function() applyTime() end)

    -- PLAYER TAB
    local playerPage = pageFrames["Player"]
    local playerLayout = Instance.new("UIListLayout", playerPage)
    playerLayout.Padding = UDim.new(0, 6 * scaleFactor)
    playerLayout.SortOrder = Enum.SortOrder.LayoutOrder
    addToggle(playerPage, "🏃 Speed Hack", "SpeedHack", function() applySpeed() end)
    addSlider(playerPage, "Speed Multiplier", "SpeedMultiplier", 16, 200, function() applySpeed() end)
    addToggle(playerPage, "🦘 Jump Power", "JumpPower", function() applyJump() end)
    addSlider(playerPage, "Jump Power Value", "JumpPowerValue", 50, 500, function() applyJump() end)
    addToggle(playerPage, "✈ Fly", "Fly", function() toggleFly() end)
    addSlider(playerPage, "Fly Speed", "FlySpeed", 20, 300)
    addToggle(playerPage, "👻 Noclip", "Noclip", function() applyNoclip() end)
    addToggle(playerPage, "♾ Infinite Jump", "InfiniteJump", function() toggleInfJump() end)

    -- MISC TAB
    local miscPage = pageFrames["Misc"]
    local miscLayout = Instance.new("UIListLayout", miscPage)
    miscLayout.Padding = UDim.new(0, 6 * scaleFactor)
    miscLayout.SortOrder = Enum.SortOrder.LayoutOrder
    addToggle(miscPage, "🤖 Anti AFK", "AntiAFK", function() antiAFK() end)
    addToggle(miscPage, "🌌 Gravity Control", "GravityControl", function() applyGravity() end)
    addSlider(miscPage, "Gravity Value", "GravityValue", 10, 300, function() applyGravity() end)

    local tpLabel = Instance.new("TextLabel")
    tpLabel.Size = UDim2.new(1, 0, 0, 25 * scaleFactor)
    tpLabel.BackgroundTransparency = 1
    tpLabel.Text = "Teleport ke Player:"
    tpLabel.TextColor3 = Theme.Text
    tpLabel.Font = Enum.Font.GothamBold
    tpLabel.TextScaled = true
    tpLabel.TextXAlignment = Enum.TextXAlignment.Left
    tpLabel.Parent = miscPage

    local tpDropdown = Instance.new("TextButton")
    tpDropdown.Size = UDim2.new(1, 0, 0, 38 * scaleFactor)
    tpDropdown.BackgroundColor3 = Theme.ButtonOff
    tpDropdown.Text = "Pilih Player"
    tpDropdown.TextColor3 = Theme.Text
    tpDropdown.Font = Enum.Font.GothamMedium
    tpDropdown.TextScaled = true
    tpDropdown.Parent = miscPage
    Instance.new("UICorner", tpDropdown).CornerRadius = UDim.new(0, 6)

    local tpList = Instance.new("Frame")
    tpList.Size = UDim2.new(1, 0, 0, 0)
    tpList.BackgroundTransparency = 1
    tpList.Visible = false
    tpList.Parent = miscPage
    local tpListLayout = Instance.new("UIListLayout", tpList)
    tpListLayout.Padding = UDim.new(0, 3 * scaleFactor)

    tpDropdown.MouseButton1Click:Connect(function()
        tpList.Visible = not tpList.Visible
        for _, c in ipairs(tpList:GetChildren()) do if c:IsA("TextButton") then c:Destroy() end end
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                local b = Instance.new("TextButton")
                b.Size = UDim2.new(1, 0, 0, 30 * scaleFactor)
                b.BackgroundColor3 = Theme.ButtonOff
                b.Text = p.Name
                b.TextColor3 = Theme.Text
                b.Font = Enum.Font.Gotham
                b.TextScaled = true
                b.Parent = tpList
                Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
                b.MouseButton1Click:Connect(function()
                    CheatSettings.TargetPlayerName = p.Name
                    tpDropdown.Text = "Target: "..p.Name
                    tpList.Visible = false
                    tpToPlayer()
                end)
            end
        end
        tpList.Size = UDim2.new(1, 0, 0, #tpList:GetChildren() * 33 * scaleFactor)
    end)

    -- Mobile fly controls
    if isMobile then
        local flyFrame = Instance.new("Frame")
        flyFrame.Size = UDim2.new(0, 140 * scaleFactor, 0, 140 * scaleFactor)
        flyFrame.Position = UDim2.new(1, -150 * scaleFactor, 1, -150 * scaleFactor)
        flyFrame.BackgroundTransparency = 1
        flyFrame.Parent = gui
        local dirs = {
            {name="▲", pos=UDim2.new(0.33,0,0,0), vec=Vector3.new(0,0,-1)},
            {name="▼", pos=UDim2.new(0.33,0,0.66,0), vec=Vector3.new(0,0,1)},
            {name="◄", pos=UDim2.new(0,0,0.33,0), vec=Vector3.new(-1,0,0)},
            {name="►", pos=UDim2.new(0.66,0,0.33,0), vec=Vector3.new(1,0,0)},
        }
        for _, d in ipairs(dirs) do
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(0.33,0,0.33,0)
            b.Position = d.pos
            b.BackgroundColor3 = Theme.Accent
            b.BackgroundTransparency = 0.5
            b.Text = d.name
            b.TextColor3 = Theme.Text
            b.Font = Enum.Font.GothamBold
            b.TextScaled = true
            b.Parent = flyFrame
            Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
            b.MouseButton1Down:Connect(function() if CheatSettings.Fly then _G.FlyTouchDirection = d.vec end end)
            b.MouseButton1Up:Connect(function() _G.FlyTouchDirection = nil end)
        end
    end

    -- Main loop
    RunService.Heartbeat:Connect(function()
        autoKillAll()
        killAura()
        if CheatSettings.SpeedHack then applySpeed() end
        if CheatSettings.JumpPower then applyJump() end
        if CheatSettings.Noclip then applyNoclip() end
        if CheatSettings.ESP then updateESP() end
        if CheatSettings.TimeChanger then applyTime() end
    end)

    -- Keybind PC: RightShift untuk toggle menu
    if not isMobile then
        UserInputService.InputBegan:Connect(function(input, gp)
            if gp then return end
            if input.KeyCode == Enum.KeyCode.RightShift then
                setMenuVisible(not menuVisible)
            end
        end)
    end
end

-- ================== START ==================
task.spawn(function()
    task.wait(1)
    createKeyGUI()
    print("[ZetGames STK] Script dimuat. Silakan login dengan key.")
end)
