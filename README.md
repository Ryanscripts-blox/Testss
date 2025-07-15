-- Script Hub para Roblox - Inspirado no Xero Hub
-- Versão Final com ESP Corrigido e Robusto

--// SERVIÇOS
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TeleportService = game:GetService("TeleportService")

--// VARIÁVEIS LOCAIS
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

--// CONFIGURAÇÕES DO HUB
local hubConfig = {
    title = "x1x1x1 Hub",
    version = "v2.4 ESP Fixed",
    keybind = Enum.KeyCode.RightControl,
    theme = {
        background = Color3.fromRGB(25, 25, 25),
        sidebar = Color3.fromRGB(35, 35, 35),
        accent = Color3.fromRGB(64, 150, 255),
        text = Color3.fromRGB(255, 255, 255),
        textDim = Color3.fromRGB(180, 180, 180)
    }
}

--// -- CRIAÇÃO DA INTERFACE (GUI) -- //--

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "x1x1x1Hub"; screenGui.Parent = playerGui; screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Name = "MainFrame"; mainFrame.Size = UDim2.new(0, 800, 0, 500); mainFrame.Position = UDim2.new(0.5, -400, 0.5, -250)
mainFrame.BackgroundColor3 = hubConfig.theme.background; mainFrame.BorderSizePixel = 0; mainFrame.Visible = false; mainFrame.Active = true
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 8)

local titleBar = Instance.new("Frame", mainFrame)
titleBar.Name = "TitleBar"; titleBar.Size = UDim2.new(1, 0, 0, 40); titleBar.BackgroundColor3 = hubConfig.theme.sidebar; titleBar.BorderSizePixel = 0
Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 8)

local titleLabel = Instance.new("TextLabel", titleBar)
titleLabel.Name = "Title"; titleLabel.Size = UDim2.new(0, 200, 1, 0); titleLabel.Position = UDim2.new(0, 15, 0, 0); titleLabel.BackgroundTransparency = 1
titleLabel.Font = Enum.Font.GothamBold; titleLabel.TextSize = 16; titleLabel.TextColor3 = hubConfig.theme.text; titleLabel.TextXAlignment = Enum.TextXAlignment.Left; titleLabel.Text = hubConfig.title

local closeButton = Instance.new("TextButton", titleBar)
closeButton.Name = "CloseButton"; closeButton.Size = UDim2.new(0, 40, 0, 30); closeButton.Position = UDim2.new(1, -45, 0, 5); closeButton.BackgroundTransparency = 1
closeButton.Font = Enum.Font.Gotham; closeButton.TextSize = 18; closeButton.TextColor3 = hubConfig.theme.text; closeButton.Text = "×"

local sidebar = Instance.new("Frame", mainFrame)
sidebar.Name = "Sidebar"; sidebar.Size = UDim2.new(0, 200, 1, -40); sidebar.Position = UDim2.new(0, 0, 0, 40); sidebar.BackgroundColor3 = hubConfig.theme.sidebar; sidebar.BorderSizePixel = 0

local contentFrame = Instance.new("Frame", mainFrame)
contentFrame.Name = "ContentFrame"; contentFrame.Size = UDim2.new(1, -200, 1, -40); contentFrame.Position = UDim2.new(0, 200, 0, 40); contentFrame.BackgroundColor3 = hubConfig.theme.background; contentFrame.BorderSizePixel = 0

local sidebarScroll = Instance.new("ScrollingFrame", sidebar)
sidebarScroll.Name = "SidebarScroll"; sidebarScroll.Size = UDim2.new(1, 0, 1, 0); sidebarScroll.BackgroundTransparency = 1; sidebarScroll.BorderSizePixel = 0; sidebarScroll.ScrollBarThickness = 0

local sidebarLayout = Instance.new("UIListLayout", sidebarScroll)
sidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder; sidebarLayout.Padding = UDim.new(0, 2)

--// -- FUNÇÕES DE CRIAÇÃO DE ELEMENTOS (MODIFICADAS) -- //--

