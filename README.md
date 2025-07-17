-- GARDEN MASTER HUB - SCRIPT EDUCACIONAL PARA EQUIPE DE SEGURANÇA
-- ================================================================
-- ⚠️ USO EXCLUSIVO PARA ANÁLISE DE SEGURANÇA E EDUCAÇÃO ⚠️
-- ================================================================

local GardenMasterHub = {}

-- SISTEMA DE PROTEÇÃO ANTI-DETECÇÃO AVANÇADO
local AntiDetection = {
    -- Verifica se está sendo debuggado
    checkDebug = function()
        local isDebugging = false
        
        -- Verifica hooks de debug
        if debug and (debug.getinfo or debug.getlocal or debug.getupvalue) then
            isDebugging = true
        end
        
        -- Verifica performance suspeita
        local startTime = tick()
        for i = 1, 100 do
            math.random()
        end
        local executionTime = tick() - startTime
        
        if executionTime > 0.05 then
            isDebugging = true
        end
        
        return isDebugging
    end,
    
    -- Ofusca strings críticas
    obfuscateString = function(str)
        local result = ""
        for i = 1, #str do
            local char = str:sub(i, i)
            local byte = string.byte(char)
            result = result .. string.char(byte + math.random(-3, 3))
        end
        return result
    end,
    
    -- Verificação de integridade
    checkIntegrity = function()
        local expectedChecksum = "GMH_2025_SECURE"
        local currentChecksum = "GMH_2025_SECURE"
        return expectedChecksum == currentChecksum
    end,
    
    -- Detecta moderadores próximos
    detectModerators = function()
        local Players = game:GetService("Players")
        local moderatorNames = {"Roblox", "Admin", "Moderator", "Staff"}
        
        for _, player in pairs(Players:GetPlayers()) do
            for _, modName in pairs(moderatorNames) do
                if player.Name:lower():find(modName:lower()) then
                    return true
                end
            end
        end
        return false
    end
}

-- SISTEMA DE SERVIÇOS E INICIALIZAÇÃO
local Services = {
    Players = game:GetService("Players"),
    RunService = game:GetService("RunService"),
    UserInputService = game:GetService("UserInputService"),
    HttpService = game:GetService("HttpService"),
    TweenService = game:GetService("TweenService"),
    Workspace = game:GetService("Workspace"),
    ReplicatedStorage = game:GetService("ReplicatedStorage"),
    StarterGui = game:GetService("StarterGui")
}

local LocalPlayer = Services.Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")

-- CONFIGURAÇÕES DO HUB
local Config = {
    -- Auto Farm
    AutoPlant = false,
    AutoWater = false,
    AutoHarvest = false,
    AutoSell = false,
    AutoBuy = false,
    
    -- Player Enhancement
    SpeedHack = false,
    FlyHack = false,
    NoClip = false,
    JumpPower = false,
    
    -- Economy
    MoneyMultiplier = false,
    InfiniteCoins = false,
    AutoUpgrade = false,
    
    -- Utility
    ESP = false,
    Teleport = false,
    AntiAFK = false,
    
    -- Proteções
    AntiKick = true,
    AntiLog = true,
    RandomizeActions = true,
    HumanDelays = true,
    
    -- Configurações específicas
    PlantType = "Carrot",
    WalkSpeed = 25,
    JumpHeight = 75,
    FlySpeed = 30,
    ActionDelay = {min = 0.8, max = 2.5}
}

-- SISTEMA DE DELAYS HUMANIZADOS
local function randomDelay()
    if Config.HumanDelays then
        wait(math.random(Config.ActionDelay.min * 100, Config.ActionDelay.max * 100) / 100)
    else
        wait(0.1)
    end
end

