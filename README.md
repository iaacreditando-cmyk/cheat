local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local StarterGui = game:GetService("StarterGui")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local Stats = game:GetService("Stats")
local Camera = workspace.CurrentCamera

-- ==============================================================================
-- CONFIGURAÇÕES E DADOS
-- ==============================================================================
local ConfigData = {
    KeyUrl = "https://gist.githubusercontent.com/iaacreditando-cmyk/58cf502aa843ffd10f5820c40f9cb850/raw/gistfile1.txt",
    WebhookUrl = "https://ptb.discord.com/api/webhooks/1456729497016729641/fI_YLZWgPW5NCn29odumf7u1KLWsIGhLhtp7clCkk6lIYqo9nxx-QZjWTHQ4lQzGYxRL" 
}

-- Funções Utilitárias (Mantidas do seu script)
local function trim(s) return (s:gsub("^%s*(.-)%s*$", "%1")) end

local function GetPing()
    local ping = "N/A"
    pcall(function() ping = tostring(math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValueString():split(" ")[1])) .. " ms" end)
    return ping
end

local function GetFPS()
    return tostring(math.floor(workspace:GetRealPhysicsFPS()))
end

local function GetPlatform()
    if UserInputService.TouchEnabled and not UserInputService.MouseEnabled then return "Mobile" end
    if UserInputService.GamepadEnabled then return "Console" end
    return "PC"
end

local function SendLog(keyUsed)
    if ConfigData.WebhookUrl:find("COLOQUE") then return end
    
    local executor = (identifyexecutor and identifyexecutor()) or "Desconhecido"
    local data = {
        ["username"] = "System Logs",
        ["embeds"] = {{
            ["title"] = "Acesso Permitido",
            ["color"] = 65280,
            ["fields"] = {
                { ["name"] = "User", ["value"] = LocalPlayer.Name .. " ("..LocalPlayer.UserId..")", ["inline"] = true },
                { ["name"] = "Key", ["value"] = "||"..keyUsed.."||", ["inline"] = true },
                { ["name"] = "Info", ["value"] = "Plat: "..GetPlatform().."\nExec: "..executor.."\nPing: "..GetPing().."\nFPS: "..GetFPS(), ["inline"] = false },
                { ["name"] = "Game", ["value"] = "ID: "..game.PlaceId.."\nJob: ||"..game.JobId.."||", ["inline"] = false }
            },
            ["footer"] = { ["text"] = os.date("%d/%m/%Y %H:%M") }
        }}
    }
    local req = request or http_request or (syn and syn.request) or (fluxus and fluxus.request)
    if req then req({ Url = ConfigData.WebhookUrl, Method = "POST", Headers = { ["Content-Type"] = "application/json" }, Body = HttpService:JSONEncode(data) }) end
end

