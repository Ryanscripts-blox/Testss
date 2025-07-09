-- X1X1X1 Hop - Script de Server Hop
-- Este script permite trocar de servidor rapidamente

local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local StarterGui = game:GetService("StarterGui")

local player = Players.LocalPlayer
local placeId = game.PlaceId

-- Função para mostrar notificação
local function showNotification(title, text, duration)
    StarterGui:SetCore("SendNotification", {
        Title = title;
        Text = text;
        Duration = duration or 5;
    })
end

-- Função principal de server hop
local function serverHop()
    showNotification("X1X1X1 Hop", "Procurando novo servidor...", 3)
    
    -- Tentar obter lista de servidores
    local success, result = pcall(function()
        local servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. placeId .. "/servers/Public?sortOrder=Asc&limit=100"))
        return servers
    end)
    
    if success and result and result.data then
        local servers = result.data
        local currentJobId = game.JobId
        
        -- Filtrar servidores disponíveis (não o atual)
        local availableServers = {}
        for _, server in pairs(servers) do
            if server.id ~= currentJobId and server.playing < server.maxPlayers then
                table.insert(availableServers, server)
            end
        end
        
        if #availableServers > 0 then
            -- Selecionar servidor aleatório
            local randomServer = availableServers[math.random(1, #availableServers)]
            
            showNotification("X1X1X1 Hop", "Conectando ao novo servidor...", 2)
            
            -- Teleportar para o novo servidor
            TeleportService:TeleportToPlaceInstance(placeId, randomServer.id, player)
        else
            showNotification("X1X1X1 Hop", "Nenhum servidor disponível encontrado!", 5)
        end
    else
        -- Fallback: teleportar para um servidor aleatório
        showNotification("X1X1X1 Hop", "Usando método alternativo...", 3)
        TeleportService:Teleport(placeId, player)
    end
end

-- Função para criar GUI
local function createGUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "X1X1X1HopGUI"
    screenGui.Parent = player:WaitForChild("PlayerGui")
    
    -- Frame principal
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 200, 0, 100)
    frame.Position = UDim2.new(0, 10, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    frame.BorderSizePixel = 0
    frame.Parent = screenGui
    
    -- Corner para deixar arredondado
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = frame
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 30)
    title.Position = UDim2.new(0, 0, 0, 0)
    title.BackgroundTransparency = 1
    title.Text = "X1X1X1 Hop"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextScaled = true
    title.Font = Enum.Font.SourceSansBold
    title.Parent = frame
    
    -- Botão de Server Hop
    local hopButton = Instance.new("TextButton")
    hopButton.Size = UDim2.new(0.9, 0, 0, 40)
    hopButton.Position = UDim2.new(0.05, 0, 0, 50)
    hopButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
    hopButton.BorderSizePixel = 0
    hopButton.Text = "Server Hop"
    hopButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    hopButton.TextScaled = true
    hopButton.Font = Enum.Font.SourceSansBold
    hopButton.Parent = frame
    
    -- Corner do botão
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 6)
    buttonCorner.Parent = hopButton
    
    -- Evento do botão
    hopButton.MouseButton1Click:Connect(function()
        serverHop()
    end)
    
    -- Efeito hover
    hopButton.MouseEnter:Connect(function()
        hopButton.BackgroundColor3 = Color3.fromRGB(255, 120, 120)
    end)
    
    hopButton.MouseLeave:Connect(function()
        hopButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
    end)
    
    -- Tornar o frame arrastável
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
        end
    end)
    
    frame.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    
    frame.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    showNotification("X1X1X1 Hop", "Script carregado com sucesso!", 3)
end

-- Inicializar o script
createGUI()

-- Comando de chat para server hop (opcional)
player.Chatted:Connect(function(message)
    if message:lower() == "/hop" or message:lower() == "/serverhop" then
        serverHop()
    end
end)