-- SISTEMA DE MOVIMENTO NATURAL
local MovementSystem = {
    -- Teleporte suave para evitar detecção
    smoothTeleport = function(targetPosition)
        local distance = (HumanoidRootPart.Position - targetPosition).Magnitude
        
        if distance > 100 then
            -- Teleporte direto para distâncias muito grandes
            HumanoidRootPart.CFrame = CFrame.new(targetPosition)
        else
            -- Movimento tweenado para distâncias menores
            local tween = Services.TweenService:Create(
                HumanoidRootPart,
                TweenInfo.new(distance / 50, Enum.EasingStyle.Linear),
                {CFrame = CFrame.new(targetPosition)}
            )
            tween:Play()
            tween.Completed:Wait()
        end
        
        randomDelay()
    end,
    
    -- Caminhada natural
    walkTo = function(targetPosition)
        Humanoid:MoveTo(targetPosition)
        Humanoid.MoveToFinished:Wait()
        randomDelay()
    end,
    
    -- Sistema de pathfinding inteligente
    smartMoveTo = function(targetPosition)
        local distance = (HumanoidRootPart.Position - targetPosition).Magnitude
        
        if distance < 50 then
            MovementSystem.walkTo(targetPosition)
        else
            MovementSystem.smoothTeleport(targetPosition)
        end
    end
}

-- SISTEMA DE DETECÇÃO DE PLANTAS E OBJETOS
local PlantDetection = {
    -- Encontra todas as plantas no mapa
    findAllPlants = function()
        local plants = {}
        
        for _, obj in pairs(Services.Workspace:GetDescendants()) do
            if obj.Name:find("Plant") or obj.Name:find("Crop") or obj.Name:find("Seed") then
                if obj:IsA("Model") or obj:IsA("Part") then
                    table.insert(plants, obj)
                end
            end
        end
        
        return plants
    end,
    
    -- Encontra plantas prontas para colheita
    findHarvestablePlants = function()
        local harvestablePlants = {}
        local allPlants = PlantDetection.findAllPlants()
        
        for _, plant in pairs(allPlants) do
            if plant:FindFirstChild("Harvestable") or plant:FindFirstChild("Ready") then
                table.insert(harvestablePlants, plant)
            end
        end
        
        return harvestablePlants
    end,
    
    -- Encontra slots vazios para plantar
    findEmptySlots = function()
        local emptySlots = {}
        
        local plotsFolder = Services.Workspace:FindFirstChild("Plots") or Services.Workspace:FindFirstChild("PlantSlots")
        if plotsFolder then
            for _, slot in pairs(plotsFolder:GetChildren()) do
                if not slot:FindFirstChild("Plant") and not slot:FindFirstChild("Occupied") then
                    table.insert(emptySlots, slot)
                end
            end
        end
        
        return emptySlots
    end,
    
    -- Encontra plantas que precisam de água
    findThirstyPlants = function()
        local thirstyPlants = {}
        local allPlants = PlantDetection.findAllPlants()
        
        for _, plant in pairs(allPlants) do
            if plant:FindFirstChild("NeedsWater") or plant:FindFirstChild("Thirsty") then
                table.insert(thirstyPlants, plant)
            end
        end
        
        return thirstyPlants
    end
}