-- ==============================================================================
-- CARREGAMENTO DO CHEAT (NOVO UI + LÓGICA ANTIGA)
-- ==============================================================================
local function LoadCheat()
    StarterGui:SetCore("SendNotification", { Title = "Sucesso"; Text = "Carregando Menu V8..."; Duration = 5; })

    -- 1. Configurações Globais do Cheat
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
    
    local HitboxSizes = {2, 10, 20, 30, 50}
    local HitboxIndex = 1

    -- 2. Visuals Gui (FOV Circle - Separado do Menu)
    local VisualsGui = Instance.new("ScreenGui")
    VisualsGui.Name = "CheatVisuals"
    VisualsGui.IgnoreGuiInset = true
    if LocalPlayer:FindFirstChild("PlayerGui") then VisualsGui.Parent = LocalPlayer.PlayerGui end

    local FOVCircle = Instance.new("Frame", VisualsGui)
    FOVCircle.Name = "FOVCircle"
    FOVCircle.BackgroundTransparency = 1
    FOVCircle.Visible = true
    local FOVStroke = Instance.new("UIStroke", FOVCircle)
    FOVStroke.Color = Color3.fromRGB(138, 43, 226) -- Roxo
    FOVStroke.Thickness = 1.5
    Instance.new("UICorner", FOVCircle).CornerRadius = UDim.new(1,0)

    -- 3. CRIAÇÃO DO MENU NOVO (ORGANIZADO)
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ComboXerecaV8_New"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = LocalPlayer.PlayerGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(0, 280, 0, 450) -- Menu mais compacto e limpo
    mainFrame.Position = UDim2.new(0.5, -140, 0.5, -225)
    mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
    mainFrame.BorderSizePixel = 0
    mainFrame.Active = true
    mainFrame.Draggable = true
    mainFrame.Parent = screenGui

    -- Borda colorida
    local uiStroke = Instance.new("UIStroke", mainFrame)
    uiStroke.Color = Color3.fromRGB(138, 43, 226)
    uiStroke.Thickness = 2
    local uiCorner = Instance.new("UICorner", mainFrame)
    uiCorner.CornerRadius = UDim.new(0, 8)

    -- Título
    local titleLabel = Instance.new("TextLabel", mainFrame)
    titleLabel.Size = UDim2.new(1, 0, 0, 40)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = "COMBO XERECA - V8"
    titleLabel.TextColor3 = Color3.fromRGB(138, 43, 226)
    titleLabel.Font = Enum.Font.GothamBlack
    titleLabel.TextSize = 18
    titleLabel.Parent = mainFrame

    -- Container de Rolagem
    local buttonContainer = Instance.new("ScrollingFrame", mainFrame)
    buttonContainer.Size = UDim2.new(1, -20, 1, -50)
    buttonContainer.Position = UDim2.new(0, 10, 0, 45)
    buttonContainer.BackgroundTransparency = 1
    buttonContainer.ScrollBarThickness = 4
    buttonContainer.BorderSizePixel = 0
    buttonContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
    buttonContainer.Parent = mainFrame

    -- O Segredo da Organização: UIListLayout
    local listLayout = Instance.new("UIListLayout", buttonContainer)
    listLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listLayout.Padding = UDim.new(0, 6)

    -- ==========================================================
    -- FUNÇÃO HELPER PARA CRIAR BOTÕES
    -- ==========================================================
    local function createSection(text)
        local label = Instance.new("TextLabel", buttonContainer)
        label.Text = text
        label.Size = UDim2.new(1, 0, 0, 20)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.fromRGB(150, 150, 150)
        label.Font = Enum.Font.Code
        label.TextSize = 12
    end

    local function createButton(text, baseColor, callback)
        local btn = Instance.new("TextButton")
        btn.Parent = buttonContainer
        btn.Size = UDim2.new(1, 0, 0, 32)
        btn.BackgroundColor3 = baseColor or Color3.fromRGB(45, 45, 50)
        btn.Text = text
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Font = Enum.Font.GothamSemibold
        btn.TextSize = 13
        
        local corner = Instance.new("UICorner", btn)
        corner.CornerRadius = UDim.new(0, 6)

        btn.MouseButton1Click:Connect(function()
            -- Animaçãozinha
            local originalColor = btn.BackgroundColor3
            btn.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
            task.wait(0.1)
            btn.BackgroundColor3 = originalColor
            
            -- Executa a lógica e atualiza o botão se retornar novo texto/cor
            if callback then
                local newText, newColor = callback()
                if newText then btn.Text = newText end
                if newColor then btn.BackgroundColor3 = newColor end
            end
        end)
        return btn
    end

    -- ==========================================================
    -- ADICIONANDO OS BOTÕES (INTEGRAÇÃO)
    -- ==========================================================
    
    createSection("[ VISUALS ]")
    
    createButton("ESP: OFF", Color3.fromRGB(45, 45, 50), function()
        Config.ESPEnabled = not Config.ESPEnabled
        if Config.ESPEnabled then
            return "ESP: ON", Color3.fromRGB(0, 150, 0)
        else
            return "ESP: OFF", Color3.fromRGB(45, 45, 50)
        end
    end)

    createButton("Team Check: ON", Color3.fromRGB(0, 150, 0), function()
        Config.TeamCheck = not Config.TeamCheck
        if Config.TeamCheck then
            return "Team Check: ON", Color3.fromRGB(0, 150, 0)
        else
            return "Team Check: OFF", Color3.fromRGB(150, 0, 0)
        end
    end)

    createSection("[ AIMBOT & HITBOX ]")

    createButton("Wall Check: ON", Color3.fromRGB(0, 150, 0), function()
        Config.WallCheck = not Config.WallCheck
        return Config.WallCheck and "Wall Check: ON" or "Wall Check: OFF", 
               Config.WallCheck and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    end)

    createButton("Hitbox: Normal (2)", Color3.fromRGB(150, 50, 50), function()
        HitboxIndex = HitboxIndex + 1
        if HitboxIndex > #HitboxSizes then HitboxIndex = 1 end
        Config.HitboxSize = HitboxSizes[HitboxIndex]
        return "Hitbox: " .. Config.HitboxSize, Color3.fromRGB(150, 50, 50)
    end)

    createButton("Ver Hitbox: ON", Color3.fromRGB(0, 150, 0), function()
        Config.ShowHitbox = not Config.ShowHitbox
        return Config.ShowHitbox and "Ver Hitbox: ON" or "Ver Hitbox: OFF",
               Config.ShowHitbox and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(45, 45, 50)
    end)

    -- BOTOES DE FOV LADO A LADO
    local fovContainer = Instance.new("Frame", buttonContainer)
    fovContainer.Size = UDim2.new(1,0,0,30); fovContainer.BackgroundTransparency = 1
    local fovLayout = Instance.new("UIListLayout", fovContainer)
    fovLayout.FillDirection = Enum.FillDirection.Horizontal; fovLayout.Padding = UDim.new(0,5)
    
    local fovDec = Instance.new("TextButton", fovContainer)
    fovDec.Size = UDim2.new(0.3,0,1,0); fovDec.Text = "-"; fovDec.BackgroundColor3 = Color3.fromRGB(150,50,50); fovDec.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", fovDec).CornerRadius = UDim.new(0,6)
    
    local fovLabel = Instance.new("TextLabel", fovContainer)
    fovLabel.Size = UDim2.new(0.35,0,1,0); fovLabel.Text = "FOV: 150"; fovLabel.BackgroundTransparency = 1; fovLabel.TextColor3 = Color3.new(1,1,1)
    
    local fovInc = Instance.new("TextButton", fovContainer)
    fovInc.Size = UDim2.new(0.3,0,1,0); fovInc.Text = "+"; fovInc.BackgroundColor3 = Color3.fromRGB(50,150,50); fovInc.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", fovInc).CornerRadius = UDim.new(0,6)

    fovInc.MouseButton1Click:Connect(function() Config.FOVSize = Config.FOVSize + 10; fovLabel.Text = "FOV: "..Config.FOVSize end)
    fovDec.MouseButton1Click:Connect(function() Config.FOVSize = math.max(10, Config.FOVSize - 10); fovLabel.Text = "FOV: "..Config.FOVSize end)

    createSection("[ EXTRAS (Seu Pedido) ]")

    createButton("Velocidade (Speed 50)", Color3.fromRGB(45, 45, 50), function()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = 50
        end
    end)

    createButton("Pulo Alto (Jump 100)", Color3.fromRGB(45, 45, 50), function()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.UseJumpPower = true
            LocalPlayer.Character.Humanoid.JumpPower = 100
        end
    end)

    createButton("Resetar Personagem", Color3.fromRGB(150, 0, 0), function()
        if LocalPlayer.Character then LocalPlayer.Character:BreakJoints() end
    end)

    createButton("FECHAR MENU", Color3.fromRGB(20, 20, 20), function()
        mainFrame.Visible = false
    end)


    -- ==========================================================
    -- LÓGICA DO CHEAT (MANTIDA DO ORIGINAL)
    -- ==========================================================
    
    -- Funções Lógicas Auxiliares
    local function GetRainbowColor() local hue = os.clock() % 3 / 3; return Color3.fromHSV(hue, 1, 1) end
    local function IsEnemy(p)
        if not Config.TeamCheck then return true end
        if p.Team and LocalPlayer.Team and p.Team == LocalPlayer.Team then return false end
        return true
    end
    local function CheckVisibility(part)
        local params = RaycastParams.new(); params.FilterDescendantsInstances = {LocalPlayer.Character, part.Parent, Camera}; params.FilterType = Enum.RaycastFilterType.Exclude
        return workspace:Raycast(Camera.CFrame.Position, part.Position - Camera.CFrame.Position, params) == nil
    end

    local function UpdateESP(p)
        if not p.Character then return end
        local hl = p.Character:FindFirstChild("SimulatedHighlight")
        local head = p.Character:FindFirstChild("Head")
        local bg = head and head:FindFirstChild("ESPText")
        
        if Config.ESPEnabled and IsEnemy(p) and head then
            local vis = CheckVisibility(head)
            local col = vis and GetRainbowColor() or Color3.fromRGB(255,0,0)
            if not hl then hl = Instance.new("Highlight", p.Character); hl.Name = "SimulatedHighlight"; hl.FillTransparency = 0.5; hl.OutlineTransparency = 0 end
            if not bg then
                bg = Instance.new("BillboardGui", head); bg.Name = "ESPText"; bg.Size = UDim2.new(0,200,0,50); bg.StudsOffset = Vector3.new(0,2,0); bg.AlwaysOnTop = true
                local t = Instance.new("TextLabel", bg); t.Size = UDim2.new(1,0,1,0); t.BackgroundTransparency = 1; t.Font = Enum.Font.GothamBold; t.TextSize = 14
            end
            local dist = math.floor((LocalPlayer.Character.Head.Position - head.Position).Magnitude)
            bg.TextLabel.Text = p.Name.."\n["..dist.."m]"
            hl.FillColor = col; hl.OutlineColor = col; bg.TextLabel.TextColor3 = col
            hl.Enabled = true; bg.Enabled = true
        else
            if hl then hl:Destroy() end
            if bg then bg:Destroy() end
        end
    end

    -- Loop Principal
    UserInputService.InputBegan:Connect(function(input, gp)
        if input.KeyCode == Enum.KeyCode.Insert then mainFrame.Visible = not mainFrame.Visible end
        if not gp and (input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey) then Config.IsAiming = true end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey then Config.IsAiming = false end
    end)

    RunService.RenderStepped:Connect(function()
        FOVCircle.Size = UDim2.new(0, Config.FOVSize*2, 0, Config.FOVSize*2)
        FOVCircle.Position = UDim2.new(0, UserInputService:GetMouseLocation().X - Config.FOVSize, 0, UserInputService:GetMouseLocation().Y - Config.FOVSize)
        FOVCircle.Visible = Config.ShowFOV
        
        local Target = nil
        local MinDist = Config.FOVSize

        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                UpdateESP(p)
                if p.Character and IsEnemy(p) then
                    -- HITBOX EXPANDER
                    for _, part in pairs({p.Character:FindFirstChild("Head"), p.Character:FindFirstChild("HumanoidRootPart")}) do
                        if part and part:IsA("BasePart") then
                            local sz = Config.HitboxSize > 2 and Vector3.new(Config.HitboxSize, Config.HitboxSize, Config.HitboxSize) or (part.Name=="Head" and Vector3.new(2,1,1) or Vector3.new(2,2,1))
                            if part.Size ~= sz then
                                part.Size = sz; part.CanCollide = false
                                if Config.HitboxSize > 2 then
                                    part.Transparency = Config.ShowHitbox and 0.85 or 1
                                    part.Material = Enum.Material.ForceField
                                    part.Color = (Config.ShowHitbox and CheckVisibility(p.Character.Head)) and GetRainbowColor() or Color3.fromRGB(255,0,0)
                                else
                                    part.Transparency = part.Name=="Head" and 0 or 1; part.Material = Enum.Material.Plastic
                                end
                            end
                        end
                    end
                    -- AIMBOT CALCULATION
                    if Config.IsAiming and p.Character:FindFirstChild("Head") then
                        local pos, onScreen = Camera:WorldToScreenPoint(p.Character.Head.Position)
                        if onScreen then
                            local dist = (Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                            if dist < MinDist then
                                if not Config.WallCheck or CheckVisibility(p.Character.Head) then
                                    Target = p.Character.Head; MinDist = dist
                                end
                            end
                        end
                    end
                end
            end
        end
        if Target then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, Target.Position), 0.2) end
    end)
