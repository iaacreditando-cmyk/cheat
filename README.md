local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local StarterGui = game:GetService("StarterGui")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local KeyUrl = "https://pastebin.com/raw/ZNLS2qZ2" 

local function trim(s)
    return (s:gsub("^%s*(.-)%s*$", "%1"))
end

local function LoadCheat()
    StarterGui:SetCore("SendNotification", {
        Title = "Sucesso";
        Text = "Carregando...";
        Duration = 5;
    })

    local Config = {
        AimbotEnabled = false,
        AimbotKey = Enum.UserInputType.MouseButton2,
        IsAiming = false,
        FOVSize = 150,
        ShowFOV = true,
        HitboxSize = 2,
        ShowHitbox = true,
        ESPEnabled = false,
        WallCheck = true,
        TeamCheck = true
    }

    local WaitingForKey = false
    local HitboxIndex = 1
    local HitboxSizes = {2, 10, 20, 30, 50, 100, 200, 250, 300}

    local VisualsGui = Instance.new("ScreenGui")
    VisualsGui.Name = "CheatVisuals"
    VisualsGui.IgnoreGuiInset = true
    if LocalPlayer:FindFirstChild("PlayerGui") then
        VisualsGui.Parent = LocalPlayer.PlayerGui
    end

    local FOVCircle = Instance.new("Frame")
    FOVCircle.Name = "FOVCircle"
    FOVCircle.BackgroundTransparency = 1
    FOVCircle.Visible = true
    FOVCircle.Parent = VisualsGui

    local FOVStroke = Instance.new("UIStroke")
    FOVStroke.Color = Color3.fromRGB(255, 0, 0)
    FOVStroke.Thickness = 1.5
    FOVStroke.Transparency = 0.4
    FOVStroke.Parent = FOVCircle

    local FOVCorner = Instance.new("UICorner")
    FOVCorner.CornerRadius = UDim.new(1, 0)
    FOVCorner.Parent = FOVCircle

    local MenuGui = Instance.new("ScreenGui")
    MenuGui.Name = "Combo Xereca V5"
    MenuGui.ResetOnSpawn = false
    MenuGui.Parent = LocalPlayer.PlayerGui

    local MainFrame = Instance.new("Frame")
    MainFrame.Size = UDim2.new(0, 380, 0, 650)
    MainFrame.Position = UDim2.new(0.5, -190, 0.5, -325)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
    MainFrame.BorderSizePixel = 2
    MainFrame.BorderColor3 = Color3.fromRGB(138, 43, 226) 
    MainFrame.Active = true
    MainFrame.Draggable = true
    MainFrame.Parent = MenuGui

    local Title = Instance.new("TextLabel")
    Title.Text = "COMBO XERECA - V5"
    Title.Size = UDim2.new(1, -30, 0, 40)
    Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    Title.TextColor3 = Color3.fromRGB(138, 43, 226)
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 18
    Title.Parent = MainFrame

    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Text = "X"
    CloseBtn.Size = UDim2.new(0, 30, 0, 40)
    CloseBtn.Position = UDim2.new(1, -30, 0, 0)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseBtn.Font = Enum.Font.GothamBlack
    CloseBtn.TextSize = 18
    CloseBtn.Parent = MainFrame

    local Container = Instance.new("ScrollingFrame")
    Container.Size = UDim2.new(0.9, 0, 0.85, 0)
    Container.Position = UDim2.new(0.05, 0, 0.1, 0)
    Container.BackgroundTransparency = 1
    Container.BorderSizePixel = 0
    Container.ScrollBarThickness = 6
    Container.Parent = MainFrame

    local Layout = Instance.new("UIListLayout")
    Layout.Parent = Container
    Layout.Padding = UDim.new(0, 8)
    Layout.SortOrder = Enum.SortOrder.LayoutOrder

    function CreateLabel(text)
        local L = Instance.new("TextLabel")
        L.Text = text
        L.Size = UDim2.new(1, 0, 0, 25)
        L.BackgroundTransparency = 1
        L.TextColor3 = Color3.fromRGB(100, 100, 100)
        L.Font = Enum.Font.Code
        L.TextSize = 14
        L.Parent = Container
    end

    CreateLabel("[ VISUALS ]")
    local ToggleESPBtn = Instance.new("TextButton")
    ToggleESPBtn.Text = "ESP: OFF"
    ToggleESPBtn.Size = UDim2.new(1, 0, 0, 30)
    ToggleESPBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    ToggleESPBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleESPBtn.Parent = Container

    local TeamCheckBtn = Instance.new("TextButton")
    TeamCheckBtn.Text = "Team Check: ON"
    TeamCheckBtn.Size = UDim2.new(1, 0, 0, 30)
    TeamCheckBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0) 
    TeamCheckBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    TeamCheckBtn.Parent = Container

    CreateLabel("[ AIMBOT ]")
    local KeyBtn = Instance.new("TextButton")
    KeyBtn.Text = "Key: Right Click"
    KeyBtn.Size = UDim2.new(1, 0, 0, 30)
    KeyBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
    KeyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    KeyBtn.Parent = Container

    local WallCheckBtn = Instance.new("TextButton")
    WallCheckBtn.Text = "Wall Check: ON"
    WallCheckBtn.Size = UDim2.new(1, 0, 0, 30)
    WallCheckBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    WallCheckBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    WallCheckBtn.Parent = Container

    local FOVFrame = Instance.new("Frame")
    FOVFrame.Size = UDim2.new(1, 0, 0, 30)
    FOVFrame.BackgroundTransparency = 1
    FOVFrame.Parent = Container
    local FOVLayout = Instance.new("UIListLayout")
    FOVLayout.Parent = FOVFrame
    FOVLayout.FillDirection = Enum.FillDirection.Horizontal
    FOVLayout.Padding = UDim.new(0, 5)

    local FOVDecBtn = Instance.new("TextButton")
    FOVDecBtn.Text = "FOV (-)"
    FOVDecBtn.Size = UDim2.new(0.48, 0, 1, 0)
    FOVDecBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    FOVDecBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    FOVDecBtn.Parent = FOVFrame

    local FOVIncBtn = Instance.new("TextButton")
    FOVIncBtn.Text = "FOV (+)"
    FOVIncBtn.Size = UDim2.new(0.48, 0, 1, 0)
    FOVIncBtn.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    FOVIncBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    FOVIncBtn.Parent = FOVFrame

    local FOVLabel = Instance.new("TextLabel")
    FOVLabel.Text = "FOV: 150"
    FOVLabel.Size = UDim2.new(1, 0, 0, 20)
    FOVLabel.BackgroundTransparency = 1
    FOVLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    FOVLabel.Parent = Container

    CreateLabel("[ HITBOX ]")
    local HitBtn = Instance.new("TextButton")
    HitBtn.Text = "Hitbox: Normal (2)"
    HitBtn.Size = UDim2.new(1, 0, 0, 40)
    HitBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    HitBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    HitBtn.Font = Enum.Font.GothamBold
    HitBtn.Parent = Container

    local ToggleHitboxVisBtn = Instance.new("TextButton")
    ToggleHitboxVisBtn.Text = "Visible Hitbox: ON"
    ToggleHitboxVisBtn.Size = UDim2.new(1, 0, 0, 30)
    ToggleHitboxVisBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    ToggleHitboxVisBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleHitboxVisBtn.Parent = Container

    local function GetRainbowColor()
        local hue = os.clock() % 3 / 3 
        return Color3.fromHSV(hue, 1, 1) 
    end

    local function IsEnemy(player)
        if not Config.TeamCheck then return true end
        if player.Team and LocalPlayer.Team then
            if player.Team == LocalPlayer.Team then return false end
        end
        if player.TeamColor == LocalPlayer.TeamColor then return false end
        return true
    end

    local function CheckVisibility(targetPart)
        if not targetPart then return false end
        local params = RaycastParams.new()
        params.FilterDescendantsInstances = {LocalPlayer.Character, targetPart.Parent, Camera} 
        params.FilterType = Enum.RaycastFilterType.Exclude
        
        local direction = targetPart.Position - Camera.CFrame.Position
        local result = workspace:Raycast(Camera.CFrame.Position, direction, params)
        return result == nil 
    end

    local function UpdateESP(player)
        if not player.Character then return end
        local highlight = player.Character:FindFirstChild("SimulatedHighlight")
        local head = player.Character:FindFirstChild("Head")
        local bg = head and head:FindFirstChild("ESPText")
        
        local shouldDraw = Config.ESPEnabled and IsEnemy(player) and head
        
        if shouldDraw then
            local isVisible = CheckVisibility(head)
            local finalColor = isVisible and GetRainbowColor() or Color3.fromRGB(255, 0, 0)

            if not highlight then
                highlight = Instance.new("Highlight")
                highlight.Name = "SimulatedHighlight"
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = player.Character
            end
            if not bg then
                bg = Instance.new("BillboardGui")
                bg.Name = "ESPText"
                bg.Size = UDim2.new(0, 200, 0, 50)
                bg.StudsOffset = Vector3.new(0, 2, 0)
                bg.AlwaysOnTop = true
                local txt = Instance.new("TextLabel", bg)
                txt.Size = UDim2.new(1, 0, 1, 0)
                txt.BackgroundTransparency = 1
                txt.TextStrokeTransparency = 0
                txt.Font = Enum.Font.GothamBold
                txt.TextSize = 14
                bg.Parent = head
            end
            
            local dist = math.floor((LocalPlayer.Character.Head.Position - head.Position).Magnitude)
            local label = bg:FindFirstChild("TextLabel")
            if label then
                label.Text = player.Name .. "\n[" .. dist .. "m]"
                highlight.FillColor = finalColor
                highlight.OutlineColor = finalColor
                label.TextColor3 = finalColor
            end
            highlight.Enabled = true
            bg.Enabled = true
        else
            if highlight then highlight:Destroy() end
            if bg then bg:Destroy() end
        end
    end

    local function ClearESP()
        for _, p in pairs(Players:GetPlayers()) do
            if p.Character then
                if p.Character:FindFirstChild("SimulatedHighlight") then p.Character.SimulatedHighlight:Destroy() end
                if p.Character:FindFirstChild("Head") and p.Character.Head:FindFirstChild("ESPText") then p.Character.Head.ESPText:Destroy() end
            end
        end
    end

    CloseBtn.MouseButton1Click:Connect(function() MainFrame.Visible = false end)

    ToggleESPBtn.MouseButton1Click:Connect(function()
        Config.ESPEnabled = not Config.ESPEnabled
        if Config.ESPEnabled then
            ToggleESPBtn.Text = "ESP: ON"
            ToggleESPBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
        else
            ToggleESPBtn.Text = "ESP: OFF"
            ToggleESPBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            ClearESP()
        end
    end)
    TeamCheckBtn.MouseButton1Click:Connect(function()
        Config.TeamCheck = not Config.TeamCheck
        TeamCheckBtn.Text = Config.TeamCheck and "Team Check: ON" or "Team Check: OFF"
        TeamCheckBtn.BackgroundColor3 = Config.TeamCheck and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    end)
    WallCheckBtn.MouseButton1Click:Connect(function()
        Config.WallCheck = not Config.WallCheck
        WallCheckBtn.Text = Config.WallCheck and "Wall Check: ON" or "Wall Check: OFF"
        WallCheckBtn.BackgroundColor3 = Config.WallCheck and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
    end)
    FOVIncBtn.MouseButton1Click:Connect(function()
        Config.FOVSize = Config.FOVSize + 10
        FOVLabel.Text = "FOV: " .. Config.FOVSize
    end)
    FOVDecBtn.MouseButton1Click:Connect(function()
        if Config.FOVSize > 10 then Config.FOVSize = Config.FOVSize - 10 end
        FOVLabel.Text = "FOV: " .. Config.FOVSize
    end)
    HitBtn.MouseButton1Click:Connect(function()
        HitboxIndex = HitboxIndex + 1
        if HitboxIndex > #HitboxSizes then HitboxIndex = 1 end
        Config.HitboxSize = HitboxSizes[HitboxIndex]
        if Config.HitboxSize == 2 then HitBtn.Text = "Hitbox: Normal (2)" else HitBtn.Text = "Hitbox: " .. Config.HitboxSize end
    end)
    ToggleHitboxVisBtn.MouseButton1Click:Connect(function()
        Config.ShowHitbox = not Config.ShowHitbox
        if Config.ShowHitbox then
            ToggleHitboxVisBtn.Text = "Visible Hitbox: ON"
            ToggleHitboxVisBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
        else
            ToggleHitboxVisBtn.Text = "Visible Hitbox: OFF"
            ToggleHitboxVisBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
        end
    end)
    KeyBtn.MouseButton1Click:Connect(function()
        WaitingForKey = true
        KeyBtn.Text = "..."
    end)

    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if WaitingForKey and input.UserInputType ~= Enum.UserInputType.MouseMovement then
            Config.AimbotKey = input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode or input.UserInputType
            local k = string.gsub(tostring(Config.AimbotKey), "Enum.UserInputType.", ""):gsub("Enum.KeyCode.", "")
            KeyBtn.Text = "Key: " .. k
            WaitingForKey = false
            return
        end
        if input.KeyCode == Enum.KeyCode.Insert then MainFrame.Visible = not MainFrame.Visible return end
        if input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey then Config.IsAiming = true end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey then Config.IsAiming = false end
    end)

    RunService.RenderStepped:Connect(function()
        FOVCircle.Size = UDim2.new(0, Config.FOVSize * 2, 0, Config.FOVSize * 2)
        local mp = UserInputService:GetMouseLocation()
        FOVCircle.Position = UDim2.new(0, mp.X - Config.FOVSize, 0, mp.Y - Config.FOVSize)
        FOVStroke.Color = Color3.fromRGB(255, 0, 0)
        
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                UpdateESP(p)
                if p.Character and IsEnemy(p) then
                    for _, part in pairs(p.Character:GetChildren()) do
                        if part:IsA("BasePart") and (part.Name == "Head" or part.Name == "HumanoidRootPart") then
                            if Config.HitboxSize > 2 then
                                part.Size = Vector3.new(Config.HitboxSize, Config.HitboxSize, Config.HitboxSize)
                                part.CanCollide = false
                                if Config.ShowHitbox then
                                    part.Transparency = 0.6 
                                    part.Material = Enum.Material.ForceField
                                    local isVis = CheckVisibility(p.Character.Head)
                                    part.Color = isVis and GetRainbowColor() or Color3.fromRGB(255,0,0)
                                else
                                    part.Transparency = 1 
                                    part.Color = Color3.new(1,0,0) 
                                end
                            else
                                if part.Name == "Head" then part.Size = Vector3.new(2,1,1) end
                                if part.Name == "HumanoidRootPart" then part.Size = Vector3.new(2,2,1) end
                                part.Transparency = (part.Name=="HumanoidRootPart" and 1 or 0)
                                part.Material = Enum.Material.Plastic
                            end
                        end
                    end
                end
            end
        end
        if Config.IsAiming then
            local t = GetTarget()
            if t then
                Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, t.Position), 0.2)
            end
        end
    end)