local function createSidebarButton(text, icon, layoutOrder)
    local button = Instance.new("TextButton", sidebarScroll)
    button.Name = text .. "Button"; button.Size = UDim2.new(1, 0, 0, 45); button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    button.BorderSizePixel = 0; button.Font = Enum.Font.Gotham; button.TextSize = 14; button.TextColor3 = hubConfig.theme.textDim
    button.TextXAlignment = Enum.TextXAlignment.Left; button.Text = "  " .. (icon or "○") .. "  " .. text; button.LayoutOrder = layoutOrder
    button.MouseEnter:Connect(function() TweenService:Create(button, TweenInfo.new(0.2), { BackgroundColor3 = Color3.fromRGB(50, 50, 50) }):Play() end)
    button.MouseLeave:Connect(function() if button.BackgroundColor3 ~= hubConfig.theme.accent then TweenService:Create(button, TweenInfo.new(0.2), { BackgroundColor3 = Color3.fromRGB(40, 40, 40) }):Play() end end)
    return button
end

local function createContentPage(name)
    local page = Instance.new("Frame", contentFrame)
    page.Name = name .. "Page"; page.Size = UDim2.new(1, 0, 1, 0); page.BackgroundTransparency = 1; page.Visible = false
    local pageScroll = Instance.new("ScrollingFrame", page)
    pageScroll.Size = UDim2.new(1, -20, 1, -20); pageScroll.Position = UDim2.new(0, 10, 0, 10); pageScroll.BackgroundTransparency = 1
    pageScroll.BorderSizePixel = 0; pageScroll.ScrollBarThickness = 6; pageScroll.ScrollBarImageColor3 = hubConfig.theme.accent
    local pageLayout = Instance.new("UIListLayout", pageScroll)
    pageLayout.SortOrder = Enum.SortOrder.LayoutOrder; pageLayout.Padding = UDim.new(0, 10)
    return page, pageScroll
end

local function createToggle(parent, text, defaultState, callback)
    local toggleFrame = Instance.new("Frame", parent)
    toggleFrame.Size = UDim2.new(1, 0, 0, 40); toggleFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40); toggleFrame.BorderSizePixel = 0
    Instance.new("UICorner", toggleFrame).CornerRadius = UDim.new(0, 6)
    local toggleLabel = Instance.new("TextLabel", toggleFrame)
    toggleLabel.Size = UDim2.new(1, -60, 1, 0); toggleLabel.Position = UDim2.new(0, 15, 0, 0); toggleLabel.BackgroundTransparency = 1
    toggleLabel.Font = Enum.Font.Gotham; toggleLabel.TextSize = 14; toggleLabel.TextColor3 = hubConfig.theme.text; toggleLabel.TextXAlignment = Enum.TextXAlignment.Left; toggleLabel.Text = text
    local toggleButton = Instance.new("TextButton", toggleFrame)
    toggleButton.Size = UDim2.new(0, 35, 0, 20); toggleButton.Position = UDim2.new(1, -45, 0.5, -10); toggleButton.BackgroundColor3 = defaultState and hubConfig.theme.accent or Color3.fromRGB(60, 60, 60)
    toggleButton.BorderSizePixel = 0; toggleButton.Text = ""
    Instance.new("UICorner", toggleButton).CornerRadius = UDim.new(0, 10)
    local toggleState = defaultState
    toggleButton.MouseButton1Click:Connect(function()
        toggleState = not toggleState
        TweenService:Create(toggleButton, TweenInfo.new(0.2), { BackgroundColor3 = toggleState and hubConfig.theme.accent or Color3.fromRGB(60, 60, 60) }):Play()
        if callback then pcall(callback, toggleState) end
    end)
    return toggleFrame
end

local function createActionButton(parent, text, color, callback)
    local buttonFrame = Instance.new("Frame", parent)
    buttonFrame.Size = UDim2.new(1, 0, 0, 40); buttonFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40); buttonFrame.BorderSizePixel = 0
    Instance.new("UICorner", buttonFrame).CornerRadius = UDim.new(0, 6)
    local button = Instance.new("TextButton", buttonFrame)
    button.Size = UDim2.new(1, -20, 1, -10); button.Position = UDim2.new(0, 10, 0, 5); button.BackgroundColor3 = color or hubConfig.theme.accent
    button.BorderSizePixel = 0; button.Font = Enum.Font.GothamBold; button.TextSize = 14; button.TextColor3 = Color3.fromRGB(255, 255, 255); button.Text = text
    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 4)
    button.MouseButton1Click:Connect(function() if callback then pcall(callback) end end)
    return button
end

