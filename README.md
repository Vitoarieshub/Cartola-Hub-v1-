-- Load OrionLib

local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/Snxdfer/back-ups-for-libs/main/Orion.lua'))()



-- Create main window

local Window = OrionLib:MakeWindow({

    Name = "Cartola Hub v2",

    HidePremium = false,

    SaveConfig = true,

    ConfigFolder = "Config"

})



-- Main Tab

local TabMain = Window:MakeTab({

    Name = "Main",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})



OrionLib:MakeNotification({

    Name = "Welcome!",

    Content = "Script loader successful!",

    Image = "rbxassetid://4483345998",

    Time = 5

})



TabMain:AddParagraph("Developers", "kwooso4 Vito0296poq FHDYRUTF")



local UserInputService = game:GetService("UserInputService")

local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer

local infinitoPuloAtivo = false



-- Infinite Jump

TabMain:AddToggle({

    Name = "Infinite Jump",

    Default = false,

    Callback = function(Value)

        infinitoPuloAtivo = Value

    end 

})



UserInputService.JumpRequest:Connect(function()

    if infinitoPuloAtivo then

        local character = LocalPlayer.Character

        if character and character:FindFirstChildOfClass("Humanoid") then

            character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)

        end

    end

end)



-- WalkSpeed

TabMain:AddTextbox({

    Name = "WalkSpeed",

    Default = "16",

    TextDisappear = false,

    Callback = function(Value)

        local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")

        if humanoid then

            humanoid.WalkSpeed = tonumber(Value) or 16

        end

    end

})



-- JumpPower

TabMain:AddTextbox({

    Name = "JumpPower",

    Default = "50",

    TextDisappear = false,

    Callback = function(Value)

        local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")

        if humanoid then

            humanoid.UseJumpPower = true

            humanoid.JumpPower = tonumber(Value) or 50

        end

    end

})



-- Gravity

local Workspace = game:GetService("Workspace")

TabMain:AddTextbox({

    Name = "Gravity",

    Default = "196.2",

    TextDisappear = false,

    Callback = function(Value)

        Workspace.Gravity = tonumber(Value) or 196.2

    end

})



-- Player Tab

local TabPlayer = Window:MakeTab({

    Name = "Player",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})



local Players = game:GetService("Players")

local Workspace = game:GetService("Workspace")



local LocalPlayer = Players.LocalPlayer

local Camera = Workspace.CurrentCamera

local selectedPlayer = nil



-- Função para obter nomes de jogadores (exceto o local)

local function getPlayerNames()

    local names = {}

    for _, player in ipairs(Players:GetPlayers()) do

        if player ~= LocalPlayer then

            table.insert(names, player.Name)

        end

    end

    return names

end



-- Criar dropdown

local playerDropdown = TabPlayer:AddDropdown({

    Name = "Select player",

    Default = "",

    Options = getPlayerNames(),

    Callback = function(Value)

        selectedPlayer = Players:FindFirstChild(Value)

    end

})



-- Atualiza o dropdown dinamicamente

local function updateDropdown()

    local names = getPlayerNames()

    playerDropdown:Refresh(names, true) -- atualiza as opções e reseta o valor selecionado

end



-- Eventos para detectar entrada e saída de jogadores

Players.PlayerAdded:Connect(updateDropdown)

Players.PlayerRemoving:Connect(function(leavingPlayer)

    if selectedPlayer == leavingPlayer then

        selectedPlayer = nil

    end

    updateDropdown()

end)



-- Espectar jogador

TabPlayer:AddToggle({

    Name = "View Player",

    Default = false,

    Callback = function(Value)

        if Value and selectedPlayer and selectedPlayer.Character then

            local humanoid = selectedPlayer.Character:FindFirstChildOfClass("Humanoid")

            if humanoid then

                Camera.CameraSubject = humanoid

            end

        else

            local myHumanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")

            if myHumanoid then

                Camera.CameraSubject = myHumanoid

            end

        end

    end

})



-- Teleportar até o jogador

TabPlayer:AddButton({

    Name = "Teleport on Player",

    Callback = function()

        if selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart") then

            local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

            if hrp then

                hrp.CFrame = selectedPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)

            end

        end

    end

})





local Players = game:GetService("Players")



local Workspace = game:GetService("Workspace")



local RunService = game:GetService("RunService")



local LocalPlayer = Players.LocalPlayer

local selectedPlayer = nil

local flingActive = false

local flingConnection = nil

local orbitRadius = 3

local orbitSpeed = 5

local angle = 0



-- Função para listar jogadores (exceto o próprio)

local function getPlayerNames()

    local names = {}

    for _, player in ipairs(Players:GetPlayers()) do

        if player ~= LocalPlayer then

            table.insert(names, player.Name)

        end

    end

    return names

end



-- Dropdown dinâmico

local playerDropdown = TabPlayer:AddDropdown({

    Name = "Select Player",

    Default = "",

    Options = getPlayerNames(),

    Callback = function(value)

        selectedPlayer = Players:FindFirstChild(value)

    end

})



-- Atualiza quando jogadores entram/saem

local function updateDropdown()

    local names = getPlayerNames()

    playerDropdown:Refresh(names, true)

end



Players.PlayerAdded:Connect(updateDropdown)

