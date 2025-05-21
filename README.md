-- carregar biblioteca 
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- aviso ao executar
Fluent:Notify({ Title = "executado!", Content = "executando com sucesso" })


local Window = Fluent:CreateWindow({
    Title = "Cartola Hub - Brookhaven " .. Fluent.Version,
    TabWidth = 160, 
    Size = UDim2.fromOffset(460, 350), 
    Theme = "Dark"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main" }),
    Esp = Window:AddTab({ Title = "Esp" }),
    Teleport = Window:AddTab({ Title = "Teleport" }),
    Exploits = Window:AddTab({ Title = "Troll" }),
    Settings = Window:AddTab({ Title = "Scripts" })
}
-- parágrafos 
Tabs.Main:AddParagraph({ Title = "Desenvolvedores", Content = "@kwooso4 @Vito0296poq" })

-- botões 
Tabs.Main:AddButton({ Title = "infinite jump", Callback = function() 
loadstring(game:HttpGet("https://raw.githubusercontent.com/HeyGyt/infjump/main/main"))()
end })

-- Funções utilitárias
local function setHumanoidProperty(property, value)
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    humanoid[property] = value
    print(property .. " ajustado para:", value)
end

Tabs.Main:AddSlider("WalkSpeed", {
    Title = "WalkSpeed",
    Description = "Define a velocidade do jogador",
    Default = 20,
    Min = 0,
    Max = 400,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("WalkSpeed", value)
    end
})

Tabs.Main:AddSlider("JumpPower", {
    Title = "JumpPower",
    Description = "Define a altura do pulo",
    Default = 50,
    Min = 0,
    Max = 400,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("JumpPower", value)
    end
})

-- Variável para armazenar o estado do FOV (ativado ou desativado)
local fovAtivo = false
local fovPadrao = 70 -- Define o valor padrão do FOV quando desativado
local fovAtual = 70   -- Valor inicial do FOV ajustável

-- Criando Slider para ajustar o FOV
Tabs.Main:AddSlider("FOV", {
    Title = "FOV",
    Description = "Ajusta o campo de visão da câmera",
    Default = fovAtual,
    Min = 30,
    Max = 120, -- Máximo permitido pelo Roblox
    Rounding = 1,

    Callback = function(value)
        fovAtual = value
        if fovAtivo then
            game.Workspace.CurrentCamera.FieldOfView = fovAtual
        end
    end
})

-- Criando Toggle para ativar/desativar FOV
Tabs.Main:AddToggle("FOV_Toggle", {
    Title = "FOV",
    Description = "Ativa ou desativa  campo de visão",
    Default = false,

    Callback = function(state)
        fovAtivo = state
        if fovAtivo then
            game.Workspace.CurrentCamera.FieldOfView = fovAtual -- Aplica o FOV escolhido
        else
            game.Workspace.CurrentCamera.FieldOfView = fovPadrao -- Volta ao normal
        end
    end
})

-- Variável para armazenar o estado do ESP
local espAtivado = false
local connections = {}

Tabs.Esp:AddToggle("esp_nome_distancia", {
    Title = "ESP Nome",
    Description = "Ativa/Desativa ESP Nome, HP, Distância e Dias",
    Default = false,
    Callback = function(state)
        espAtivado = state
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Função para criar o ESP no jogador
        local function criarESP(player)
            if player == LocalPlayer or not espAtivado then return end

            local char = player.Character or player.CharacterAdded:Wait()
            local head = char:FindFirstChild("Head", 5)
            local humanoid = char:FindFirstChild("Humanoid")

            if head and humanoid then
                local esp = head:FindFirstChild("ESP")
                if not esp then
                    esp = Instance.new("BillboardGui")
                    esp.Name = "ESP"
                    esp.Adornee = head
                    esp.Size = UDim2.new(0, 200, 0, 50) -- Aumentei o tamanho para caber todas as informações
                    esp.StudsOffset = Vector3.new(0, -2, 0)
                    esp.AlwaysOnTop = true

                    local text = Instance.new("TextLabel")
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.BackgroundTransparency = 1
                    text.TextColor3 = Color3.fromRGB(255, 255, 255)
                    text.TextSize = 14
                    text.TextScaled = false
                    text.Font = Enum.Font.GothamBold
                    text.TextStrokeTransparency = 0.4
                    text.TextStrokeColor3 = Color3.new(0, 0, 0)
                    text.Parent = esp

                    esp.Parent = head
                end

                -- Atualiza o texto para mostrar Nome, HP, Distância e Dias de conta
                local function atualizarTexto()
                    while espAtivado and char and char.Parent and head.Parent and humanoid.Health > 0 do
                        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("HumanoidRootPart") then
                            local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                            local accountAge = player.AccountAge
                            local vida = math.floor(humanoid.Health)

                            esp.TextLabel.Text = string.format(
                                "%s - %d HP - %d Studs - %d Dias",
                                player.Name,
                                vida,
                                math.floor(distancia),
                                accountAge
                            )
                        end
                        task.wait(0.5)
                    end

                    if esp then
                        esp:Destroy()
                    end
                end

                -- Inicia a atualização do ESP
                task.spawn(atualizarTexto)

                -- Remove o ESP quando o jogador morrer
                humanoid.Died:Connect(function()
                    if esp then
                        esp:Destroy()
                    end
                end)
            end
        end

        -- Função para monitorar renascimentos
        local function monitorarPersonagem(player)
            if espAtivado then
                if connections[player] then
                    connections[player]:Disconnect()
                end

                connections[player] = player.CharacterAdded:Connect(function()
                    task.wait(1)
                    if espAtivado then
                        criarESP(player)
                    end
                end)
            end
        end

        -- Ativar ou desativar o ESP para todos jogadores
        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                criarESP(player)
                monitorarPersonagem(player)
            end

            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                monitorarPersonagem(player)
                criarESP(player)
            end)
        else
            -- Desativa todos os ESPs e desconecta
            for _, player in ipairs(Players:GetPlayers()) do
                if connections[player] then
                    connections[player]:Disconnect()
                    connections[player] = nil
                end
            end

            if connections["PlayerAdded"] then
                connections["PlayerAdded"]:Disconnect()
                connections["PlayerAdded"] = nil
            end
        end
    end
})

