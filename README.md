local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- Configurações
local ENABLED = false
local FOV_RADIUS = 200
local TEAM_CHECK = true

-- Funções de utilidade
local function GetClosestPlayer()
    local ClosestPlayer = nil
    local ShortestDistance = FOV_RADIUS

    for _, Player in pairs(Players:GetPlayers()) do
        if Player ~= LocalPlayer then
            -- Verificação de time
            if TEAM_CHECK and Player.Team == LocalPlayer.Team then
                continue
            end

            local Character = Player.Character
            if Character and Character:FindFirstChild("Humanoid") and Character:FindFirstChild("HumanoidRootPart") and Character.Humanoid.Health > 0 then
                local Vector, OnScreen = Camera:WorldToScreenPoint(Character.HumanoidRootPart.Position)
                if OnScreen then
                    local Distance = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(Vector.X, Vector.Y)).Magnitude
                    if Distance < ShortestDistance then
                        ClosestPlayer = Character
                        ShortestDistance = Distance
                    end
                end
            end
        end
    end
    
    return ClosestPlayer
end

-- Desenhar FOV
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.NumSides = 60
FOVCircle.Radius = FOV_RADIUS
FOVCircle.Filled = false
FOVCircle.Visible = true
FOVCircle.Color = Color3.fromRGB(255, 255, 255)
FOVCircle.Transparency = 1

-- Loop principal
RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y)
    
    if ENABLED then
        local Target = GetClosestPlayer()
        if Target then
            local Vector = Camera:WorldToScreenPoint(Target.HumanoidRootPart.Position)
            mousemoverel((Vector.X - Mouse.X) / 10, (Vector.Y - Mouse.Y) / 10)
        end
    end
end)

-- Toggle com tecla P
UserInputService.InputBegan:Connect(function(Input)
    if Input.KeyCode == Enum.KeyCode.P then
        ENABLED = not ENABLED
        if ENABLED then
            print("Aimbot: ON")
        else
            print("Aimbot: OFF")
        end
    end
end)
