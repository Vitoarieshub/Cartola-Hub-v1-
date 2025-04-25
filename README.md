-- carregar biblioteca 
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- aviso ao executar
Fluent:Notify({ Title = "executado!", Content = "executando com sucesso" })


local Window = Fluent:CreateWindow({
    Title = "Cartola Hub - Brookhaven " .. Fluent.Version,
    TabWidth = 160, 
    Size = UDim2.fromOffset(580, 460), 
    Theme = "Dark"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main" }),
    Visual = Window:AddTab({ Title = "Visual" }),
    Exploits = Window:AddTab({ Title = "Exploits" }),
    Settings = Window:AddTab({ Title = "Scripts" })
}
-- parágrafos 
Tabs.Main:AddParagraph({ Title = "Desenvolvedores", Content = "@kwooso4 @Vito0296poq @KainagamerTigre" })

-- botões 
Tabs.Main:AddButton({ Title = "infinite jump", Callback = function() 
loadstring(game:HttpGet("https://raw.githubusercontent.com/HeyGyt/infjump/main/main"))()
end })

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

-- Variável para armazenar o estado do ESP
local espAtivado = false
local connections = {}

Tabs.Visual:AddToggle("esp_nome_distancia", {
    Title = "ESP Nome",
    Description = "Ativa/Desativa ESP Nome e Distância",
    Default = false,
    Callback = function(state)
        espAtivado = state
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Função para criar ESP
        local function criarESP(player)
            if player == LocalPlayer then return end
            if not espAtivado then return end -- Impede a criação se o ESP estiver desligado

            local char = player.Character or player.CharacterAdded:Wait()
            local head = char:FindFirstChild("Head", 5)
            local humanoid = char:FindFirstChild("Humanoid")

            if head and humanoid then
                local esp = head:FindFirstChild("ESP")
                if not esp then
                    esp = Instance.new("BillboardGui")
                    esp.Name = "ESP"
                    esp.Adornee = head
                    esp.Size = UDim2.new(0, 150, 0, 50) -- Ajusta o tamanho para incluir nome e distância
                    esp.StudsOffset = Vector3.new(0, -2, 0)  -- Modificado para posicionar abaixo do personagem
                    esp.AlwaysOnTop = true

                    local text = Instance.new("TextLabel")
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.BackgroundTransparency = 1
                    text.TextColor3 = Color3.fromRGB(255, 255, 255) -- Cor branca
                    text.TextSize = 14 -- Tamanho da fonte
                    text.TextScaled = false -- Não usa escala automática
                    text.Font = Enum.Font.GothamBold
                    text.TextStrokeTransparency = 0.4
                    text.TextStrokeColor3 = Color3.new(0, 0, 0)
                    text.Parent = esp

                    esp.Parent = head
                end

                -- Atualiza o texto para incluir o nome e a distância
                local function atualizarTexto()
                    while espAtivado and char and char.Parent and head.Parent and humanoid.Health > 0 do
                        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("HumanoidRootPart") then
                            local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                            esp.TextLabel.Text = player.Name .. " - " .. math.floor(distancia) .. " Studs"
                        end
                        wait(0.1) -- Atualiza a cada 0.1 segundos
                    end

                    -- Remove o ESP quando o personagem morre
                    if esp then
                        esp:Destroy()
                    end
                end

                -- Inicia a atualização do texto
                task.spawn(atualizarTexto)

                -- Adiciona um evento para remover o ESP quando o jogador morre
                humanoid.Died:Connect(function()
                    if esp then
                        esp:Destroy()
                    end
                end)
            end
        end

        -- Função para monitorar renascimentos e atualizar ESP automaticamente
        local function monitorarPersonagem(player)
            if espAtivado then
                if connections[player] then
                    connections[player]:Disconnect() -- Desconectar conexão antiga
                end

                connections[player] = player.CharacterAdded:Connect(function(char)
                    wait(1) -- Espera o personagem carregar
                    if espAtivado then
                        criarESP(player)
                    end
                end)
            end
        end

        -- Ativar ESP para todos os jogadores
        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                criarESP(player)
                monitorarPersonagem(player)
            end

            -- Criar ESP para novos jogadores somente quando o ESP estiver ligado
            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                monitorarPersonagem(player)
                criarESP(player)
            end)
        else
            -- Desativar ESP completamente
            for _, player in ipairs(Players:GetPlayers()) do
                if connections[player] then
                    connections[player]:Disconnect()
                    connections[player] = nil
                end
            end

            -- Desconectar evento de novos jogadores quando o ESP estiver desligado
            if connections["PlayerAdded"] then
                connections["PlayerAdded"]:Disconnect()
                connections["PlayerAdded"] = nil
            end
        end
    end
})

-- Variável para armazenar o estado do FOV (ativado ou desativado)
local fovAtivo = false
local fovPadrao = 70 -- Define o valor padrão do FOV quando desativado
local fovAtual = 70   -- Valor inicial do FOV ajustável

-- Criando Slider para ajustar o FOV
Tabs.Visual:AddSlider("FOV", {
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
Tabs.Visual:AddToggle("FOV_Toggle", {
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

Tabs.Exploits:AddButton({
    Title = "Grudar portas",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Bring-Parts-27586"))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Fly car",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/main/Vehicle%20Fly%20Gui'))()
    end
})

Tabs.Settings:AddButton({
    Title = "Real hub",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Laelmano24/Rael-Hub/main/main.txt"))()
    end
})

Tabs.Settings:AddButton({
    Title = "SanderX",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/kigredns/SanderXV4.2.2/refs/heads/main/New.lua'))()
    end
})
