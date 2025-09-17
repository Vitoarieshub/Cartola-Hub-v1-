-- Load OrionLib

local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/Snxdfer/back-ups-for-libs/main/Orion.lua'))()



-- Create main window

local Window = OrionLib:MakeWindow({

    Name = "Cartola Hub Brookhaven",

    HidePremium = false,

    SaveConfig = true,

    ConfigFolder = "Config"

})


OrionLib:MakeNotification({

    Name = "Cartola Hub!",

    Content = "Script loader successful!",

    Image = "rbxassetid://130563590371769",

    Time = 5

})



-- CreditsTab

local TabCredits = Window:MakeTab({

    Name = "Credits",

    Icon = "rbxassetid://130563590371769",

    PremiumOnly = false

})


TabCredits:AddParagraph("Developers", "kwooso4 Vito0296poq FHDYRUTF")

TabCredits:AddParagraph("Admin", "Tanjiro_kamado7Wk")



TabCredits:AddButton({

    Name = "Cartola Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Davi999z/Cartola-Hub/refs/heads/main/Brookhaven",true))()
        
    end

})


-- Main Tab

local TabMain = Window:MakeTab({

    Name = "Main",

    Icon = "rbxassetid://130563590371769",

    PremiumOnly = false

})


TabMain:AddButton({

    Name = "Fly Cartola",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-Cartola-Hub-/refs/heads/main/README.md"))()
        
    end

})


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


local Lighting = game:GetService("Lighting")

-- Armazena configurações originais
local originalSettings = {
    Brightness = Lighting.Brightness,
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    ClockTime = Lighting.ClockTime,
    FogEnd = Lighting.FogEnd,
    GlobalShadows = Lighting.GlobalShadows
}

local fullBrightEnabled = false
local connections = {}

-- Ativa o modo manhã (11:30)
local function enableMorningLight()
    fullBrightEnabled = true

    Lighting.Brightness = 1.5
    Lighting.Ambient = Color3.fromRGB(180, 180, 160)
    Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 170)
    Lighting.ClockTime = 11.5
    Lighting.FogEnd = 1e9
    Lighting.GlobalShadows = true

    -- Protege as propriedades contra mudanças externas
    table.insert(connections, Lighting:GetPropertyChangedSignal("ClockTime"):Connect(function()
        if fullBrightEnabled then Lighting.ClockTime = 11.5 end
    end))

    table.insert(connections, Lighting:GetPropertyChangedSignal("Ambient"):Connect(function()
        if fullBrightEnabled then Lighting.Ambient = Color3.fromRGB(180, 180, 160) end
    end))

    table.insert(connections, Lighting:GetPropertyChangedSignal("OutdoorAmbient"):Connect(function()
        if fullBrightEnabled then Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 170) end
    end))

    table.insert(connections, Lighting:GetPropertyChangedSignal("Brightness"):Connect(function()
        if fullBrightEnabled then Lighting.Brightness = 1.5 end
    end))

    table.insert(connections, Lighting:GetPropertyChangedSignal("GlobalShadows"):Connect(function()
        if fullBrightEnabled then Lighting.GlobalShadows = true end
    end))

    table.insert(connections, Lighting:GetPropertyChangedSignal("FogEnd"):Connect(function()
        if fullBrightEnabled then Lighting.FogEnd = 1e9 end
    end))

    print("It was daytime.")
end

-- Restaura os valores originais
local function disableMorningLight()
    fullBrightEnabled = false

    for _, conn in ipairs(connections) do
        if conn.Disconnect then
            conn:Disconnect()
        end
    end
    connections = {}

    for prop, value in pairs(originalSettings) do
        Lighting[prop] = value
    end

    print("restaurada.")
end

-- Toggle no TabMain
TabMain:AddToggle({
    Name = "Day",
    Default = false,
    Callback = function(Value)
        if Value then
            enableMorningLight()
        else
            disableMorningLight()
        end
    end
})


local function isBanWall(part)
    if not part:IsA("BasePart") then return false end
    local name = part.Name:lower()
    return name:match("ban") or name:match("barrier")
end

TabHome:AddButton({
    Name = "Unban",
    Callback = function()
        local count = 0
        for _, part in ipairs(workspace:GetDescendants()) do
            if isBanWall(part) then
                part:Destroy()
                count += 1
            end
        end
        print("Unban Manual:", count)
    end
})


