-- Carregar biblioteca Fluent
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Aviso ao executar
Fluent:Notify({ Title = "Executado!", Content = "Executando com sucesso" })

-- Criar janela principal
local Window = Fluent:CreateWindow({
    Title = "Cartola Hub - Brookhaven " .. Fluent.Version,
    TabWidth = 160, 
    Size = UDim2.fromOffset(460, 350), 
    Theme = "Dark"
})

-- Criar abas
local Tabs = {
    Main = Window:AddTab({ Title = "Main" }),
    Esp = Window:AddTab({ Title = "Esp" }),
    Teleport = Window:AddTab({ Title = "Teleport" }),
    Exploits = Window:AddTab({ Title = "Troll" }),
    Settings = Window:AddTab({ Title = "Scripts" })
}

-- Parágrafo informativo
Tabs.Main:AddParagraph({ Title = "Desenvolvedores", Content = "@kwooso4 @Vito0296poq" })

-- Botão: Infinite Jump
Tabs.Main:AddButton({
    Title = "Infinite Jump",
    Callback = function() 
        loadstring(game:HttpGet("https://raw.githubusercontent.com/HeyGyt/infjump/main/main"))()
    end
})

-- Função utilitária para alterar propriedades do Humanoid
local function setHumanoidProperty(property, value)
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    humanoid[property] = value
    print(property .. " ajustado para:", value)
end

-- Sliders de WalkSpeed e JumpPower
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

-- Campo de visão (FOV)
local fovAtivo = false
local fovPadrao = 70
local fovAtual = 70

Tabs.Main:AddSlider("FOV", {
    Title = "FOV",
    Description = "Ajusta o campo de visão da câmera",
    Default = fovAtual,
    Min = 30,
    Max = 120,
    Rounding = 1,
    Callback = function(value)
        fovAtual = value
        if fovAtivo then
            game.Workspace.CurrentCamera.FieldOfView = fovAtual
        end
    end
})

Tabs.Main:AddToggle("FOV_Toggle", {
    Title = "FOV",
    Description = "Ativa ou desativa o campo de visão",
    Default = false,
    Callback = function(state)
        fovAtivo = state
        game.Workspace.CurrentCamera.FieldOfView = fovAtivo and fovAtual or fovPadrao
    end
})

-- ESP Nome/Distância/HP/Dias
local espAtivado = false
local connections = {}

Tabs.Esp:AddToggle("esp_nome_distancia", {
    Title = "ESP Nome",
    Description = "Ativa/Desativa ESP com informações",
    Default = false,
    Callback = function(state)
        espAtivado = state
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        local function criarESP(player)
            if player == LocalPlayer or not espAtivado then return end

            local char = player.Character or player.CharacterAdded:Wait()
            local head = char:FindFirstChild("Head")
            local humanoid = char:FindFirstChild("Humanoid")

            if head and humanoid then
                local esp = head:FindFirstChild("ESP")
                if not esp then
                    esp = Instance.new("BillboardGui", head)
                    esp.Name = "ESP"
                    esp.Adornee = head
                    esp.Size = UDim2.new(0, 200, 0, 50)
                    esp.StudsOffset = Vector3.new(0, -2, 0)
                    esp.AlwaysOnTop = true

                    local text = Instance.new("TextLabel", esp)
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.BackgroundTransparency = 1
                    text.TextColor3 = Color3.fromRGB(255, 255, 255)
                    text.TextSize = 14
                    text.Font = Enum.Font.GothamBold
                    text.TextStrokeTransparency = 0.4
                    text.TextStrokeColor3 = Color3.new(0, 0, 0)
                end

                task.spawn(function()
                    while espAtivado and char.Parent and humanoid.Health > 0 do
                        local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                        head.ESP.TextLabel.Text = string.format("%s - %d HP - %d Studs - %d Dias",
                            player.Name,
                            math.floor(humanoid.Health),
                            math.floor(distancia),
                            player.AccountAge)
                        task.wait(0.5)
                    end
                    if head:FindFirstChild("ESP") then
                        head.ESP:Destroy()
                    end
                end)

                humanoid.Died:Connect(function()
                    if head:FindFirstChild("ESP") then
                        head.ESP:Destroy()
                    end
                end)
            end
        end

        local function monitorar(player)
            if connections[player] then
                connections[player]:Disconnect()
            end
            connections[player] = player.CharacterAdded:Connect(function()
                task.wait(1)
                criarESP(player)
            end)
        end

        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                criarESP(player)
                monitorar(player)
            end
            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                criarESP(player)
                monitorar(player)
            end)
        else
            for _, conn in pairs(connections) do
                if typeof(conn) == "RBXScriptConnection" then conn:Disconnect() end
            end
            connections = {}
            for _, player in ipairs(Players:GetPlayers()) do
                local char = player.Character
                if char and char:FindFirstChild("Head") and char.Head:FindFirstChild("ESP") then
                    char.Head.ESP:Destroy()
                end
            end
        end
    end
})

-- ESP Highlight (Caixa Branca)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local function aplicarHighlight(player)
    if player == LocalPlayer then return end
    local char = player.Character
    if char and not char:FindFirstChild("ESPHighlight") then
        local highlight = Instance.new("Highlight", char)
        highlight.Name = "ESPHighlight"
        highlight.Adornee = char
        highlight.FillColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 1
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0
    end
end

local function removerHighlight(player)
    local char = player.Character
    if char then
        local highlight = char:FindFirstChild("ESPHighlight")
        if highlight then highlight:Destroy() end
    end
end

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

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if espAtivado then aplicarHighlight(player) end
    end)
end)

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

-- TELEPORTES
Tabs.Teleport:AddButton({
    Title = "Teleport por player",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Infinity2346/Tect-Menu/main/Teleport%20Gui.lua"))()
    end
})

Tabs.Teleport:AddButton({
    Title = "Lobby",
    Callback = function()
        local hrp = game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(2.88, 3.29, 0.15)
    end
})

Tabs.Teleport:AddButton({
    Title = "Hospital",
    Callback = function()
        local hrp = game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(-314.12, 16.57, 60.16)
    end
})

Tabs.Teleport:AddButton({
    Title = "Mountain",
    Callback = function()
        local hrp = game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(-521.62, 0.19, 910.48)
    end
})

-- EXPLOITS
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

-- SCRIPTS EXTERNOS
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

-- Mensagem final
Tabs.Main:AddParagraph({ Title = "Em breve mais, atualizações." })