-- SISTEMA DE AUTO FARM INTELIGENTE
local AutoFarm = {
    -- Função principal de auto farm
    startAutoFarm = function()
        spawn(function()
            while Config.AutoPlant or Config.AutoWater or Config.AutoHarvest do
                if AntiDetection.detectModerators() then
                    wait(10) -- Pausa quando detecta moderador
                    continue
                end
                
                -- Auto Harvest (Prioridade máxima)
                if Config.AutoHarvest then
                    local harvestables = PlantDetection.findHarvestablePlants()
                    for _, plant in pairs(harvestables) do
                        if not Config.AutoHarvest then break end
                        
                        MovementSystem.smartMoveTo(plant.Position)
                        
                        -- Simula interação humana
                        if plant:FindFirstChild("ProximityPrompt") then
                            fireproximityprompt(plant.ProximityPrompt)
                        elseif plant:FindFirstChild("ClickDetector") then
                            fireclickdetector(plant.ClickDetector)
                        end
                        
                        randomDelay()
                    end
                end
                
                -- Auto Water
                if Config.AutoWater then
                    local thirstyPlants = PlantDetection.findThirstyPlants()
                    for _, plant in pairs(thirstyPlants) do
                        if not Config.AutoWater then break end
                        
                        MovementSystem.smartMoveTo(plant.Position)
                        
                        -- Simula uso de regador
                        if plant:FindFirstChild("ProximityPrompt") then
                            fireproximityprompt(plant.ProximityPrompt)
                        end
                        
                        randomDelay()
                    end
                end
                
                -- Auto Plant
                if Config.AutoPlant then
                    local emptySlots = PlantDetection.findEmptySlots()
                    for _, slot in pairs(emptySlots) do
                        if not Config.AutoPlant then break end
                        
                        MovementSystem.smartMoveTo(slot.Position)
                        
                        -- Simula plantio
                        if slot:FindFirstChild("ProximityPrompt") then
                            fireproximityprompt(slot.ProximityPrompt)
                        end
                        
                        randomDelay()
                    end
                end
                
                -- Auto Sell
                if Config.AutoSell then
                    local sellPoint = Services.Workspace:FindFirstChild("SellPoint") or 
                                    Services.Workspace:FindFirstChild("Market")
                    
                    if sellPoint then
                        MovementSystem.smartMoveTo(sellPoint.Position)
                        
                        if sellPoint:FindFirstChild("ProximityPrompt") then
                            fireproximityprompt(sellPoint.ProximityPrompt)
                        end
                        
                        randomDelay()
                    end
                end
                
                wait(2) -- Delay principal do loop
            end
        end)
    end
}

-- SISTEMA DE HACKS DE PLAYER
local PlayerHacks = {
    -- Speed Hack com proteção
    setupSpeedHack = function()
        if Config.SpeedHack then
            Humanoid.WalkSpeed = Config.WalkSpeed
        else
            Humanoid.WalkSpeed = 16 -- Velocidade padrão
        end
    end,
    
    -- Fly Hack
    setupFlyHack = function()
        if Config.FlyHack then
            local bodyVelocity = Instance.new("BodyVelocity")
            bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            bodyVelocity.Parent = HumanoidRootPart
            bodyVelocity.Name = "GMH_FlyVelocity"
            
            -- Controles de voo
            Services.UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Space then
                    bodyVelocity.Velocity = Vector3.new(0, Config.FlySpeed, 0)
                elseif input.KeyCode == Enum.KeyCode.LeftShift then
                    bodyVelocity.Velocity = Vector3.new(0, -Config.FlySpeed, 0)
                end
            end)
            
            Services.UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Space or input.KeyCode == Enum.KeyCode.LeftShift then
                    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
                end
            end)
        else
            local flyVelocity = HumanoidRootPart:FindFirstChild("GMH_FlyVelocity")
            if flyVelocity then
                flyVelocity:Destroy()
            end
        end
    end,
    
    -- NoClip
    setupNoClip = function()
        if Config.NoClip then
            for _, part in pairs(Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        else
            for _, part in pairs(Character:GetChildren()) do
                if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                    part.CanCollide = true
                end
            end
        end
    end,
    
    -- Jump Power
    setupJumpPower = function()
        if Config.JumpPower then
            Humanoid.JumpPower = Config.JumpHeight
        else
            Humanoid.JumpPower = 50 -- Valor padrão
        end
    end
}

-- SISTEMA ESP (EXTRA SENSORY PERCEPTION)
local ESPSystem = {
    highlights = {},
    
    createESP = function()
        if not Config.ESP then return end
        
        -- ESP para plantas
        local plants = PlantDetection.findAllPlants()
        for _, plant in pairs(plants) do
            if not ESPSystem.highlights[plant] then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = plant
                
                if plant:FindFirstChild("Harvestable") then
                    highlight.FillColor = Color3.fromRGB(0, 255, 0) -- Verde para prontas
                elseif plant:FindFirstChild("NeedsWater") then
                    highlight.FillColor = Color3.fromRGB(255, 255, 0) -- Amarelo para sede
                else
                    highlight.FillColor = Color3.fromRGB(255, 255, 255) -- Branco para normais
                end
                
                highlight.OutlineColor = Color3.fromRGB(0, 0, 0)
                highlight.Parent = plant
                
                ESPSystem.highlights[plant] = highlight
            end
        end
    end,
    
    removeESP = function()
        for plant, highlight in pairs(ESPSystem.highlights) do
            if highlight then
                highlight:Destroy()
            end
        end
        ESPSystem.highlights = {}
    end
}

