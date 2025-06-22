-- Load OrionLib

local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/Snxdfer/back-ups-for-libs/main/Orion.lua'))()



-- Create main window

local Window = OrionLib:MakeWindow({

    Name = "Cartola Hub v3",

    HidePremium = false,

    SaveConfig = true,

    ConfigFolder = "Config"

})


OrionLib:MakeNotification({

    Name = "Cartola Hub!",

    Content = "Script loader successful!",

    Image = "rbxassetid://4483345998",

    Time = 5

})



-- CreditsTab

local TabCredits = Window:MakeTab({

    Name = "Credits",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})


TabCredits:AddParagraph("Developers", "kwooso4 Vito0296poq FHDYRUTF")

TabCredits:AddParagraph("Admin", "Tanjiro_kamado7Wk")

TabCredits:AddParagraph("Ó Cartola Hub v3 foi encerrado", "ó Novo Hub vai vim")
TabCredits:AddParagraph("the Cartola Hub v3 was closed", "Oh New Hub is coming")


TabCredits:AddButton({

    Name = "New Cartola Hub",

    Callback = function()

           loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Cartola-Hub-Brookhaven-/refs/heads/main/Cartola.txt"))()
        
    end

})



-- Main Tab

local TabMain = Window:MakeTab({

    Name = "Main",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})


TabMain:AddButton({

    Name = "Fly Cartola",

    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-Cartola-Hub-/refs/heads/main/README.md"))()
        
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

-- Ativa o modo manhã com iluminação suave
local function enableMorningLight()
	fullBrightEnabled = true

	Lighting.Brightness = 1.5
	Lighting.Ambient = Color3.fromRGB(180, 180, 160)
	Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 170)
	Lighting.ClockTime = 7
	Lighting.FogEnd = 1e9
	Lighting.GlobalShadows = true

	-- Protege as propriedades contra mudanças externas
	table.insert(connections, Lighting:GetPropertyChangedSignal("ClockTime"):Connect(function()
		if fullBrightEnabled then Lighting.ClockTime = 7 end
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

	print(" ativada.")
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

	print(" restaurada.")
end

-- Toggle no TabMain
TabMain:AddToggle({
	Name = "From Tomorrow",
	Default = false,
	Callback = function(Value)
		if Value then
			enableMorningLight()
		else
			disableMorningLight()
		end
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





-- Houses Tab

local TabHouses = Window:MakeTab({

    Name = "Houses",

    Icon = "rbxassetid://4483345998",

    PremiumOnly = false

})


-- Botão para remover ban manualmente em Brookhaven
TabHouses:AddButton({
    Name = "Remove Ban",
    Callback = function()
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local Workspace = game:GetService("Workspace")

        -- Função para identificar barreiras de ban
        local function isBanWall(part)
            return part:IsA("BasePart")
                and part.Transparency == 1
                and part.Color == Color3.fromRGB(255, 0, 0)
                and part.CanCollide == true
                and part.Size.Magnitude > 10 -- normalmente são grandes
        end

        -- Remover barreiras já existentes
        local count = 0
        for _, part in pairs(Workspace:GetDescendants()) do
            if isBanWall(part) then
                part:Destroy()
                count += 1
            end
        end

        print("Remoção de ban manual completa. Total removido:", count)
    end
})



local atravesarPortasAtivo = false
local portasOriginais = {}

-- Função para ativar colisão falsa (atravessar)
local function ativarAtravessarPortas()
	atravesarPortasAtivo = true
	portasOriginais = {}

	for _, obj in ipairs(workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name:lower():match("door") then
			table.insert(portasOriginais, {part = obj, canCollide = obj.CanCollide})
			obj.CanCollide = false
		end
	end
end

-- Função para restaurar colisão
local function desativarAtravessarPortas()
	atravesarPortasAtivo = false
	for _, data in ipairs(portasOriginais) do
		if data.part and data.part:IsDescendantOf(workspace) then
			data.part.CanCollide = data.canCollide
		end
	end
	portasOriginais = {}
end

-- Toggle no TabMain
TabHouses:AddToggle({
	Name = "Go Through Doors",
	Default = false,
	Callback = function(Value)
		if Value then
			ativarAtravessarPortas()
		else
			desativarAtravessarPortas()
		end
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



local antiVehicleSeatEnabled = false

local characterConnection = nil

local seatedConnection = nil



local function setupAntiVehicleSeat(character)

    local humanoid = character:WaitForChild("Humanoid", 5)

    if humanoid then

        if seatedConnection then

            seatedConnection:Disconnect()

        end

        seatedConnection = humanoid.Seated:Connect(function(isSeated, seat)

            if isSeated and antiVehicleSeatEnabled and seat and seat:IsA("VehicleSeat") then

                humanoid.Sit = false

                -- Tenta mover o jogador levemente para evitar travamento

                local hrp = character:FindFirstChild("HumanoidRootPart")

                if hrp then

                    hrp.CFrame = hrp.CFrame + Vector3.new(0, 5, 0)

                end

            end

        end)

    end

end



TabPlayer:AddToggle({

    Name = "Anti Vehicle",

    Default = false,

    Callback = function(Value)

        antiVehicleSeatEnabled = Value



        if antiVehicleSeatEnabled then

            if characterConnection then

                characterConnection:Disconnect()

            end



            characterConnection = LocalPlayer.CharacterAdded:Connect(function(character)

                setupAntiVehicleSeat(character)

            end)



            if LocalPlayer.Character then

                setupAntiVehicleSeat(LocalPlayer.Character)

            end

        else

            if characterConnection then

                characterConnection:Disconnect()

                characterConnection = nil

            end

            if seatedConnection then

                seatedConnection:Disconnect()

                seatedConnection = nil

            end

        end

    end

})



local antiSeatEnabled = false

local seatConnection = nil



TabPlayer:AddToggle({

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
    Name = "Gumball Hub",
    Callback = function()

        loadstring(game:HttpGet("https://raw.githubusercontent.com/JaozinScripts/Gumball-Hub/refs/heads/main/GumballHubRetorn2.1.1.1.lua"))()

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