local function createSlider(parent, text, min, max, default, callback)
    local sliderFrame = Instance.new("Frame", parent)
    sliderFrame.Size = UDim2.new(1, 0, 0, 60); sliderFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40); sliderFrame.BorderSizePixel = 0
    Instance.new("UICorner", sliderFrame).CornerRadius = UDim.new(0, 6)
    local sliderLabel = Instance.new("TextLabel", sliderFrame)
    sliderLabel.Size = UDim2.new(1, -60, 0, 25); sliderLabel.Position = UDim2.new(0, 15, 0, 5); sliderLabel.BackgroundTransparency = 1
    sliderLabel.Font = Enum.Font.Gotham; sliderLabel.TextSize = 14; sliderLabel.TextColor3 = hubConfig.theme.text; sliderLabel.TextXAlignment = Enum.TextXAlignment.Left; sliderLabel.Text = text
    local valueLabel = Instance.new("TextLabel", sliderFrame)
    valueLabel.Size = UDim2.new(0, 50, 0, 25); valueLabel.Position = UDim2.new(1, -65, 0, 5); valueLabel.BackgroundTransparency = 1
    valueLabel.Font = Enum.Font.Gotham; valueLabel.TextSize = 12; valueLabel.TextColor3 = hubConfig.theme.textDim; valueLabel.Text = tostring(math.floor(default))
    local sliderBG = Instance.new("Frame", sliderFrame)
    sliderBG.Size = UDim2.new(1, -30, 0, 6); sliderBG.Position = UDim2.new(0, 15, 0, 35); sliderBG.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    Instance.new("UICorner", sliderBG).CornerRadius = UDim.new(0, 3)
    local sliderFill = Instance.new("Frame", sliderBG)
    sliderFill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0); sliderFill.BackgroundColor3 = hubConfig.theme.accent
    Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(0, 3)
    local isDragging = false
    local sliderButton = Instance.new("TextButton", sliderFrame)
    sliderButton.Size = UDim2.new(1, 0, 1, 0); sliderButton.BackgroundTransparency = 1; sliderButton.Text = ""
    local function updateSlider(input)
        local relativePos = math.clamp((input.Position.X - sliderBG.AbsolutePosition.X) / sliderBG.AbsoluteSize.X, 0, 1)
        local currentValue = min + (max - min) * relativePos
        valueLabel.Text = tostring(math.floor(currentValue))
        sliderFill.Size = UDim2.new(relativePos, 0, 1, 0)
        if callback then pcall(callback, currentValue) end
    end
    sliderButton.MouseButton1Down:Connect(function() isDragging = true; updateSlider(UserInputService:GetMouseLocation()) end)
    UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then isDragging = false end end)
    UserInputService.InputChanged:Connect(function(input) if isDragging and input.UserInputType == Enum.UserInputType.MouseMovement then updateSlider(input) end end)
    return sliderFrame
end

local function createDropdown(parent, text, options, default)
    local dropdownFrame = Instance.new("Frame", parent)
    dropdownFrame.Size = UDim2.new(1, 0, 0, 40); dropdownFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40); dropdownFrame.BorderSizePixel = 0
    Instance.new("UICorner", dropdownFrame).CornerRadius = UDim.new(0, 6)
    local dropdownLabel = Instance.new("TextLabel", dropdownFrame)
    dropdownLabel.Size = UDim2.new(0, 120, 1, 0); dropdownLabel.Position = UDim2.new(0, 15, 0, 0); dropdownLabel.BackgroundTransparency = 1
    dropdownLabel.Font = Enum.Font.Gotham; dropdownLabel.TextSize = 14; dropdownLabel.TextColor3 = hubConfig.theme.text; dropdownLabel.TextXAlignment = Enum.TextXAlignment.Left; dropdownLabel.Text = text
    local dropdownButton = Instance.new("TextButton", dropdownFrame)
    dropdownButton.Size = UDim2.new(0, 120, 0, 25); dropdownButton.Position = UDim2.new(1, -135, 0.5, -12.5); dropdownButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    dropdownButton.BorderSizePixel = 0; dropdownButton.Font = Enum.Font.Gotham; dropdownButton.TextSize = 12; dropdownButton.TextColor3 = hubConfig.theme.text; dropdownButton.Text = default .. " ▼"
    Instance.new("UICorner", dropdownButton).CornerRadius = UDim.new(0, 4)
    return dropdownFrame
end

