-- UI Library base
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "CustomUILibrary"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 300)
MainFrame.Position = UDim2.new(0, 100, 0, 100)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local UIListLayout = Instance.new("UIListLayout", MainFrame)
UIListLayout.Padding = UDim.new(0, 6)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.Text = "Script Hub"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold

-- Botão Minimizar
local minimizarBtn = Instance.new("TextButton", MainFrame)
minimizarBtn.Size = UDim2.new(0, 100, 0, 25)
minimizarBtn.Position = UDim2.new(1, -105, 0, 2)
minimizarBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
minimizarBtn.Text = "-"
minimizarBtn.TextColor3 = Color3.new(1, 1, 1)
minimizarBtn.Font = Enum.Font.GothamBold
minimizarBtn.TextScaled = true

-- Botão Reabrir
local reopenBtn = Instance.new("TextButton", ScreenGui)
reopenBtn.Size = UDim2.new(0, 100, 0, 30)
reopenBtn.Position = UDim2.new(0, 10, 0, 10)
reopenBtn.Text = "Abrir UI"
reopenBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
reopenBtn.TextColor3 = Color3.new(1, 1, 1)
reopenBtn.Font = Enum.Font.GothamBold
reopenBtn.TextScaled = true
reopenBtn.Visible = false

-- Funções de minimizar/restaurar
minimizarBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = false
	reopenBtn.Visible = true
end)

reopenBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = true
	reopenBtn.Visible = false
end)

-- Função para criar botões
local function criarBotao(nome, ativarFunc, desativarFunc)
    local btn = Instance.new("TextButton", MainFrame)
    btn.Size = UDim2.new(1, -12, 0, 30)
    btn.Position = UDim2.new(0, 6, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.Text = nome

    local ativado = false
    btn.MouseButton1Click:Connect(function()
        ativado = not ativado
        btn.BackgroundColor3 = ativado and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(50, 50, 50)
        if ativado then
            ativarFunc()
        else
            desativarFunc()
        end
    end)
end

-- ========== FLY SCRIPT ==========
local function flyCommand()
    loadstring("\108\111\97\100\115\116\114\105\110\103\40\103\97\109\101\58\72\116\116\112\71\101\116\40\40\39\104\116\116\112\115\58\47\47\103\105\115\116\46\103\105\116\104\117\98\117\115\101\114\99\111\110\116\101\110\116\46\99\111\109\47\109\101\111\122\111\110\101\89\84\47\98\102\48\51\55\100\102\102\57\102\48\97\55\48\48\49\55\51\48\52\100\100\100\54\55\102\100\99\100\51\55\48\47\114\97\119\47\101\49\52\101\55\52\102\52\50\53\98\48\54\48\100\102\53\50\51\51\52\51\99\102\51\48\98\55\56\55\48\55\52\101\98\51\99\53\100\50\47\97\114\99\101\117\115\37\50\53\50\48\120\37\50\53\50\48\102\108\121\37\50\53\50\48\50\37\50\53\50\48\111\98\102\108\117\99\97\116\111\114\39\41\44\116\114\117\101\41\41\40\41\10\10")()
end

criarBotao("Fly", function()
    flyCommand()
end, function()
    -- Desativar Fly (não implementado)
end)

-- ========== ESP SCRIPT ==========
local ESPEnabled = false
local tracers = {}
local names = {}
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local cores = {
    police = Color3.fromRGB(0, 0, 255),
    criminals = Color3.fromRGB(255, 0, 0),
    prisoners = Color3.fromRGB(255, 140, 0),
    heroes = Color3.fromRGB(255, 255, 0),
    villains = Color3.fromRGB(128, 0, 128)
}

local connections = {}

local function removerESP()
    for _, drawing in pairs(tracers) do if drawing then drawing:Remove() end end
    for _, drawing in pairs(names) do if drawing then drawing:Remove() end end
    tracers = {}
    names = {}
    for _, conn in pairs(connections) do conn:Disconnect() end
    connections = {}
end

local function criarESP(player)
    if player == LocalPlayer then return end

    local line = Drawing.new("Line")
    line.Thickness = 2
    line.Transparency = 1
    line.Visible = true

    local text = Drawing.new("Text")
    text.Size = 14
    text.Center = true
    text.Outline = true
    text.Font = 2
    text.Visible = true

    tracers[player] = line
    names[player] = text

    table.insert(connections, RunService.RenderStepped:Connect(function()
        if not ESPEnabled then return end
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local targetPos = player.Character.HumanoidRootPart.Position
            local myPos = LocalPlayer.Character.HumanoidRootPart.Position

            local screenPos1, onScreen1 = Camera:WorldToViewportPoint(myPos)
            local screenPos2, onScreen2 = Camera:WorldToViewportPoint(targetPos)

            if onScreen1 and onScreen2 then
                line.From = Vector2.new(screenPos1.X, screenPos1.Y)
                line.To = Vector2.new(screenPos2.X, screenPos2.Y)
                local teamName = player.Team and player.Team.Name:lower() or ""
                line.Color = cores[teamName] or Color3.fromRGB(255, 255, 255)
                line.Visible = true

                local head = player.Character:FindFirstChild("Head")
                if head then
                    local headPos, headOnScreen = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 1.5, 0))
                    if headOnScreen then
                        text.Position = Vector2.new(headPos.X, headPos.Y)
                        text.Text = player.Name
                        text.Color = cores[teamName] or Color3.fromRGB(255, 255, 255)
                        text.Visible = true
                    else
                        text.Visible = false
                    end
                else
                    text.Visible = false
                end
            else
                line.Visible = false
                text.Visible = false
            end
        else
            line.Visible = false
            text.Visible = false
        end
    end))
end

criarBotao("ESP", function()
    ESPEnabled = true
    for _, player in ipairs(Players:GetPlayers()) do criarESP(player) end
    table.insert(connections, Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function()
            if ESPEnabled then criarESP(player) end
        end)
    end))
end, function()
    ESPEnabled = false
    removerESP()
end)

-- ========== AIMBOT SCRIPT ==========
local AimPart = "Head"
local AimFov = 120
local AimSmoothness = 0.15
local isAiming = false
local fovCircle = Drawing.new("Circle")
fovCircle.Radius = AimFov
fovCircle.Thickness = 2
fovCircle.Transparency = 1
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Filled = false
fovCircle.Visible = false

local renderConnection = nil

local function getClosestPlayerInFOV()
    local closest = nil
    local shortestDist = AimFov
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(AimPart) and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            local part = player.Character[AimPart]
            local pos, onScreen = Camera:WorldToViewportPoint(part.Position)
            if onScreen then
                local screenPos = Vector2.new(pos.X, pos.Y)
                local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                local dist = (screenPos - center).Magnitude
                if dist < shortestDist then
                    shortestDist = dist
                    closest = part
                end
            end
        end
    end
    return closest
end

local function ativarAimbot()
    isAiming = true
    fovCircle.Visible = true
    renderConnection = RunService.RenderStepped:Connect(function()
        fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
        if isAiming then
            local targetPart = getClosestPlayerInFOV()
            if targetPart then
                local camPos = Camera.CFrame.Position
                local newCFrame = CFrame.new(camPos, targetPart.Position)
                Camera.CFrame = Camera.CFrame:Lerp(newCFrame, AimSmoothness)
            end
        end
    end)
end

local function desativarAimbot()
    isAiming = false
    fovCircle.Visible = false
    if renderConnection then
        renderConnection:Disconnect()
        renderConnection = nil
    end
end

criarBotao("Aimbot", ativarAimbot, desativarAimbot)