local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local nightConnection

-- Função para ativar/desativar modo noite
local function enableNight(state)
    if state then
        -- Mantém forçado noite
        nightConnection = RunService.RenderStepped:Connect(function()
            Lighting.ClockTime = 20.67 
            Lighting.Brightness = 2
            Lighting.OutdoorAmbient = Color3.fromRGB(30, 30, 30)
        end)
        print("Night mode activated.")
    else
        -- Desliga e restaura padrão
        if nightConnection then
            nightConnection:Disconnect()
            nightConnection = nil
        end
        Lighting.ClockTime = 14 
        Lighting.Brightness = 3
        Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        print("Night mode disabled, restored.")
    end
end

-- Toggle no TabMain
TabMain:AddToggle({
    Name = "Night",
    Default = false,
    Callback = function(Value)
        enableNight(Value)
    end
})


-- Player Tab

local TabPlayer = Window:MakeTab({

    Name = "Player",

    Icon = "rbxassetid://130563590371769",

    PremiumOnly = false

})



local Players = game:GetService("Players")

local Workspace = game:GetService("Workspace")

local RunService = game:GetService("RunService")



local LocalPlayer = Players.LocalPlayer

local Camera = Workspace.CurrentCamera



local selectedPlayer = nil

local flingConnection = nil

local flingActive = false

local orbitRadius = 5

local orbitSpeed = 10

local angle = 0



-- Obter lista de jogadores

local function getPlayerNames()

	local names = {}

	for _, player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			table.insert(names, player.Name)

		end

	end

	return names

end



-- Dropdown

local playerDropdown = TabPlayer:AddDropdown({

	Name = "Select player",

	Default = "",

	Options = getPlayerNames(),

	Callback = function(Value)

		selectedPlayer = Players:FindFirstChild(Value)

	end

})



-- Atualizar lista

local function updateDropdown()

	playerDropdown:Refresh(getPlayerNames(), true)

end



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

		local target = Value and selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChildOfClass("Humanoid")

		local myHumanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")

		Camera.CameraSubject = target or myHumanoid or Camera.CameraSubject

	end

})



-- Teleportar até jogador

TabPlayer:AddButton({

	Name = "Teleport on Player",

	Callback = function()

		if selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart") then

			local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

			if myHRP then

				myHRP.CFrame = selectedPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)

			end

		end

	end

})

-- FLING