end

local function CheckOnlineKey(inputKey)
    local success, content = pcall(function()
        return game:HttpGet(KeyUrl)
    end)

    if not success then return "ErroHTTP" end

    for line in content:gmatch("[^\r\n]+") do
        if trim(line) == trim(inputKey) then
            return true
        end
    end

    return false
end

if LocalPlayer.PlayerGui:FindFirstChild("MyKeySystem") then
    LocalPlayer.PlayerGui.MyKeySystem:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MyKeySystem"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

local LoginFrame = Instance.new("Frame")
LoginFrame.Name = "LoginFrame"
LoginFrame.Size = UDim2.new(0, 320, 0, 200)
LoginFrame.Position = UDim2.new(0.5, -160, 0.5, -100)
LoginFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
LoginFrame.BorderSizePixel = 0
LoginFrame.Active = true
LoginFrame.Draggable = true
LoginFrame.Parent = ScreenGui

local Stroke = Instance.new("UIStroke")
Stroke.Color = Color3.fromRGB(138, 43, 226)
Stroke.Thickness = 2
Stroke.Parent = LoginFrame

Instance.new("UICorner", LoginFrame).CornerRadius = UDim.new(0, 8)

local TitleLog = Instance.new("TextLabel")
TitleLog.Text = "KEY SYSTEM"
TitleLog.Size = UDim2.new(1, 0, 0, 40)
TitleLog.BackgroundTransparency = 1
TitleLog.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLog.Font = Enum.Font.GothamBlack
TitleLog.TextSize = 16
TitleLog.Parent = LoginFrame