end

-- ==============================================================================
-- SISTEMA DE KEY 
-- ==============================================================================
local function CheckOnlineKey(inputKey)
    local s, r = pcall(function() return game:HttpGet(ConfigData.KeyUrl) end)
    if not s then return "Erro" end
    for line in r:gmatch("[^\r\n]+") do if trim(line) == trim(inputKey) then return true end end
    return false
end

if LocalPlayer.PlayerGui:FindFirstChild("MyKeySystem") then LocalPlayer.PlayerGui.MyKeySystem:Destroy() end
local Screen = Instance.new("ScreenGui", LocalPlayer.PlayerGui); Screen.Name = "MyKeySystem"
local Frame = Instance.new("Frame", Screen); Frame.Size = UDim2.new(0,300,0,180); Frame.Position = UDim2.new(0.5,-150,0.5,-90)
Frame.BackgroundColor3 = Color3.fromRGB(25,25,30); Frame.Active = true; Frame.Draggable = true
Instance.new("UICorner", Frame)
local Txt = Instance.new("TextLabel", Frame); Txt.Text = "KEY SYSTEM V8"; Txt.Size = UDim2.new(1,0,0,40); Txt.TextColor3 = Color3.new(1,1,1); Txt.BackgroundTransparency = 1
local Box = Instance.new("TextBox", Frame); Box.PlaceholderText = "Key..."; Box.Size = UDim2.new(0.8,0,0,35); Box.Position = UDim2.new(0.1,0,0.35,0); Box.BackgroundColor3 = Color3.fromRGB(40,40,45); Box.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", Box)
local Btn = Instance.new("TextButton", Frame); Btn.Text = "ENTRAR"; Btn.Size = UDim2.new(0.5,0,0,35); Btn.Position = UDim2.new(0.25,0,0.65,0); Btn.BackgroundColor3 = Color3.fromRGB(0,150,0); Btn.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", Btn)
local Stat = Instance.new("TextLabel", Frame); Stat.Text = ""; Stat.Size = UDim2.new(1,0,0,20); Stat.Position = UDim2.new(0,0,0.85,0); Stat.BackgroundTransparency = 1; Stat.TextColor3 = Color3.fromRGB(150,150,150)

Btn.MouseButton1Click:Connect(function()
    local key = trim(Box.Text)
    if #key < 3 then Stat.Text = "Key Inválida"; return end
    Stat.Text = "Verificando..."
    if CheckOnlineKey(key) == true then
        Stat.Text = "Sucesso!"; SendLog(key); task.wait(1); Screen:Destroy(); LoadCheat()
    else
        Stat.Text = "Key Errada"
    end
end)