local function setupFling()

	if not (selectedPlayer and selectedPlayer.Character and LocalPlayer.Character) then return end



	local myHRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

	local targetHRP = selectedPlayer.Character:FindFirstChild("HumanoidRootPart")

	if not (myHRP and targetHRP) then return end



	myHRP.Anchored = false

	angle = 0



	if flingConnection then flingConnection:Disconnect() end



	flingConnection = RunService.Heartbeat:Connect(function(dt)

		if not (selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart")) then return end

		targetHRP = selectedPlayer.Character.HumanoidRootPart



		local offset = Vector3.new(math.cos(angle), 0.5, math.sin(angle)) * orbitRadius

		angle += orbitSpeed * dt



		myHRP.CFrame = CFrame.new(targetHRP.Position + offset, targetHRP.Position)

		myHRP.RotVelocity = Vector3.new(9999, 9999, 9999)

	end)

end



local function startFling()

	setupFling()



	LocalPlayer.CharacterAdded:Connect(function()

		if flingActive then wait(1) setupFling() end

	end)



	if selectedPlayer then

		selectedPlayer.CharacterAdded:Connect(function()

			if flingActive then wait(1) setupFling() end

		end)

	end

end



local function stopFling()

	flingActive = false

	if flingConnection then

		flingConnection:Disconnect()

		flingConnection = nil

	end

	local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

	if myHRP then

		myHRP.RotVelocity = Vector3.zero

		myHRP.AssemblyLinearVelocity = Vector3.zero

	end

end



-- Botão de Fling

TabPlayer:AddButton({

	Name = "Fling (Beta)",

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



TabPlayer:AddParagraph("Misc", "")


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local antiSeatEnabled = false
local seatedConnection = nil
local characterConnection = nil
local seatWatcher = nil
local ignoreSeats = {}

-- Verifica se o assento faz parte de um veículo
local function isSeatInVehicle(seat)
	if seat:IsA("VehicleSeat") then
		return true
	end
	local model = seat:FindFirstAncestorOfClass("Model")
	if model then
		for _, part in ipairs(model:GetDescendants()) do
			if part:IsA("VehicleSeat") or part.Name:lower():match("wheel") then
				return true
			end
		end
	end
	return false
end

-- Impede o personagem de sentar em assentos de veículos
local function preventSitting(character)
	local humanoid = character:WaitForChild("Humanoid", 5)
	if not humanoid then return end

	if seatedConnection then
		seatedConnection:Disconnect()
	end

	seatedConnection = humanoid.Seated:Connect(function(isSeated, seat)
		if isSeated and antiSeatEnabled and seat and isSeatInVehicle(seat) then
			humanoid.Sit = false
		end
	end)

	if humanoid.Sit and isSeatInVehicle(humanoid.SeatPart) then
		humanoid.Sit = false
	end
end

-- Desativa assentos de veículos
local function disableVehicleSeats()
	for _, obj in ipairs(workspace:GetDescendants()) do
		if (obj:IsA("Seat") or obj:IsA("VehicleSeat")) and isSeatInVehicle(obj) then
			if not ignoreSeats[obj] then
				ignoreSeats[obj] = obj.CanTouch
				obj.CanTouch = false
			end
		end
	end
end

-- Restaura os assentos
local function restoreSeats()
	for seat, original in pairs(ignoreSeats) do
		if seat and seat:IsDescendantOf(workspace) then
			seat.CanTouch = original
		end
	end
	ignoreSeats = {}
end

-- Observa novos assentos sendo criados
local function watchNewSeats()
	if seatWatcher then seatWatcher:Disconnect() end
	seatWatcher = workspace.DescendantAdded:Connect(function(desc)
		if antiSeatEnabled and (desc:IsA("Seat") or desc:IsA("VehicleSeat")) then
			task.wait(0.1)
			if isSeatInVehicle(desc) then
				ignoreSeats[desc] = desc.CanTouch
				desc.CanTouch = false
			end
		end
	end)
end

-- Toggle Anti Vehicle
TabPlayer:AddToggle({
	Name = "Anti Vehicle",
	Default = false,
	Callback = function(Value)
		antiSeatEnabled = Value

		if Value then
			-- Ativar
			if LocalPlayer.Character then
				preventSitting(LocalPlayer.Character)
			end

			if characterConnection then
				characterConnection:Disconnect()
			end
			characterConnection = LocalPlayer.CharacterAdded:Connect(preventSitting)

			disableVehicleSeats()
			watchNewSeats()
		else
			-- Desativar
			if seatedConnection then
				seatedConnection:Disconnect()
				seatedConnection = nil
			end
			if characterConnection then
				characterConnection:Disconnect()
				characterConnection = nil
			end
			if seatWatcher then
				seatWatcher:Disconnect()
				seatWatcher = nil
			end

			restoreSeats()
		end
	end
})



local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local antiSeatEnabled = false
local seatConnections = {}

-- Função para proteger contra sentar
local function setupAntiSit(character)
    local humanoid = character:WaitForChild("Humanoid", 5)
    if not humanoid then return end

    local connection = humanoid.Seated:Connect(function(isSeated)
        if isSeated and antiSeatEnabled then
            humanoid.Sit = false
        end
    end)

    table.insert(seatConnections, connection)
end

-- Função para limpar conexões anteriores
local function disconnectAllSeats()
    for _, conn in ipairs(seatConnections) do
        if conn.Connected then
            conn:Disconnect()
        end
    end
    table.clear(seatConnections)
end

-- Detectar novo personagem
LocalPlayer.CharacterAdded:Connect(function(char)
    if antiSeatEnabled then
        setupAntiSit(char)
    end
end)

-- Toggle Anti Sit
TabPlayer:AddToggle({
    Name = "Anti Sit",
    Default = false,
    Callback = function(Value)
        antiSeatEnabled = Value

        if Value then
            -- Aplicar imediatamente se já tiver personagem
            if LocalPlayer.Character then
                setupAntiSit(LocalPlayer.Character)
            end
        else
            disconnectAllSeats()
        end
    end
})



local Players = game:GetService("Players")

-- Função que aplica ESP num player
local function ApplyESP(v)
    if v.Character and v.Character:FindFirstChildOfClass("Humanoid") then
        local humanoid = v.Character:FindFirstChildOfClass("Humanoid")
        humanoid.NameDisplayDistance = 9e9
        humanoid.NameOcclusion = Enum.NameOcclusion.NoOcclusion
        humanoid.HealthDisplayDistance = 9e9
        humanoid.HealthDisplayType = Enum.HumanoidHealthDisplayType.AlwaysOn
        humanoid.Health = humanoid.Health -- "refresh"
    end
end

-- Função que remove ESP de um player
local function RemoveESP(v)
    if v.Character and v.Character:FindFirstChildOfClass("Humanoid") then
        local humanoid = v.Character:FindFirstChildOfClass("Humanoid")
        humanoid.NameDisplayDistance = 0
        humanoid.HealthDisplayDistance = 0
        humanoid.HealthDisplayType = Enum.HumanoidHealthDisplayType.AlwaysOff
    end
end

-- Toggle no TabPlayer
TabPlayer:AddToggle({
    Name = "ESP Players",
    Default = false,
    Callback = function(Value)
        if Value then
            -- Ativar ESP
            for _, v in ipairs(Players:GetPlayers()) do
                ApplyESP(v)
                v.CharacterAdded:Connect(function()
                    task.wait(0.33)
                    ApplyESP(v)
                end)
            end

            Players.PlayerAdded:Connect(function(v)
                ApplyESP(v)
                v.CharacterAdded:Connect(function()
                    task.wait(0.33)
                    ApplyESP(v)
                end)
            end)
        else
            -- Desativar ESP
            for _, v in ipairs(Players:GetPlayers()) do
                RemoveESP(v)
            end
        end
    end
})


-- Teleport Tab

local TabTeleport = Window:MakeTab({

    Name = "Teleport",

    Icon = "rbxassetid://130563590371769",

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



TabTeleport:AddButton({

    Name = "Hill",

    Callback = function()

        teleportTo(-349.32, 65.65, -439.50)

    end

})



-- Scripts Tab

local TabScripts = Window:MakeTab({

    Name = "Scripts",

    Icon = "rbxassetid://130563590371769",

    PremiumOnly = false

})

TabScripts:AddParagraph("Brookhaven", "")


TabScripts:AddButton({

    Name = "Brutus Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/codenixstudios/brutus-hub/script/Games/brookhaven.lua"))()
        
    end

})



TabScripts:AddButton({

    Name = "Coquette Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Daivd977/Deivd999/refs/heads/main/pessal"))()

    end

})


TabScripts:AddButton({

    Name = "Bruxus Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Lindao10/BRUXUS-HUB/refs/heads/main/BRUXUS%20HUB.LUA"))()

    end

})