-- Estado do ESP
local espAtivado = false
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Função para aplicar o Highlight
local function aplicarHighlight(player)
    if player == LocalPlayer then return end
    local character = player.Character
    if character and not character:FindFirstChild("ESPHighlight") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESPHighlight"
        highlight.Adornee = character
        highlight.FillColor = Color3.fromRGB(255, 255, 255) -- Cor branca
        highlight.FillTransparency = 1 -- Centro totalmente transparente
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- Cor branca
        highlight.OutlineTransparency = 0 -- Contorno totalmente opaco
        highlight.Parent = character
    end
end

-- Função para remover o Highlight
local function removerHighlight(player)
    local character = player.Character
    if character then
        local highlight = character:FindFirstChild("ESPHighlight")
        if highlight then
            highlight:Destroy()
        end
    end
end

-- Loop de atualização contínua
local function loopAtualizacao()
    RunService.RenderStepped:Connect(function()
        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                aplicarHighlight(player)
            end
        else
            for _, player in ipairs(Players:GetPlayers()) do
                removerHighlight(player)
            end
        end
    end)
end

-- Monitorar novos jogadores
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if espAtivado then
            aplicarHighlight(player)
        end
    end)
end)

-- Adicionar Toggle ao menu
Tabs.Esp:AddToggle("esp_box_transparente", {
    Title = "ESP Box",
    Description = "Ativa/Desativa ESP Box",
    Default = false,
    Callback = function(state)
        espAtivado = state
        if state then
            loopAtualizacao()
        else
            for _, player in ipairs(Players:GetPlayers()) do
                removerHighlight(player)
            end
        end
    end
})
 
 Tabs.Teleport:AddButton({
    Title = "Teleport por player",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Infinity2346/Tect-Menu/main/Teleport%20Gui.lua"))()
    end
})
 
 -- parágrafos 
Tabs.Main:AddParagraph({ Title = "Em breve mais, atualizações." })

Tabs.Teleport:AddButton({
    Title = "Lobby",
    Callback = function()
        local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")
hrp.CFrame = CFrame.new(2.8888463973999023, 3.2902424335479736, 0.15440566837787628)
    end
})

Tabs.Teleport:AddButton({
    Title = "Hospital",
    Callback = function()
        local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")
hrp.CFrame = CFrame.new(-314.1259460449219, 16.576541900634766, 60.161827087402344)hospital
    end
})

Tabs.Teleport:AddButton({
    Title = "Mountain",
    Callback = function()
        local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")
hrp.CFrame = CFrame.new(-521.6278076171875, 0.19005724787712097, 910.4871215820312)
    end
})

Tabs.Exploits:AddButton({
    Title = "Fly car",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/main/Vehicle%20Fly%20Gui'))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Copiar avatar",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/refs/heads/main/Copy%20Avatar'))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Grudar portas",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Bring-Parts-27586"))()
    end
})

Tabs.Settings:AddButton({
    Title = "Real hub",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Laelmano24/Rael-Hub/main/main.txt"))()
    end
})

Tabs.Settings:AddButton({
    Title = "SP Hub",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/as6cd0/SP_Hub/refs/heads/main/Brookhaven"))()
    end
})

Tabs.Settings:AddButton({
    Title = "SanderX",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/kigredns/SanderXV4.2.2/refs/heads/main/New.lua'))()
    end
})

Tabs.Settings:AddButton({
    Title = "Sander-XY",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Brookhaven-RP-Sander-XY-35845"))()
    end
})

Tabs.Settings:AddButton({
    Title = "SystemBroken",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/H20CalibreYT/SystemBroken/main/script"))()
    end
})
