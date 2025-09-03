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

           loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Cartola-Hub-Brookhaven-/refs/heads/main/Cartola.txt"))()
        
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
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local antiPushConnection = nil

local function enableAntiPush()
	if not LocalPlayer.Character then return end
	local rootPart = LocalPlayer.Character:WaitForChild("HumanoidRootPart", 5)
	if not rootPart then return end

	antiPushConnection = RunService.Heartbeat:Connect(function()
		if not rootPart or not rootPart.Parent then return end
		rootPart.Velocity = Vector3.zero
		rootPart.RotVelocity = Vector3.zero
		rootPart.AssemblyLinearVelocity = Vector3.zero
		rootPart.AssemblyAngularVelocity = Vector3.zero

		for _, v in pairs(rootPart:GetChildren()) do
			if v:IsA("BodyVelocity") or v:IsA("BodyForce") or v:IsA("BodyThrust") then
				v:Destroy()
			end
		end
	end)
end

local function disableAntiPush()
	if antiPushConnection then
		antiPushConnection:Disconnect()
		antiPushConnection = nil
	end
end

-- Conectar novamente ao trocar de personagem
Players.LocalPlayer.CharacterAdded:Connect(function()
	if getgenv().AntiPushEnabled then
		task.wait(1)
		enableAntiPush()
	end
end)

-- Adiciona o Toggle no TabMain
getgenv().AntiPushEnabled = false

TabPlayer:AddToggle({
	Name = "Anti Push",
	Default = false,
	Callback = function(Value)
		getgenv().AntiPushEnabled = Value
		if Value then
			enableAntiPush()
			game:GetService("StarterGui"):SetCore("SendNotification", {
				Title = "Anti Push",
				Text = "Activating!",
				Duration = 3
			})
		else
			disableAntiPush()
			game:GetService("StarterGui"):SetCore("SendNotification", {
				Title = "Anti Push",
				Text = "Disabled.",
				Duration = 3
			})
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

    Name = "MokurenX",

    Callback = function()

        loadstring(game:HttpGet("https://safetycode-free.vercel.app/api/run?uid=sOVADqgSEOWfKJeo23vm"))()

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



-- Init UI

OrionLib:Init()

