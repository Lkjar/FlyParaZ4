
-- Exclusivo para PC
-- Feito por: snow
-- Exclusivamente para: z4

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local Player = Players.LocalPlayer
local Character = Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Variáveis do Fly
local Flying = false
local FlySpeed = 50
local BodyVelocity = nil
local BodyAngularVelocity = nil
local FlyConnection = nil

-- Controles de movimento
local MoveVector = Vector3.new(0, 0, 0)
local Keys = {
    W = false,
    A = false,
    S = false,
    D = false,
    Space = false,
    LeftShift = false
}

-- Função para criar BodyMovers
local function CreateBodyMovers()
    if BodyVelocity then BodyVelocity:Destroy() end
    if BodyAngularVelocity then BodyAngularVelocity:Destroy() end
    
    BodyVelocity = Instance.new("BodyVelocity")
    BodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BodyVelocity.Velocity = Vector3.new(0, 0, 0)
    BodyVelocity.Parent = RootPart
    
    BodyAngularVelocity = Instance.new("BodyAngularVelocity")
    BodyAngularVelocity.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    BodyAngularVelocity.AngularVelocity = Vector3.new(0, 0, 0)
    BodyAngularVelocity.Parent = RootPart
end

-- Função para remover BodyMovers
local function RemoveBodyMovers()
    if BodyVelocity then
        BodyVelocity:Destroy()
        BodyVelocity = nil
    end
    if BodyAngularVelocity then
        BodyAngularVelocity:Destroy()
        BodyAngularVelocity = nil
    end
end

-- Função principal do Fly
local function StartFly()
    if Flying then return end
    Flying = true
    
    CreateBodyMovers()
    
    -- Desabilita a gravidade do personagem
    Humanoid.PlatformStand = true
    
    -- Loop principal do fly
    FlyConnection = RunService.Heartbeat:Connect(function()
        if not Flying then return end
        
        local Camera = workspace.CurrentCamera
        local CameraCFrame = Camera.CFrame
        
        -- Calcula o vetor de movimento baseado nas teclas pressionadas
        MoveVector = Vector3.new(0, 0, 0)
        
        if Keys.W then
            MoveVector = MoveVector + CameraCFrame.LookVector
        end
        if Keys.S then
            MoveVector = MoveVector - CameraCFrame.LookVector
        end
        if Keys.A then
            MoveVector = MoveVector - CameraCFrame.RightVector
        end
        if Keys.D then
            MoveVector = MoveVector + CameraCFrame.RightVector
        end
        if Keys.Space then
            MoveVector = MoveVector + Vector3.new(0, 1, 0)
        end
        if Keys.LeftShift then
            MoveVector = MoveVector - Vector3.new(0, 1, 0)
        end
        
        -- Normaliza o vetor e aplica a velocidade
        if MoveVector.Magnitude > 0 then
            MoveVector = MoveVector.Unit
        end
        
        BodyVelocity.Velocity = MoveVector * FlySpeed
    end)
end

-- Função para parar o Fly
local function StopFly()
    if not Flying then return end
    Flying = false
    
    if FlyConnection then
        FlyConnection:Disconnect()
        FlyConnection = nil
    end
    
    RemoveBodyMovers()
    Humanoid.PlatformStand = false
end

-- Gerenciamento de input do teclado
UserInputService.InputBegan:Connect(function(Input, GameProcessed)
    if GameProcessed then return end
    
    if Input.KeyCode == Enum.KeyCode.W then
        Keys.W = true
    elseif Input.KeyCode == Enum.KeyCode.A then
        Keys.A = true
    elseif Input.KeyCode == Enum.KeyCode.S then
        Keys.S = true
    elseif Input.KeyCode == Enum.KeyCode.D then
        Keys.D = true
    elseif Input.KeyCode == Enum.KeyCode.Space then
        Keys.Space = true
    elseif Input.KeyCode == Enum.KeyCode.LeftShift then
        Keys.LeftShift = true
    end
end)

