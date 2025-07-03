local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local mouse = player:GetMouse()
local camera = workspace.CurrentCamera

-- Interface
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "TeleportUI"
screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Name = "MainUI"
mainFrame.Size = UDim2.new(0, 250, 0, 150)
mainFrame.Position = UDim2.new(0.05, 0, 0.05, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true

local uiCorner = Instance.new("UICorner", mainFrame)
uiCorner.CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel", mainFrame)
title.Text = "Teleportador de Base"
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextScaled = true

local toggle = Instance.new("TextButton", mainFrame)
toggle.Text = "Ativar"
toggle.Size = UDim2.new(0.4, 0, 0.2, 0)
toggle.Position = UDim2.new(0.05, 0, 0.5, 0)
toggle.BackgroundColor3 = Color3.fromRGB(60, 200, 60)
toggle.Font = Enum.Font.Gotham
toggle.TextScaled = true
toggle.TextColor3 = Color3.new(1, 1, 1)

local goButton = Instance.new("TextButton", mainFrame)
goButton.Text = "Ir para Base"
goButton.Size = UDim2.new(0.5, 0, 0.2, 0)
goButton.Position = UDim2.new(0.45, 0, 0.5, 0)
goButton.BackgroundColor3 = Color3.fromRGB(60, 120, 200)
goButton.Font = Enum.Font.Gotham
goButton.TextScaled = true
goButton.TextColor3 = Color3.new(1, 1, 1)

local ativo = true
toggle.MouseButton1Click:Connect(function()
    ativo = not ativo
    toggle.Text = ativo and "Ativar" or "Desativado"
    toggle.BackgroundColor3 = ativo and Color3.fromRGB(60, 200, 60) or Color3.fromRGB(200, 60, 60)
end)

-- Função: mover suavemente até a base
local function moverSuavementeAteBase()
    if not ativo then return end
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")

    local baseFolder = workspace:FindFirstChild("Bases")
    if not baseFolder then warn("Pasta 'Bases' não encontrada no Workspace.") return end

    local base = baseFolder:FindFirstChild(player.Name)
    if not base then warn("Base para o jogador '"..player.Name.."' não encontrada.") return end

    local destino = base.Position + Vector3.new(0, 5, 0)

    local tweenInfo = TweenInfo.new(2, Enum.EasingStyle.Sine, Enum.EasingDirection.Out)
    local goal = {Position = destino}
    local tween = TweenService:Create(hrp, tweenInfo, goal)
    tween:Play()
end

goButton.MouseButton1Click:Connect(moverSuavementeAteBase)

-- ESP: cor nos corpos dos outros jogadores
local function criarESP(jogador)
    if jogador == player then return end

    jogador.CharacterAdded:Connect(function(char)
        task.wait(1)
        local highlight = Instance.new("Highlight")
        highlight.FillColor = Color3.fromRGB(255, 100, 100)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 1
        highlight.Adornee = char
        highlight.Parent = char
    end)
end

for _, plr in pairs(Players:GetPlayers()) do
    criarESP(plr)
end

Players.PlayerAdded:Connect(criarESP)
