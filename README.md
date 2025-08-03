local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()

local Window = OrionLib:MakeWindow({Name = "RichardHub (PvP)", HidePremium = false, SaveConfig = true, ConfigFolder = "OrionTest"})

--Tab
local JogadorTab = Window:MakeTab({
    Name = "PvP",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

    JogadorTab:AddParagraph(" By Richard","")

--Notify
    OrionLib:MakeNotification({
    Name = "Notificação",
    Content = "Hub Executado!",
    Image = "rbxassetid://4483345998",
    Time = 5
})

    local Section = JogadorTab:AddSection({
    Name = "OP"
})

--Botton
    JogadorTab:AddButton({
    Name = "Invisibilidade",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/3Rnd9rHf"))()        
    end    
})

    local Section = JogadorTab:AddSection({
    Name = "HeadExpander"
})

--Botton
    JogadorTab:AddButton({
    Name = "EnableHeadExpander",
    Callback = function()
        local Players = game:GetService("Players")
        local localPlayer = Players.LocalPlayer

        local HEAD_HITBOX_SCALE = Vector3.new(6, 6, 6) -- Tamanho da hitbox da cabeça

        local function scaleHeadHitbox(character)
            -- Ignora o jogador local
            if character:IsDescendantOf(localPlayer) then
                return
            end
            
            local head = character:FindFirstChild("Head")
            if head and head:IsA("BasePart") then
                -- Aumenta a hitbox (para detectar tiros)
                head.Size = HEAD_HITBOX_SCALE
                
                -- Configuração para manter detecção de hits, mas sem colisão física
                head.CanCollide = false  -- Não colide com o mundo (paredes, chão)
                head.CanTouch = false    -- Evita eventos de toque indesejados
                head.CanQuery = true     -- IMPORTANTE: Permite que tiros (raycasts) acertem
                
                -- Mantém o visual original (opcional)
                local mesh = head:FindFirstChildOfClass("SpecialMesh")
                if mesh then
                    mesh.Scale = Vector3.new(1, 1, 1)
                end
            end
        end

        local function onCharacterAdded(character)
            scaleHeadHitbox(character)
            character.ChildAdded:Connect(function(child)
                if child.Name == "Head" then
                    scaleHeadHitbox(character)
                end
            end)
        end

        local function onPlayerAdded(player)
            if player == localPlayer then return end
            player.CharacterAdded:Connect(onCharacterAdded)
            if player.Character then
                onCharacterAdded(player.Character)
            end
        end

        -- Aplica em jogadores existentes
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= localPlayer then
                onPlayerAdded(player)
            end
        end

        -- Monitora novos jogadores
        Players.PlayerAdded:Connect(function(player)
            if player ~= localPlayer then
                onPlayerAdded(player)
            end
        end)
    end    
})

    local Section = JogadorTab:AddSection({
    Name = " AimBot 🎯 "
})

    JogadorTab:AddButton({
    Name = "EnableAimBot",
    Callback = function()
        
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local camera = workspace.CurrentCamera
local runService = game:GetService("RunService")
local uis = game:GetService("UserInputService")
local target = nil
local aiming = false

-- Config
local circleRadius = 70
local circleOffset = Vector2.new(0, 0)

-- GUI
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.ResetOnSpawn = false

local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 220, 0, 150)
main.Position = UDim2.new(1, -230, 0, 10)
main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
main.BorderSizePixel = 0
main.Name = "AimbotMain"

local toggle = Instance.new("TextButton", main)
toggle.Size = UDim2.new(0, 180, 0, 30)
toggle.Position = UDim2.new(0, 10, 0, 30)
toggle.Text = "Aimbot: OFF"
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.BackgroundColor3 = Color3.new(0, 0, 0)

local circle = Drawing.new("Circle")
circle.Radius = circleRadius
circle.Filled = false
circle.Color = Color3.fromRGB(255, 0, 0)
circle.Visible = false
circle.Thickness = 1

local up = Instance.new("TextButton", main)
up.Size = UDim2.new(0, 30, 0, 20)
up.Position = UDim2.new(0, 10, 0, 70)
up.Text = "+"
local down = Instance.new("TextButton", main)
down.Size = UDim2.new(0, 30, 0, 20)
down.Position = UDim2.new(0, 10, 0, 95)
down.Text = "-"

local left = Instance.new("TextButton", main)
left.Size = UDim2.new(0, 30, 0, 20)
left.Position = UDim2.new(0, 45, 0, 95)
left.Text = "<"
local right = Instance.new("TextButton", main)
right.Size = UDim2.new(0, 30, 0, 20)
right.Position = UDim2.new(0, 80, 0, 95)
right.Text = ">"

local sizePlus = Instance.new("TextButton", main)
sizePlus.Size = UDim2.new(0, 30, 0, 20)
sizePlus.Position = UDim2.new(0, 120, 0, 95)
sizePlus.Text = "+S"

local sizeMinus = Instance.new("TextButton", main)
sizeMinus.Size = UDim2.new(0, 30, 0, 20)
sizeMinus.Position = UDim2.new(0, 155, 0, 95)
sizeMinus.Text = "-S"

local minimize = Instance.new("TextButton", main)
minimize.Size = UDim2.new(0, 20, 0, 20)
minimize.Position = UDim2.new(1, -22, 0, 2)
minimize.Text = "-"
minimize.TextColor3 = Color3.new(1, 1, 1)
minimize.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local reopen = Instance.new("TextButton", screenGui)
reopen.Size = UDim2.new(0, 60, 0, 25)
reopen.Position = UDim2.new(1, -70, 0, 10)
reopen.Text = "Abrir"
reopen.Visible = false
reopen.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
reopen.TextColor3 = Color3.new(1, 1, 1)

-- RGB effect
task.spawn(function()
	while true do
		for i = 0, 1, 0.01 do
			local color = Color3.fromHSV(i, 1, 1)
			toggle.BackgroundColor3 = color
			minimize.BackgroundColor3 = color
			main.BackgroundColor3 = color:lerp(Color3.new(0, 0, 0), 0.5)
			reopen.BackgroundColor3 = color
			circle.Color = color
			task.wait()
		end
	end
end)

-- Minimizar / Reabrir
minimize.MouseButton1Click:Connect(function()
	main.Visible = false
	reopen.Visible = true
end)

reopen.MouseButton1Click:Connect(function()
	main.Visible = true
	reopen.Visible = false
end)

-- Toggle aimbot
toggle.MouseButton1Click:Connect(function()
	aiming = not aiming
	toggle.Text = aiming and "Aimbot: ON" or "Aimbot: OFF"
	circle.Visible = aiming
	if not aiming then target = nil end
end)

-- Mover círculo
up.MouseButton1Click:Connect(function()
	circleOffset = circleOffset + Vector2.new(0, -5)
end)
down.MouseButton1Click:Connect(function()
	circleOffset = circleOffset + Vector2.new(0, 5)
end)
left.MouseButton1Click:Connect(function()
	circleOffset = circleOffset + Vector2.new(-5, 0)
end)
right.MouseButton1Click:Connect(function()
	circleOffset = circleOffset + Vector2.new(5, 0)
end)

-- Alterar tamanho do círculo
sizePlus.MouseButton1Click:Connect(function()
	circleRadius += 5
	circle.Radius = circleRadius
end)
sizeMinus.MouseButton1Click:Connect(function()
	circleRadius = math.max(5, circleRadius - 5)
	circle.Radius = circleRadius
end)

-- Verificar se é inimigo (detecção de time)
local function isEnemy(plr)
	if plr.Team and player.Team then
		return plr.Team ~= player.Team
	else
		return true -- Se não tiver times, considera inimigo
	end
end

-- Visibilidade por raycast
local function isVisible(part)
	local origin = camera.CFrame.Position
	local direction = (part.Position - origin).Unit * 1000
	local ray = Ray.new(origin, direction)
	local hit = workspace:FindPartOnRay(ray, player.Character, false, true)
	return hit and (hit:IsDescendantOf(part.Parent) or hit == part)
end

-- Buscar alvo
local function getTarget()
	local players = game.Players:GetPlayers()
	local closest = nil
	local shortest = math.huge

	for _, plr in ipairs(players) do
		if plr ~= player and plr.Character and plr.Character:FindFirstChild("Head") and isEnemy(plr) then
			local pos, onScreen = camera:WorldToViewportPoint(plr.Character.Head.Position)
			if onScreen then
				local dist = (Vector2.new(pos.X, pos.Y) - (Vector2.new(camera.ViewportSize.X, camera.ViewportSize.Y)/2 + circleOffset)).Magnitude
				if dist <= circleRadius and dist < shortest and isVisible(plr.Character.Head) then
					shortest = dist
					closest = plr
				end
			end
		end
	end

	return closest
end

-- Aimbot loop
runService.RenderStepped:Connect(function()
	circle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2) + circleOffset

	if aiming then
		local newTarget = getTarget()

		if newTarget and newTarget.Character and newTarget.Character:FindFirstChild("Head") then
			local headPos = newTarget.Character.Head.Position
			camera.CFrame = CFrame.new(camera.CFrame.Position, headPos)
		end
	end
end)

    end    
})

    JogadorTab:AddButton({
    Name = "ESP ( Melhor Que Ta Tendo ! Pc/Mobile )",
    Callback = function()
       loadstring(game:HttpGet('https://raw.githubusercontent.com/Lucasfin000/SpaceHub/main/UESP'))()
    end    
})

    local Section = JogadorTab:AddSection({
    Name = "FoV"
})

