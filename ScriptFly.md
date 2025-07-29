-- Script Roblox com Rayfield - Fly e Noclip para PC
-- Carrega a biblioteca Rayfield
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Serviços do Roblox
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- Variáveis do jogador
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- Variáveis de controle
local flyEnabled = false
local noclipEnabled = false
local flySpeed = 50
local bodyVelocity = nil
local bodyAngularVelocity = nil

-- Cria a interface Rayfield
local Window = Rayfield:CreateWindow({
    Name = "Script Utilitário - Fly & Noclip",
    LoadingTitle = "Carregando Script",
    LoadingSubtitle = "by Seu Nome",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "RayfieldConfig",
        FileName = "FlyNoclipScript"
    },
    KeySystem = false
})

-- Tab Principal
local MainTab = Window:CreateTab("Principal", 4483362458)

-- Seção de Movimento
local MovementSection = MainTab:CreateSection("Controles de Movimento")

-- Toggle para Fly
local FlyToggle = MainTab:CreateToggle({
    Name = "Ativar Fly (F para voar)",
    CurrentValue = false,
    Flag = "FlyToggle",
    Callback = function(Value)
        flyEnabled = Value
        if not flyEnabled then
            stopFly()
        end
    end,
})

-- Slider para velocidade do Fly
local FlySpeedSlider = MainTab:CreateSlider({
    Name = "Velocidade do Fly",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 50,
    Flag = "FlySpeed",
    Callback = function(Value)
        flySpeed = Value
    end,
})

-- Toggle para Noclip
local NoclipToggle = MainTab:CreateToggle({
    Name = "Ativar Noclip",
    CurrentValue = false,
    Flag = "NoclipToggle",
    Callback = function(Value)
        noclipEnabled = Value
    end,
})

-- Seção de Informações
local InfoSection = MainTab:CreateSection("Informações")

MainTab:CreateParagraph({
    Title = "Como usar:",
    Content = "• Ative o Fly e pressione F para começar a voar\n• Use WASD para se mover no ar\n• Space para subir, Shift para descer\n• Noclip permite atravessar paredes"
})

-- Função para iniciar o fly
function startFly()
    if bodyVelocity then return end
    
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = rootPart
    
    bodyAngularVelocity = Instance.new("BodyAngularVelocity")
    bodyAngularVelocity.MaxTorque = Vector3.new(4000, 4000, 4000)
    bodyAngularVelocity.AngularVelocity = Vector3.new(0, 0, 0)
    bodyAngularVelocity.Parent = rootPart
end

-- Função para parar o fly
function stopFly()
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
    if bodyAngularVelocity then
        bodyAngularVelocity:Destroy()
        bodyAngularVelocity = nil
    end
end

-- Função para atualizar o movimento do fly
function updateFlyMovement()
    if not flyEnabled or not bodyVelocity then return end
    
    local camera = workspace.CurrentCamera
    local moveVector = Vector3.new(0, 0, 0)
    
    -- Detecta teclas pressionadas
    if UserInputService:IsKeyDown(Enum.KeyCode.W) then
        moveVector = moveVector + camera.CFrame.LookVector
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) then
        moveVector = moveVector - camera.CFrame.LookVector
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.A) then
        moveVector = moveVector - camera.CFrame.RightVector
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.D) then
        moveVector = moveVector + camera.CFrame.RightVector
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
        moveVector = moveVector + Vector3.new(0, 1, 0)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
        moveVector = moveVector + Vector3.new(0, -1, 0)
    end
    
    -- Aplica a velocidade
    bodyVelocity.Velocity = moveVector * flySpeed
end

-- Função para noclip
function updateNoclip()
    if not noclipEnabled then return end
    
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end

-- Event listeners
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.F and flyEnabled then
        if bodyVelocity then
            stopFly()
        else
            startFly()
        end
    end
end)

-- Atualização contínua
RunService.Heartbeat:Connect(function()
    updateFlyMovement()
    updateNoclip()
end)

-- Atualiza referências quando o personagem respawna
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    
    -- Para o fly quando respawna
    stopFly()
end)

-- Notificação de carregamento
Rayfield:Notify({
    Title = "Script Carregado!",
    Content = "Fly e Noclip estão prontos para uso",
    Duration = 3,
    Image = 4483362458
})
