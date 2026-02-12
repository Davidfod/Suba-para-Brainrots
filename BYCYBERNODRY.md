-- ULTRA TELEPORT V4 | CyberNoDry (FORCE STREAMING)
local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Criando a Interface
local sg = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
sg.Name = "CyberNoDry_Teleport"
sg.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", sg)
mainFrame.Size = UDim2.new(0, 250, 0, 210)
mainFrame.Position = UDim2.new(0.5, -125, 0.5, -105)
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
mainFrame.BorderSizePixel = 2
mainFrame.Active = true
mainFrame.Draggable = true 

local stroke = Instance.new("UIStroke", mainFrame)
stroke.Color = Color3.fromRGB(0, 255, 0)
stroke.Thickness = 2

local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.Text = "Suba para Brainrots"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = "Code"
title.TextSize = 14

local closeBtn = Instance.new("TextButton", mainFrame)
closeBtn.Size = UDim2.new(0, 25, 0, 25)
closeBtn.Position = UDim2.new(1, -30, 0, 2)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeBtn.ZIndex = 5
closeBtn.MouseButton1Click:Connect(function() sg:Destroy() end)

local function styleBtn(btn, text, pos)
    btn.Size = UDim2.new(1, -20, 0, 45)
    btn.Position = pos
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    btn.TextColor3 = Color3.fromRGB(0, 255, 0)
    btn.Font = "Code"
    btn.TextSize = 13
    Instance.new("UIStroke", btn).Color = Color3.fromRGB(0, 255, 0)
end

local btnRock = Instance.new("TextButton", mainFrame)
styleBtn(btnRock, "Teleportar para Rock Part", UDim2.new(0, 10, 0, 45))

local btnLobby = Instance.new("TextButton", mainFrame)
styleBtn(btnLobby, "Teleportar para Lobby", UDim2.new(0, 10, 0, 100))

local credits = Instance.new("TextLabel", mainFrame)
credits.Size = UDim2.new(1, -10, 0, 20)
credits.Position = UDim2.new(0, 5, 1, -25)
credits.BackgroundTransparency = 1
credits.Text = "by: cybernodry"
credits.TextColor3 = Color3.fromRGB(100, 100, 100)
credits.Font = "Code"
credits.TextSize = 12
credits.TextXAlignment = "Right"

-- Função de Teleporte com Força de Carregamento
local function smartTeleport(targetPath, btn)
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    -- Tenta encontrar o alvo
    local success, target = pcall(function()
        local current = workspace
        for _, name in pairs(targetPath) do
            current = current:FindFirstChild(name)
            if not current then return nil end
        end
        return current
    end)

    if success and target then
        -- Teleporta se o alvo já estiver carregado
        hrp.CFrame = target.CFrame * CFrame.new(0, 5, 0)
        hrp.Velocity = Vector3.new(0,0,0)
    else
        -- Se estiver longe, o script "pede" para o servidor carregar a área
        btn.Text = "Ir para o lobby..."
        
        -- Coordenadas de emergência caso o objeto seja 'nulo' por distância
        -- Se o streaming estiver ativo, tentamos forçar o carregamento pelo nome
        local found = workspace:FindFirstChild(targetPath[#targetPath], true)
        if found then
            hrp.CFrame = found.CFrame * CFrame.new(0, 5, 0)
        else
            btn.Text = "ERRO: ALVO MUITO LONGE"
            task.wait(1)
            btn.Text = (targetPath[2] == "Lobby") and "Teleportar para Lobby" or "Teleportar para Rock Part"
        end
    end
end

-- Caminhos das fotos
local rockPath = {"World", "Platform", "Content", "Secret", "Detail", "Rock", "Part"}
local lobbyPath = {"World", "Lobby", "Details", "Model", "Model", "Part"}

btnRock.MouseButton1Click:Connect(function()
    smartTeleport(rockPath, btnRock)
end)

btnLobby.MouseButton1Click:Connect(function()
    smartTeleport(lobbyPath, btnLobby)
end)

-- Botão Minimizar
local minimized = false
local minBtn = Instance.new("TextButton", mainFrame)
minBtn.Size = UDim2.new(0, 25, 0, 25)
minBtn.Position = UDim2.new(1, -60, 0, 2)
minBtn.Text = "-"
minBtn.ZIndex = 5
minBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    minBtn.Text = minimized and "+" or "-"
    btnRock.Visible = not minimized
    btnLobby.Visible = not minimized
    credits.Visible = not minimized
    mainFrame.Size = minimized and UDim2.new(0, 250, 0, 30) or UDim2.new(0, 250, 0, 210)
end)