--Fov
    JogadorTab:AddButton({
    Name = "Fov (120)",
    Callback = function()
        workspace.CurrentCamera.FieldOfView = 120
    end    
})


    JogadorTab:AddButton({
    Name = "Fov (100)",
    Callback = function()
        workspace.CurrentCamera.FieldOfView = 100
    end    
})

    JogadorTab:AddButton({
    Name = "Fov (80)",
    Callback = function()
        workspace.CurrentCamera.FieldOfView = 80
    end    
})

    JogadorTab:AddButton({
    Name = "Fov (60)",
    Callback = function()
        workspace.CurrentCamera.FieldOfView = 60
    end    
})

    JogadorTab:AddButton({
    Name = "Fov (40)",
    Callback = function()
        workspace.CurrentCamera.FieldOfView = 40
    end    
})

    local JogadorTab = Window:MakeTab({
    Name = "Jogador",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "SpeeD"
})

local speedValue = 16 -- Valor padrão de velocidade
local speedEnabled = false

-- Função para aplicar a velocidade
local function applySpeed()
    if speedEnabled then
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = speedValue
    end
end

-- Slider de velocidade
JogadorTab:AddSlider({
    Name = "Velocidade",
    Min = 16,
    Max = 1000,
    Default = 16,
    Color = Color3.fromRGB(255, 0, 0),
    Increment = 1,
    ValueName = "unidades",
    Callback = function(value)
        speedValue = value
        applySpeed()
    end
})