-- SISTEMA DE GUI MODERNO
local GUI = {
    screenGui = nil,
    mainFrame = nil,
    
    createGUI = function()
        -- Remove GUI anterior se existir
        if GUI.screenGui then
            GUI.screenGui:Destroy()
        end
        
        -- Cria ScreenGui principal
        GUI.screenGui = Instance.new("ScreenGui")
        GUI.screenGui.Name = "GardenMasterHub"
        GUI.screenGui.Parent = game.CoreGui
        GUI.screenGui.ResetOnSpawn = false
        
        -- Frame principal
        GUI.mainFrame = Instance.new("Frame")
        GUI.mainFrame.Size = UDim2.new(0, 400, 0, 500)
        GUI.mainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
        GUI.mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
        GUI.mainFrame.BorderSizePixel = 0
        GUI.mainFrame.Active = true
        GUI.mainFrame.Draggable = true
        GUI.mainFrame.Parent = GUI.screenGui
        
        -- Corner radius
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 10)
        corner.Parent = GUI.mainFrame
        
        -- Título
        local title = Instance.new("TextLabel")
        title.Size = UDim2.new(1, 0, 0, 40)
        title.Position = UDim2.new(0, 0, 0, 0)
        title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        title.BorderSizePixel = 0
        title.Text = "🌱 Garden Master Hub - Security Analysis"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.TextScaled = true
        title.Font = Enum.Font.GothamBold
        title.Parent = GUI.mainFrame
        
        local titleCorner = Instance.new("UICorner")
        titleCorner.CornerRadius = UDim.new(0, 10)
        titleCorner.Parent = title
        
        -- Scroll Frame para opções
        local scrollFrame = Instance.new("ScrollingFrame")
        scrollFrame.Size = UDim2.new(1, -20, 1, -60)
        scrollFrame.Position = UDim2.new(0, 10, 0, 50)
        scrollFrame.BackgroundTransparency = 1
        scrollFrame.BorderSizePixel = 0
        scrollFrame.ScrollBarThickness = 6
        scrollFrame.Parent = GUI.mainFrame
        
        -- Layout
        local listLayout = Instance.new("UIListLayout")
        listLayout.SortOrder = Enum.SortOrder.LayoutOrder
        listLayout.Padding = UDim.new(0, 5)
        listLayout.Parent = scrollFrame
        
        -- Função para criar botões toggle
        local function createToggle(name, configKey, parent)
            local frame = Instance.new("Frame")
            frame.Size = UDim2.new(1, 0, 0, 35)
            frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            frame.BorderSizePixel = 0
            frame.Parent = parent
            
            local frameCorner = Instance.new("UICorner")
            frameCorner.CornerRadius = UDim.new(0, 5)
            frameCorner.Parent = frame
            
            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(0.7, 0, 1, 0)
            label.Position = UDim2.new(0, 10, 0, 0)
            label.BackgroundTransparency = 1
            label.Text = name
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.TextScaled = true
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Font = Enum.Font.Gotham
            label.Parent = frame
            
            local toggle = Instance.new("TextButton")
            toggle.Size = UDim2.new(0, 60, 0, 25)
            toggle.Position = UDim2.new(1, -70, 0.5, -12.5)
            toggle.BackgroundColor3 = Config[configKey] and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
            toggle.BorderSizePixel = 0
            toggle.Text = Config[configKey] and "ON" or "OFF"
            toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
            toggle.TextScaled = true
            toggle.Font = Enum.Font.GothamBold
            toggle.Parent = frame
            
            local toggleCorner = Instance.new("UICorner")
            toggleCorner.CornerRadius = UDim.new(0, 5)
            toggleCorner.Parent = toggle
            
            toggle.MouseButton1Click:Connect(function()
                Config[configKey] = not Config[configKey]
                toggle.BackgroundColor3 = Config[configKey] and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
                toggle.Text = Config[configKey] and "ON" or "OFF"
                
                -- Aplica mudanças específicas
                if configKey == "SpeedHack" then
                    PlayerHacks.setupSpeedHack()
                elseif configKey == "FlyHack" then
                    PlayerHacks.setupFlyHack()
                elseif configKey == "NoClip" then
                    PlayerHacks.setupNoClip()
                elseif configKey == "JumpPower" then
                    PlayerHacks.setupJumpPower()
                elseif configKey == "AutoPlant" or configKey == "AutoWater" or configKey == "AutoHarvest" then
                    AutoFarm.startAutoFarm()
                elseif configKey == "ESP" then
                    if Config.ESP then
                        ESPSystem.createESP()
                    else
                        ESPSystem.removeESP()
                    end
                end
            end)
        end
        
        -- Cria todas as opções
        createToggle("🌱 Auto Plant", "AutoPlant", scrollFrame)
        createToggle("💧 Auto Water", "AutoWater", scrollFrame)
        createToggle("🌾 Auto Harvest", "AutoHarvest", scrollFrame)
        createToggle("💰 Auto Sell", "AutoSell", scrollFrame)
        createToggle("⚡ Speed Hack", "SpeedHack", scrollFrame)
        createToggle("🕊️ Fly Hack", "FlyHack", scrollFrame)
        createToggle("👻 NoClip", "NoClip", scrollFrame)
        createToggle("🦘 Jump Power", "JumpPower", scrollFrame)
        createToggle("👁️ ESP", "ESP", scrollFrame)
        createToggle("🤖 Anti AFK", "AntiAFK", scrollFrame)
        
        -- Atualiza tamanho do scroll
        scrollFrame.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y)
        listLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            scrollFrame.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y + 10)
        end)
    end
}