UserInputService.InputEnded:Connect(function(Input)
    if Input.KeyCode == Enum.KeyCode.W then
        Keys.W = false
    elseif Input.KeyCode == Enum.KeyCode.A then
        Keys.A = false
    elseif Input.KeyCode == Enum.KeyCode.S then
        Keys.S = false
    elseif Input.KeyCode == Enum.KeyCode.D then
        Keys.D = false
    elseif Input.KeyCode == Enum.KeyCode.Space then
        Keys.Space = false
    elseif Input.KeyCode == Enum.KeyCode.LeftShift then
        Keys.LeftShift = false
    end
end)

-- Interface Rayfield
local Window = Rayfield:CreateWindow({
    Name = "The Literally - Fly Script by snow for z4",
    LoadingTitle = "Carregando Fly Script para z4",
    LoadingSubtitle = "feito por snow usando Rayfield",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "LiterallyFlyScript",
        FileName = "Config"
    },
    Discord = {
        Enabled = false,
        Invite = "",
        RememberJoins = false
    },
    KeySystem = false
})

-- Aba principal
local MainTab = Window:CreateTab("🚀 Fly Controls", 4483362458)

-- Toggle do Fly
local FlyToggle = MainTab:CreateToggle({
    Name = "Ativar Fly",
    CurrentValue = false,
    Flag = "FlyToggle",
    Callback = function(Value)
        if Value then
            StartFly()
        else
            StopFly()
        end
    end,
})

-- Slider de velocidade
local SpeedSlider = MainTab:CreateSlider({
    Name = "Velocidade do Fly",
    Range = {1, 200},
    Increment = 1,
    Suffix = " studs/s",
    CurrentValue = 50,
    Flag = "FlySpeed",
    Callback = function(Value)
        FlySpeed = Value
    end,
})

-- Informações de controle
MainTab:CreateParagraph({
    Title = "Controles:",
    Content = "W, A, S, D - Movimento horizontal\nSpace - Subir\nLeft Shift - Descer\n\nFeito por snow exclusivamente para z4\nPara uso no The Literally"
})

-- Botões de velocidade pré-definida
MainTab:CreateButton({
    Name = "Velocidade Lenta (25)",
    Callback = function()
        SpeedSlider:Set(25)
    end,
})

MainTab:CreateButton({
    Name = "Velocidade Média (50)",
    Callback = function()
        SpeedSlider:Set(50)
    end,
})

MainTab:CreateButton({
    Name = "Velocidade Rápida (100)",
    Callback = function()
        SpeedSlider:Set(100)
    end,
})

MainTab:CreateButton({
    Name = "Velocidade Extrema (200)",
    Callback = function()
        SpeedSlider:Set(200)
    end,
})

-- Aba de configurações
local ConfigTab = Window:CreateTab("⚙️ Configurações", 4483362458)

-- Toggle para auto-fly ao spawnar
local AutoFlyToggle = ConfigTab:CreateToggle({
    Name = "Auto-Fly ao Spawnar",
    CurrentValue = false,
    Flag = "AutoFly",
    Callback = function(Value)
        -- Implementação do auto-fly será ativada no próximo spawn
    end,
})

-- Botão para resetar personagem
ConfigTab:CreateButton({
    Name = "Resetar Personagem",
    Callback = function()
        StopFly()
        Player.Character.Humanoid.Health = 0
    end,
})

-- Gerenciamento de respawn do personagem
Player.CharacterAdded:Connect(function(NewCharacter)
    Character = NewCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
    
    -- Para o fly se estiver ativo
    if Flying then
        StopFly()
        FlyToggle:Set(false)
    end
    
    -- Auto-fly se estiver ativado
    wait(1) -- Espera o personagem carregar completamente
    if Rayfield.Flags["AutoFly"] and Rayfield.Flags["AutoFly"].CurrentValue then
        FlyToggle:Set(true)
    end
end)

-- Limpeza ao sair
game:GetService("Players").PlayerRemoving:Connect(function(player)
    if player == Player then
        StopFly()
    end
end)

Rayfield:Notify({
    Title = "Script carregado para z4!",
    Content = "Fly script feito por snow exclusivamente para z4 no The Literally!",
    Duration = 6.5,
    Image = 4483362458,
    Actions = {
        Ignore = {
            Name = "Ok!",
            Callback = function()
                print("Notificação fechada")
            end
        },
    },
})