-- Toggle para ativar/desativar
JogadorTab:AddToggle({
    Name = "Ativar Velocidade",
    Default = false,
    Callback = function(state)
        speedEnabled = state
        if state then
            applySpeed()
        else
            -- Resetar para velocidade padrão quando desativado
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
        end
    end
})

-- Conectar para reaplicar velocidade quando o personagem respawnar
game.Players.LocalPlayer.CharacterAdded:Connect(function(character)
    character:WaitForChild("Humanoid")
    if speedEnabled then
        applySpeed()
    end
end)

--Section
    local Section = JogadorTab:AddSection({
    Name = "JumP"
})

local jumpValue = 50 -- Valor padrão de pulo (normalmente 50 no Roblox)
local jumpEnabled = false

-- Função para aplicar o pulo
local function applyJump()
    if jumpEnabled then
        local humanoid = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.JumpPower = jumpValue
        end
    end
end

-- Slider de altura do pulo
JogadorTab:AddSlider({
    Name = "Altura do Pulo",
    Min = 50,
    Max = 1000,
    Default = 50,
    Color = Color3.fromRGB(0, 255, 0),
    Increment = 1,
    ValueName = "unidades",
    Callback = function(value)
        jumpValue = value
        applyJump()
    end
})

-- Toggle para ativar/desativar
JogadorTab:AddToggle({
    Name = "Ativar Pulo Alto",
    Default = false,
    Callback = function(state)
        jumpEnabled = state
        if state then
            applyJump()
        else
            -- Resetar para pulo padrão quando desativado
            local humanoid = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.JumpPower = 50
            end
        end
    end
})