--// -- CRIAÇÃO DE PÁGINAS E CONTEÚDO -- //--
local mainPage, mainScroll = createContentPage("Main")
local configPage, configScroll = createContentPage("Config")
local creditsPage, creditsScroll = createContentPage("Credits")
local buttons = { main = createSidebarButton("Main", "🏠", 1), config = createSidebarButton("Configurações", "⚙️", 2), credits = createSidebarButton("Créditos", "👥", 3) }
local pages = { main = mainPage, config = configPage, credits = creditsPage }
local function showPage(pageName)
    for name, page in pairs(pages) do
        page.Visible = false
        TweenService:Create(buttons[name], TweenInfo.new(0.2), { BackgroundColor3 = Color3.fromRGB(40, 40, 40), TextColor3 = hubConfig.theme.textDim }):Play()
    end
    pages[pageName].Visible = true
    TweenService:Create(buttons[pageName], TweenInfo.new(0.2), { BackgroundColor3 = hubConfig.theme.accent, TextColor3 = hubConfig.theme.text }):Play()
end
for name, button in pairs(buttons) do button.MouseButton1Click:Connect(function() showPage(name) end) end

--// -- [PÁGINA MAIN] - LÓGICA E CONTEÚDO -- //--

local welcomeLabel = Instance.new("TextLabel", mainScroll)
welcomeLabel.Size = UDim2.new(1, 0, 0, 60); welcomeLabel.BackgroundTransparency = 1; welcomeLabel.Font = Enum.Font.GothamBold
welcomeLabel.TextSize = 20; welcomeLabel.TextColor3 = hubConfig.theme.text; welcomeLabel.Text = "Bem-vindo ao x1x1x1 Hub!"
local infoLabel = Instance.new("TextLabel", mainScroll)
infoLabel.Size = UDim2.new(1, 0, 0, 60); infoLabel.BackgroundTransparency = 1; infoLabel.Font = Enum.Font.Gotham
infoLabel.TextSize = 14; infoLabel.TextColor3 = hubConfig.theme.textDim; infoLabel.TextWrapped = true
infoLabel.Text = "Este é o x1x1x1 Hub - um hub moderno e funcional para Roblox.\nNavegue pelas páginas usando o menu lateral."
local mainTitle = Instance.new("TextLabel", mainScroll)
mainTitle.Size = UDim2.new(1, 0, 0, 40); mainTitle.BackgroundTransparency = 1; mainTitle.Font = Enum.Font.GothamBold
mainTitle.TextSize = 16; mainTitle.TextColor3 = hubConfig.theme.text; mainTitle.Text = "Funcionalidades Principais"

createActionButton(mainScroll, "🔄 Server Hop", Color3.fromRGB(255, 149, 0), function() pcall(TeleportService.Teleport, TeleportService, game.PlaceId) end)