TabScripts:AddButton({
    Name = "Gumball Hub",
    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/JaozinScripts/Gumball-Hub/refs/heads/main/GumballHubRetorn2.1.1.1.lua"))()

    end

})



TabScripts:AddButton({

    Name = "Real hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Laelmano24/Rael-Hub/main/main.txt"))()

    end

})



TabScripts:AddButton({

    Name = "Sander XY",

    Callback = function()

        loadstring(game:HttpGet("https://rawscripts.net/raw/Brookhaven-RP-Sander-XY-35845"))()

    end

})


TabScripts:AddParagraph("Universal", "")


TabScripts:AddButton({

    Name = "Ghost Hub",

    Callback = function()

        loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/main/GhostHub'))()

    end

})


TabScripts:AddButton({

    Name = "Dragon Menu",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/DragonUniversal/Dragon-Script-/refs/heads/main/main.lua"))()

    end

})


TabScripts:AddButton({

    Name = "YARHM Menu",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Joystickplays/psychic-octo-invention/main/yarhm.lua", false))()

    end

})


TabScripts:AddButton({

    Name = "Galaxy Hub",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/GalaxyStudio646/Galaxy-Hub/refs/heads/main/Universal/GalaxyHub"))()
		
    end

})


-- Init UI

OrionLib:Init()