-- Conectar para reaplicar quando o personagem respawnar
game.Players.LocalPlayer.CharacterAdded:Connect(function(character)
    character:WaitForChild("Humanoid")
    if jumpEnabled then
        applyJump()
    end
end)

--Section
    local Section = JogadorTab:AddSection({
    Name = "InfiniteJumP"
})

local infiniteJumpEnabled = false
local connection = nil

-- Função para ativar o pulo infinito
local function enableInfiniteJump()
    if connection then connection:Disconnect() end

    connection = game:GetService("UserInputService").JumpRequest:Connect(function()
        if infiniteJumpEnabled then
            game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
        end
    end)
end

-- Toggle para ativar/desativar
JogadorTab:AddToggle({
    Name = "Ativar Pulo Infinito",
    Default = false,
    Callback = function(state)
        infiniteJumpEnabled = state
        if state then
            enableInfiniteJump()
            OrionLib:MakeNotification({
                Name = "Pulo Infinito",
                Content = "Pulo infinito ATIVADO!",
                Time = 3
            })
        else
            if connection then
                connection:Disconnect()
                connection = nil
            end
            OrionLib:MakeNotification({
                Name = "Pulo Infinito",
                Content = "Pulo infinito DESATIVADO!",
                Time = 3
            })
        end
    end
})



-- Reconectar quando o personagem respawnar
game.Players.LocalPlayer.CharacterAdded:Connect(function(character)
    if infiniteJumpEnabled then
        enableInfiniteJump()
    end
end)

--Section
    local Section = JogadorTab:AddSection({
    Name = "Fly"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = " 🚨🚨 ATENÇÃO! ALGUNS MAPAS TÊM BYPASS PARA FLY! 🚨🚨 "
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "LISTA ATUALIZADA 2025:"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "DEAD RAILS ☠️ "
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "STEAL A BRAINROT 🧠 "
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "BUILD A PLANE ✈️ "
})


--Section
    local Section = JogadorTab:AddSection({
    Name = "DOORS 🚪"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "GROW A GARDEN 🌿"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "OBBY/SANDBOX 🧱"
})

    local Section = JogadorTab:AddSection({
    Name = "  ⚠️⚠️⚠️ CUIDADO !!! ⚠️⚠️⚠️  "
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "Anti-Cheat Pode BANIR! ❌"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "Use em CONTAS ALTERNATIVAS! 🔄"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "Teste em SERVERS PRIVADOS! 🔒"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "🎮 QUER MAIS DETALHES? CHAMA DM! 👇😎"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "🚀  VAI NO MEU INSTA OU TTK  🚀"
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "⚠️(Atualizado em 2025 - Sujeito a mudanças)⚠️"
})

--Botton
    JogadorTab:AddButton({
    Name = "Fly",
    Callback = function()

 loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()

    end   
})

--Tab
    local JogadorTab = Window:MakeTab({
    Name = "Outros",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

--Section
    local Section = JogadorTab:AddSection({
    Name = "Outros Scripts"
})

--Botton
    JogadorTab:AddButton({
    Name = "InfiniteYield",
    Callback = function()
        loadstring(game:HttpGet(('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'),true))()
    end    
})

    local JogadorTab = Window:MakeTab({
    Name = "Credits",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

    local Section = JogadorTab:AddSection({
    Name = "Me Siga Nas Redes Sociais !  🙆🏻   "
})

local Section = JogadorTab:AddSection({
    Name = "Discord  :  https://discord.gg/nBQwGAeq "
})

local Section = JogadorTab:AddSection({
    Name = "Instagram  📷  :  rd.75e "
})

local Section = JogadorTab:AddSection({
    Name = "TikTok  🇯  :  rd.75e "
})

local Section = JogadorTab:AddSection({
    Name = "YouTube  :  @Guts0227  "
})