local godModeActive = false
createToggle(mainScroll, "🛡️ God Mode", false, function(state) godModeActive = state; local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid"); if humanoid then humanoid.MaxHealth = state and math.huge or 100 end end)
RunService.Heartbeat:Connect(function() if godModeActive then local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid"); if humanoid then humanoid.Health = humanoid.MaxHealth end end end)

local infiniteJumpActive = false
createToggle(mainScroll, "🦘 Infinite Jump", false, function(state) infiniteJumpActive = state end)
UserInputService.JumpRequest:Connect(function() if infiniteJumpActive then local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid"); if humanoid then humanoid:ChangeState(Enum.HumanoidStateType.Jumping) end end end)

-- LÓGICA DO ESP CORRIGIDA E ROBUSTA
local espActive = false
local activeHighlights = {}
createToggle(mainScroll, "👁️ ESP Highlight", false, function(state)
    espActive = state
    -- Se desativado, limpa todos os highlights existentes de uma vez.
    if not espActive then
        for _, highlight in pairs(activeHighlights) do
            if highlight and highlight.Parent then highlight:Destroy() end
        end
        activeHighlights = {}
    end
end)

RunService.RenderStepped:Connect(function()
    if not espActive then return end -- Se estiver desligado, não faz nada.

    local playersInGame = {} -- Tabela para rastrear quem está válido *neste frame*.

    -- 1. Identificar todos os alvos válidos.
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character then
            playersInGame[p] = p.Character -- Armazena o personagem atual.
        end
    end

    -- 2. Garantir que todos os alvos válidos tenham um highlight.
    for p, char in pairs(playersInGame) do
        local existingHighlight = activeHighlights[p]
        if not existingHighlight or not existingHighlight.Parent then
            -- Se não existe ou foi destruído, cria um novo.
            if existingHighlight then existingHighlight:Destroy() end -- Limpa referência antiga
            
            local newHighlight = Instance.new("Highlight", char)
            newHighlight.FillColor = hubConfig.theme.accent
            newHighlight.OutlineColor = Color3.fromRGB(255, 255, 255)
            newHighlight.FillTransparency = 0.7
            newHighlight.OutlineTransparency = 0.2
            newHighlight.DepthMode = Enum.DepthMode.AlwaysOnTop
            activeHighlights[p] = newHighlight
        elseif existingHighlight.Parent ~= char then
            -- Se existe, mas está no personagem antigo (respawn), move para o novo.
            existingHighlight.Parent = char
        end
    end

    -- 3. Limpar highlights de jogadores que não são mais válidos.
    for p, highlight in pairs(activeHighlights) do
        if not playersInGame[p] then -- Se o jogador não está na lista de válidos (saiu, morreu, etc.)
            if highlight and highlight.Parent then
                highlight:Destroy()
            end
            activeHighlights[p] = nil -- Remove da nossa tabela de controle.
        end
    end
end)


local flyActive = false; local bodyGyro, bodyVelocity = nil, nil
createToggle(mainScroll, "✈️ Fly", false, function(state)
    flyActive = state; local char = player.Character; local humanoid = char and char:FindFirstChildOfClass("Humanoid"); local rootPart = char and char:FindFirstChild("HumanoidRootPart")
    if flyActive and rootPart and humanoid then
        humanoid.PlatformStand = true; bodyGyro = Instance.new("BodyGyro", rootPart); bodyGyro.P = 50000; bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9); bodyGyro.CFrame = rootPart.CFrame
        bodyVelocity = Instance.new("BodyVelocity", rootPart); bodyVelocity.Velocity = Vector3.new(0,0,0); bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    else
        if humanoid then humanoid.PlatformStand = false end
        if bodyGyro then bodyGyro:Destroy(); bodyGyro = nil end
        if bodyVelocity then bodyVelocity:Destroy(); bodyVelocity = nil end
    end
end)
RunService.RenderStepped:Connect(function()
    if flyActive and bodyGyro and bodyVelocity then
        local cam = workspace.CurrentCamera; bodyGyro.CFrame = cam.CFrame; local speed = 50; local direction = Vector3.new()
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then direction += cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then direction -= cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then direction -= cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then direction += cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then direction += Vector3.new(0, 1, 0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then direction -= Vector3.new(0, 1, 0) end
        bodyVelocity.Velocity = direction.Magnitude > 0 and direction.Unit * speed or Vector3.new()
    end
end)

createSlider(mainScroll, "Velocidade", 16, 500, 16, function(value) local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid"); if humanoid then humanoid.WalkSpeed = value end end)

--// -- [PÁGINAS CONFIG & CRÉDITOS] - CONTEÚDO ORIGINAL -- //--
local configTitle = Instance.new("TextLabel", configScroll); configTitle.Size = UDim2.new(1,0,0,40); configTitle.BackgroundTransparency = 1; configTitle.Font = Enum.Font.GothamBold; configTitle.TextSize = 18; configTitle.TextColor3 = hubConfig.theme.text; configTitle.Text = "Configurações do Hub"
createSlider(configScroll, "Transparência da Interface", 0, 100, 0, nil); createDropdown(configScroll, "Tema", {"Escuro", "Claro", "Azul"}, "Escuro"); createToggle(configScroll, "Mostrar Notificações", true, nil); createToggle(configScroll, "Som nos Botões", false, nil); createDropdown(configScroll, "Keybind para Abrir", {"RightControl", "LeftControl", "Insert", "Home"}, "RightControl")

local creditsTitle = Instance.new("TextLabel", creditsScroll); creditsTitle.Size = UDim2.new(1,0,0,50); creditsTitle.BackgroundTransparency = 1; creditsTitle.Font = Enum.Font.GothamBold; creditsTitle.TextSize = 24; creditsTitle.TextColor3 = hubConfig.theme.text; creditsTitle.Text = "x1x1x1 Hub"
local creditsVersion = Instance.new("TextLabel", creditsScroll); creditsVersion.Size = UDim2.new(1,0,0,30); creditsVersion.BackgroundTransparency = 1; creditsVersion.Font = Enum.Font.Gotham; creditsVersion.TextSize = 14; creditsVersion.TextColor3 = hubConfig.theme.textDim; creditsVersion.Text = "Versão " .. hubConfig.version
local creditsDesc = Instance.new("TextLabel", creditsScroll); creditsDesc.Size = UDim2.new(1,0,0,60); creditsDesc.BackgroundTransparency = 1; creditsDesc.Font = Enum.Font.Gotham; creditsDesc.TextSize = 14; creditsDesc.TextColor3 = hubConfig.theme.textDim; creditsDesc.TextWrapped = true; creditsDesc.Text = "Um hub moderno e funcional para Roblox, desenvolvido com foco em performance e usabilidade."
local devTitle = Instance.new("TextLabel", creditsScroll); devTitle.Size = UDim2.new(1,0,0,40); devTitle.BackgroundTransparency = 1; devTitle.Font = Enum.Font.GothamBold; devTitle.TextSize = 16; devTitle.TextColor3 = hubConfig.theme.text; devTitle.Text = "Desenvolvedor Principal"
local devFrame = Instance.new("Frame", creditsScroll); devFrame.Size = UDim2.new(1,0,0,60); devFrame.BackgroundColor3 = Color3.fromRGB(40,40,40); devFrame.BorderSizePixel = 0; Instance.new("UICorner", devFrame).CornerRadius = UDim.new(0, 6)
local devIcon = Instance.new("TextLabel", devFrame); devIcon.Size = UDim2.new(0,40,0,40); devIcon.Position = UDim2.new(0,10,0,10); devIcon.BackgroundColor3 = hubConfig.theme.accent; devIcon.Font = Enum.Font.GothamBold; devIcon.TextSize = 18; devIcon.TextColor3 = Color3.fromRGB(255,255,255); devIcon.Text = "👨‍💻"; Instance.new("UICorner", devIcon).CornerRadius = UDim.new(0, 20)
local devName = Instance.new("TextLabel", devFrame); devName.Size = UDim2.new(1,-70,0,25); devName.Position = UDim2.new(0,60,0,10); devName.BackgroundTransparency = 1; devName.Font = Enum.Font.GothamBold; devName.TextSize = 14; devName.TextColor3 = hubConfig.theme.text; devName.TextXAlignment = Enum.TextXAlignment.Left; devName.Text = "x1x1x1"
local devRole = Instance.new("TextLabel", devFrame); devRole.Size = UDim2.new(1,-70,0,20); devRole.Position = UDim2.new(0,60,0,30); devRole.BackgroundTransparency = 1; devRole.Font = Enum.Font.Gotham; devRole.TextSize = 12; devRole.TextColor3 = hubConfig.theme.textDim; devRole.TextXAlignment = Enum.TextXAlignment.Left; devRole.Text = "Desenvolvedor & Designer"

--// -- CONTROLES DO HUB -- //--
local dragging, dragStart, startPos = false, nil, nil
titleBar.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging, dragStart, startPos = true, input.Position, mainFrame.Position end end)
titleBar.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then local delta = input.Position - dragStart; mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y) end end)
titleBar.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
local function toggleHub(visible)
    if visible then
        mainFrame.Visible = true; mainFrame.Size = UDim2.new(0,0,0,0); mainFrame.Position = UDim2.new(0.5,0,0.5,0)
        TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), { Size = UDim2.new(0, 800, 0, 500), Position = UDim2.new(0.5, -400, 0.5, -250) }):Play()
    else
        TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.In), { Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0.5, 0, 0.5, 0) }):Play()
        task.wait(0.3); mainFrame.Visible = false
    end
end
closeButton.MouseButton1Click:Connect(function() toggleHub(false) end)
UserInputService.InputBegan:Connect(function(input, gameProcessed) if not gameProcessed and input.KeyCode == hubConfig.keybind then toggleHub(not mainFrame.Visible) end end)

--// -- INICIALIZAÇÃO -- //--
showPage("main")
toggleHub(true)
print("x1x1x1 Hub carregado com sucesso! Pressione " .. hubConfig.keybind.Name .. " para abrir/fechar.")