local KeyInput = Instance.new("TextBox")
KeyInput.PlaceholderText = "Key..."
KeyInput.Text = ""
KeyInput.Size = UDim2.new(0.8, 0, 0, 40)
KeyInput.Position = UDim2.new(0.1, 0, 0.35, 0)
KeyInput.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.Font = Enum.Font.Gotham
KeyInput.TextSize = 14
KeyInput.Parent = LoginFrame
Instance.new("UICorner", KeyInput)

local CheckBtn = Instance.new("TextButton")
CheckBtn.Text = "CHECK KEY"
CheckBtn.Size = UDim2.new(0.6, 0, 0, 40)
CheckBtn.Position = UDim2.new(0.2, 0, 0.65, 0)
CheckBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
CheckBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CheckBtn.Font = Enum.Font.GothamBold
CheckBtn.TextSize = 16
CheckBtn.Parent = LoginFrame
Instance.new("UICorner", CheckBtn)

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Text = ""
StatusLabel.Size = UDim2.new(1, 0, 0, 20)
StatusLabel.Position = UDim2.new(0, 0, 0.88, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
StatusLabel.Font = Enum.Font.Code
StatusLabel.TextSize = 12
StatusLabel.Parent = LoginFrame

CheckBtn.MouseButton1Click:Connect(function()
    local key = trim(KeyInput.Text)
    
    if key == "" or string.len(key) < 3 then
        StatusLabel.Text = "Invalid Key"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        return
    end
    
    StatusLabel.Text = "Checking..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    CheckBtn.Text = "..."
    
    local resultado = CheckOnlineKey(key)
    
    if resultado == "ErroHTTP" then
        StatusLabel.Text = "Connection Error"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        CheckBtn.Text = "ERROR"
    elseif resultado == true then
        StatusLabel.Text = "SUCCESS!"
        StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        CheckBtn.Text = "OK"
        
        task.wait(1)
        ScreenGui:Destroy()
        LoadCheat()
    else
        StatusLabel.Text = "Wrong Key"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        CheckBtn.Text = "RETRY"
    end
end)
