local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local StarterGui = game:GetService("StarterGui")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")
local LocalizationService = game:GetService("LocalizationService")
local Stats = game:GetService("Stats")
local Camera = workspace.CurrentCamera

local ConfigData = {
    KeyUrl = "https://gist.githubusercontent.com/iaacreditando-cmyk/58cf502aa843ffd10f5820c40f9cb850/raw/gistfile1.txt",
    WebhookUrl = "https://ptb.discord.com/api/webhooks/1456729497016729641/fI_YLZWgPW5NCn29odumf7u1KLWsIGhLhtp7clCkk6lIYqo9nxx-QZjWTHQ4lQzGYxRL" 
}

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
    local hwid = (gethwid and gethwid()) or "Oculto"
    local data = {
        ["username"] = "System Logs",
        ["avatar_url"] = "https://cdn-icons-png.flaticon.com/512/2313/2313360.png",
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

local function LoadCheat()
    StarterGui:SetCore("SendNotification", { Title = "Sucesso"; Text = "Carregando Menu..."; Duration = 5; })

    local Config = {
        AimbotEnabled = false, AimbotKey = Enum.UserInputType.MouseButton2, IsAiming = false,
        FOVSize = 150, ShowFOV = true, HitboxSize = 2, ShowHitbox = true,
        ESPEnabled = false, WallCheck = true, TeamCheck = true
    }

    local HitboxSizes = {2, 10, 20, 30, 50, 100}
    local HitboxIndex = 1

    local VisualsGui = Instance.new("ScreenGui")
    VisualsGui.Name = "CheatVisuals"
    VisualsGui.IgnoreGuiInset = true
    if LocalPlayer:FindFirstChild("PlayerGui") then VisualsGui.Parent = LocalPlayer.PlayerGui end

    local FOVCircle = Instance.new("Frame", VisualsGui)
    FOVCircle.Name = "FOVCircle"; FOVCircle.BackgroundTransparency = 1; FOVCircle.Visible = true
    local FOVStroke = Instance.new("UIStroke", FOVCircle); FOVStroke.Color = Color3.fromRGB(255,0,0); FOVStroke.Thickness = 1.5
    Instance.new("UICorner", FOVCircle).CornerRadius = UDim.new(1,0)

    local MenuGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
    MenuGui.Name = "Combo Xereca V8"; MenuGui.ResetOnSpawn = false

    local MainFrame = Instance.new("Frame", MenuGui)
    MainFrame.Size = UDim2.new(0, 380, 0, 650); MainFrame.Position = UDim2.new(0.5, -190, 0.5, -325)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30); MainFrame.BorderSizePixel = 2; MainFrame.BorderColor3 = Color3.fromRGB(138, 43, 226)
    MainFrame.Active = true; MainFrame.Draggable = true

    local Title = Instance.new("TextLabel", MainFrame)
    Title.Text = "COMBO XERECA - V8"; Title.Size = UDim2.new(1, -30, 0, 40)
    Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20); Title.TextColor3 = Color3.fromRGB(138, 43, 226)
    Title.Font = Enum.Font.GothamBlack; Title.TextSize = 18

    local CloseBtn = Instance.new("TextButton", MainFrame)
    CloseBtn.Text = "X"; CloseBtn.Size = UDim2.new(0, 30, 0, 40); CloseBtn.Position = UDim2.new(1, -30, 0, 0)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0); CloseBtn.TextColor3 = Color3.new(1,1,1); CloseBtn.Font = Enum.Font.GothamBlack

    local Container = Instance.new("ScrollingFrame", MainFrame)
    Container.Size = UDim2.new(0.9, 0, 0.85, 0); Container.Position = UDim2.new(0.05, 0, 0.1, 0)
    Container.BackgroundTransparency = 1; Container.BorderSizePixel = 0; Container.ScrollBarThickness = 6
    local Layout = Instance.new("UIListLayout", Container); Layout.Padding = UDim.new(0, 8)

    local function CreateLabel(text)
        local L = Instance.new("TextLabel", Container); L.Text = text; L.Size = UDim2.new(1,0,0,25)
        L.BackgroundTransparency = 1; L.TextColor3 = Color3.fromRGB(100,100,100); L.Font = Enum.Font.Code; L.TextSize = 14
    end

    CreateLabel("[ VISUALS ]")
    local ToggleESPBtn = Instance.new("TextButton", Container); ToggleESPBtn.Text = "ESP: OFF"; ToggleESPBtn.Size = UDim2.new(1,0,0,30)
    ToggleESPBtn.BackgroundColor3 = Color3.fromRGB(50,50,50); ToggleESPBtn.TextColor3 = Color3.new(1,1,1)

    local TeamCheckBtn = Instance.new("TextButton", Container); TeamCheckBtn.Text = "Team Check: ON"; TeamCheckBtn.Size = UDim2.new(1,0,0,30)
    TeamCheckBtn.BackgroundColor3 = Color3.fromRGB(0,150,0); TeamCheckBtn.TextColor3 = Color3.new(1,1,1)

    CreateLabel("[ AIMBOT ]")
    local KeyBtn = Instance.new("TextButton", Container); KeyBtn.Text = "Key: Right Click"; KeyBtn.Size = UDim2.new(1,0,0,30)
    KeyBtn.BackgroundColor3 = Color3.fromRGB(40,40,45); KeyBtn.TextColor3 = Color3.new(1,1,1)

    local WallCheckBtn = Instance.new("TextButton", Container); WallCheckBtn.Text = "Wall Check: ON"; WallCheckBtn.Size = UDim2.new(1,0,0,30)
    WallCheckBtn.BackgroundColor3 = Color3.fromRGB(0,150,0); WallCheckBtn.TextColor3 = Color3.new(1,1,1)

    local FOVFrame = Instance.new("Frame", Container); FOVFrame.Size = UDim2.new(1,0,0,30); FOVFrame.BackgroundTransparency = 1
    local FOVLayout = Instance.new("UIListLayout", FOVFrame); FOVLayout.FillDirection = Enum.FillDirection.Horizontal; FOVLayout.Padding = UDim.new(0,5)
    local FOVDecBtn = Instance.new("TextButton", FOVFrame); FOVDecBtn.Text = "-"; FOVDecBtn.Size = UDim2.new(0.48,0,1,0); FOVDecBtn.BackgroundColor3 = Color3.fromRGB(150,50,50)
    local FOVIncBtn = Instance.new("TextButton", FOVFrame); FOVIncBtn.Text = "+"; FOVIncBtn.Size = UDim2.new(0.48,0,1,0); FOVIncBtn.BackgroundColor3 = Color3.fromRGB(50,150,50)
    local FOVLabel = Instance.new("TextLabel", Container); FOVLabel.Text = "FOV: 150"; FOVLabel.Size = UDim2.new(1,0,0,20); FOVLabel.BackgroundTransparency = 1; FOVLabel.TextColor3 = Color3.new(0.8,0.8,0.8)

    CreateLabel("[ HITBOX ]")
    local HitBtn = Instance.new("TextButton", Container); HitBtn.Text = "Hitbox: Normal (2)"; HitBtn.Size = UDim2.new(1,0,0,40)
    HitBtn.BackgroundColor3 = Color3.fromRGB(150,50,50); HitBtn.TextColor3 = Color3.new(1,1,1); HitBtn.Font = Enum.Font.GothamBold

    local ToggleHitboxVisBtn = Instance.new("TextButton", Container); ToggleHitboxVisBtn.Text = "Ver Hitbox: ON"; ToggleHitboxVisBtn.Size = UDim2.new(1,0,0,30)
    ToggleHitboxVisBtn.BackgroundColor3 = Color3.fromRGB(0,150,0); ToggleHitboxVisBtn.TextColor3 = Color3.new(1,1,1)

    -- LOGICA
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

    CloseBtn.MouseButton1Click:Connect(function() MainFrame.Visible = false end)
    ToggleESPBtn.MouseButton1Click:Connect(function() 
        Config.ESPEnabled = not Config.ESPEnabled 
        ToggleESPBtn.Text = Config.ESPEnabled and "ESP: ON" or "ESP: OFF"
        ToggleESPBtn.BackgroundColor3 = Config.ESPEnabled and Color3.fromRGB(0,150,0) or Color3.fromRGB(50,50,50)
    end)
    HitBtn.MouseButton1Click:Connect(function()
        HitboxIndex = HitboxIndex + 1; if HitboxIndex > #HitboxSizes then HitboxIndex = 1 end
        Config.HitboxSize = HitboxSizes[HitboxIndex]
        HitBtn.Text = "Hitbox: " .. Config.HitboxSize
    end)
    ToggleHitboxVisBtn.MouseButton1Click:Connect(function()
        Config.ShowHitbox = not Config.ShowHitbox
        ToggleHitboxVisBtn.Text = Config.ShowHitbox and "Ver Hitbox: ON" or "Ver Hitbox: OFF"
        ToggleHitboxVisBtn.BackgroundColor3 = Config.ShowHitbox and Color3.fromRGB(0,150,0) or Color3.fromRGB(150,0,0)
    end)
    
    UserInputService.InputBegan:Connect(function(input, gp)
        if input.KeyCode == Enum.KeyCode.Insert then MainFrame.Visible = not MainFrame.Visible end
        if not gp and (input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey) then Config.IsAiming = true end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.KeyCode == Config.AimbotKey or input.UserInputType == Config.AimbotKey then Config.IsAiming = false end
    end)

    RunService.RenderStepped:Connect(function()
        FOVCircle.Size = UDim2.new(0, Config.FOVSize*2, 0, Config.FOVSize*2)
        FOVCircle.Position = UDim2.new(0, UserInputService:GetMouseLocation().X - Config.FOVSize, 0, UserInputService:GetMouseLocation().Y - Config.FOVSize)
        
        local Target = nil
        local MinDist = Config.FOVSize

        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                UpdateESP(p)
                if p.Character and IsEnemy(p) then
                    -- HITBOX
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
                    -- AIMBOT TARGET
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