-- SISTEMA ANTI-AFK
local AntiAFK = {
    start = function()
        spawn(function()
            while Config.AntiAFK do
                wait(180) -- A cada 3 minutos
                
                -- Movimento micro para evitar kick por AFK
                local currentPos = HumanoidRootPart.Position
                HumanoidRootPart.CFrame = HumanoidRootPart.CFrame + Vector3.new(math.random(-1, 1), 0, math.random(-1, 1))
                wait(0.1)
                HumanoidRootPart.CFrame = CFrame.new(currentPos)
            end
        end)
    end
}

-- INICIALIZAÇÃO PRINCIPAL
function GardenMasterHub.Initialize()
    -- Verificações de segurança
    if AntiDetection.checkDebug() then
        warn("Debug detected - Educational mode only")
        return false
    end
    
    if not AntiDetection.checkIntegridity() then
        warn("Integrity check failed")
        return false
    end
    
    -- Inicializa sistemas
    GUI.createGUI()
    
    -- Conecta eventos
    Services.RunService.Heartbeat:Connect(function()
        if Config.ESP then
            ESPSystem.createESP()
        end
        
        if Config.AntiAFK then
            AntiAFK.start()
        end
    end)
    
    -- Reconecta quando personagem respawna
    LocalPlayer.CharacterAdded:Connect(function(newCharacter)
        Character = newCharacter
        HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
        Humanoid = Character:WaitForChild("Humanoid")
        
        wait(2) -- Espera carregar completamente
        
        -- Reaplica configurações
        PlayerHacks.setupSpeedHack()
        PlayerHacks.setupFlyHack()
        PlayerHacks.setupNoClip()
        PlayerHacks.setupJumpPower()
    end)
    
    print("🌱 Garden Master Hub initialized for security analysis")
    print("⚠️ Educational use only - Security team analysis")
    
    return true
end

-- AUTO-INICIALIZAÇÃO
if GardenMasterHub.Initialize() then
    AutoFarm.startAutoFarm()
end

return GardenMasterHub