Players.PlayerRemoving:Connect(function(p)

    if selectedPlayer == p then

        selectedPlayer = nil

    end

    updateDropdown()

end)



-- Ativar FLING girando em volta

local function startFling()

    if not selectedPlayer then return end



    local myChar = LocalPlayer.Character

    local targetChar = selectedPlayer.Character

    if not (myChar and targetChar) then return end



    local hrp = myChar:FindFirstChild("HumanoidRootPart")

    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")

    if not (hrp and targetHRP) then return end



    hrp.Anchored = false

    angle = 0



    flingConnection = RunService.Heartbeat:Connect(function(dt)

        if not (selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart")) then

            return

        end



        -- Calcular posição em volta do jogador

        angle += orbitSpeed * dt

        local offsetX = math.cos(angle) * orbitRadius

        local offsetZ = math.sin(angle) * orbitRadius

        local targetPos = targetHRP.Position + Vector3.new(offsetX, 0.5, offsetZ)



        hrp.CFrame = CFrame.new(targetPos, targetHRP.Position)

        hrp.RotVelocity = Vector3.new(9999, 9999, 9999)

    end)

end



-- Parar FLING

local function stopFling()

    flingActive = false

    if flingConnection then

        flingConnection:Disconnect()

        flingConnection = nil

    end



    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

    if hrp then

        hrp.RotVelocity = Vector3.zero

        hrp.AssemblyLinearVelocity = Vector3.zero

    end

end



-- Botão para alternar FLING

TabPlayer:AddButton({

    Name = "Fling",

    Callback = function()

        flingActive = not flingActive

        if flingActive then

            startFling()

        else

            stopFling()

        end

    end

})







-- CORREÇÃO AQUI:

TabPlayer:AddButton({

    Name = "Bring Parts",

    Callback = function()

        print("Executando Bring Parts...")

        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Bring-Parts-27586"))()

    end

})



-- Player Tab

local TabAnti = Window:MakeTab({



    Name = "Anti Troll",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})



local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer



local antiVehicleSeatEnabled = false

local seatConnections = {}



TabAnti:AddToggle({

    Name = "Anti Vehicle",

    Default = false,

    Callback = function(Value)

        antiVehicleSeatEnabled = Value



        -- Limpa conexões anteriores

        for _, conn in ipairs(seatConnections) do

            if conn then conn:Disconnect() end

        end

        table.clear(seatConnections)



        if Value then

            local function monitorCharacter(character)

                local humanoid = character:WaitForChild("Humanoid")

                local conn = humanoid.Seated:Connect(function(isSeated, seat)

                    if isSeated and seat and seat:IsA("VehicleSeat") and antiVehicleSeatEnabled then

                        humanoid.Sit = false

                    end

                end)

                table.insert(seatConnections, conn)

            end



            -- Proteger o personagem atual

            if LocalPlayer.Character then

                monitorCharacter(LocalPlayer.Character)

            end



            -- Proteger futuros personagens

            local charConn = LocalPlayer.CharacterAdded:Connect(function(character)

                monitorCharacter(character)

            end)

            table.insert(seatConnections, charConn)

        end

    end

})



local antiSeatEnabled = false

local seatConnection = nil



TabAnti:AddToggle({

    Name = "Anti Sit",

    Default = false,

    Callback = function(Value)

        antiSeatEnabled = Value



        if Value then

            seatConnection = LocalPlayer.CharacterAdded:Connect(function(character)

                local humanoid = character:WaitForChild("Humanoid")

                humanoid.Seated:Connect(function(isSeated)

                    if isSeated and antiSeatEnabled then

                        humanoid.Sit = false

                    end

                end)

            end)



            -- Protege imediatamente

            if LocalPlayer.Character then

                local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")

                if humanoid then

                    humanoid.Seated:Connect(function(isSeated)

                        if isSeated and antiSeatEnabled then

                            humanoid.Sit = false

                        end

                    end)

                end

            end

        else

            if seatConnection then

                seatConnection:Disconnect()

                seatConnection = nil

            end

        end

    end

})



-- Teleport Tab

local TabTeleport = Window:MakeTab({

    Name = "Teleport",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})



local function teleportTo(x, y, z)

    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

    if hrp then

        hrp.CFrame = CFrame.new(x, y, z)

    end

end



TabTeleport:AddButton({

    Name = "Lobby",

    Callback = function()

        teleportTo(2.88, 3.29, 0.15)

    end

})



TabTeleport:AddButton({

    Name = "Hospital",

    Callback = function()

        teleportTo(-314.12, 16.57, 60.16)

    end

})



TabTeleport:AddButton({

    Name = "Mountain",

    Callback = function()

        teleportTo(-521.62, 0.19, 910.48)

    end

})



-- Scripts Tab

local TabScripts = Window:MakeTab({

    Name = "Scripts",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})



TabScripts:AddButton({

    Name = "Real hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Laelmano24/Rael-Hub/main/main.txt"))()

    end

})



TabScripts:AddButton({

    Name = "SP Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/as6cd0/SP_Hub/main/Brookhaven"))()

    end

})



TabScripts:AddButton({

    Name = "Sander XY",

    Callback = function()

        loadstring(game:HttpGet("https://rawscripts.net/raw/Brookhaven-RP-Sander-XY-35845"))()

    end

})



-- Init UI

OrionLib:Init()

