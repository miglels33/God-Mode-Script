local Players = game:GetService("Players")

-- Dicionário para armazenar o estado do God Mode de cada jogador
local godModePlayers = {}

local function applyGodMode(player)
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    
    -- Configurações invencíveis
    humanoid.MaxHealth = math.huge
    humanoid.Health = math.huge
    
    -- Conexão para prevenir qualquer dano
    humanoid:GetPropertyChangedSignal("Health"):Connect(function()
        if godModePlayers[player.UserId] and humanoid.Health < humanoid.MaxHealth then
            humanoid.Health = humanoid.MaxHealth
        end
    end)
end

local function createGodModeGUI(player)
    -- Cria a interface gráfica
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "GodModeGUI"
    screenGui.ResetOnSpawn = false
    
    -- Frame principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 200, 0, 50)
    mainFrame.Position = UDim2.new(0.5, -100, 0, 10)
    mainFrame.AnchorPoint = Vector2.new(0.5, 0)
    mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = screenGui
    
    -- Efeito de borda arredondada
    local uiCorner = Instance.new("UICorner")
    uiCorner.CornerRadius = UDim.new(0, 8)
    uiCorner.Parent = mainFrame
    
    -- Botão de God Mode
    local godButton = Instance.new("TextButton")
    godButton.Name = "GodButton"
    godButton.Size = UDim2.new(0.9, 0, 0.7, 0)
    godButton.Position = UDim2.new(0.05, 0, 0.15, 0)
    godButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    godButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    godButton.Text = "GOD MODE: OFF"
    godButton.Font = Enum.Font.GothamBold
    godButton.TextSize = 14
    godButton.Parent = mainFrame
    
    -- Borda arredondada para o botão
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 6)
    buttonCorner.Parent = godButton
    
    -- Função para alternar o God Mode
    local function toggleGodMode()
        godModePlayers[player.UserId] = not godModePlayers[player.UserId]
        
        if godModePlayers[player.UserId] then
            godButton.Text = "GOD MODE: ON"
            godButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
            applyGodMode(player)
        else
            godButton.Text = "GOD MODE: OFF"
            godButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            
            -- Restaura configurações normais
            if player.Character then
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid.MaxHealth = 100
                    humanoid.Health = 100
                end
            end
        end
    end
    
    -- Conecta o clique do botão
    godButton.MouseButton1Click:Connect(toggleGodMode)
    
    -- Atualiza quando o personagem muda
    player.CharacterAdded:Connect(function(character)
        if godModePlayers[player.UserId] then
            applyGodMode(player)
        end
    end)
    
    -- Adiciona a GUI ao jogador
    screenGui.Parent = player:WaitForChild("PlayerGui")
end

-- Configura para novos jogadores
Players.PlayerAdded:Connect(function(player)
    godModePlayers[player.UserId] = false
    createGodModeGUI(player)
end)

-- Configura para jogadores já conectados
for _, player in ipairs(Players:GetPlayers()) do
    godModePlayers[player.UserId] = false
    createGodModeGUI(player)
end
