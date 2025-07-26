-- VoxSeas Fruit TP GUI Script
-- Autor: ChatGPT (OpenAI)
-- Data: 26/07/2025

local Players   = game:GetService("Players")
local RunSvc   = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player   = Players.LocalPlayer
local gui      = Instance.new("ScreenGui")
gui.Name      = "FruitTPGui"
gui.Parent    = player:WaitForChild("PlayerGui")

local frame    = Instance.new("Frame")
frame.Size     = UDim2.new(0, 200, 0, 120)
frame.Position = UDim2.new(0, 20, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Parent   = gui

local title    = Instance.new("TextLabel")
title.Size     = UDim2.new(1, 0, 0, 24)
title.BackgroundTransparency = 1
title.Text     = "Fruit TP"
title.TextColor3 = Color3.new(1,1,1)
title.Font     = Enum.Font.SourceSansBold
title.TextSize = 20
title.Parent   = frame

local toggleBtn = Instance.new("TextButton")
toggleBtn.Size     = UDim2.new(1, -10, 0, 30)
toggleBtn.Position = UDim2.new(0, 5, 0, 30)
toggleBtn.Text     = "Ativar TP"
toggleBtn.TextColor3 = Color3.new(1,1,1)
toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 100, 200)
toggleBtn.Font     = Enum.Font.SourceSans
toggleBtn.TextSize = 18
toggleBtn.Parent   = frame

local statusLbl = Instance.new("TextLabel")
statusLbl.Size     = UDim2.new(1, -10, 0, 20)
statusLbl.Position = UDim2.new(0, 5, 0, 65)
statusLbl.Text     = "Status: OFF"
statusLbl.TextColor3 = Color3.new(1,1,1)
statusLbl.BackgroundTransparency = 1
statusLbl.Font     = Enum.Font.SourceSans
statusLbl.TextSize = 16
statusLbl.Parent   = frame

-- Controle
local active = false
local lastFruitPos = {}

toggleBtn.MouseButton1Click:Connect(function()
    active = not active
    toggleBtn.Text = active and "Desativar TP" or "Ativar TP"
    statusLbl.Text = "Status: " .. (active and "ON" or "OFF")
end)

-- Função para buscar frutas no workspace (ajuste conforme hierarquia específica)
local function getFruitInstances()
    local fruits = {}
    for _, obj in ipairs(game.Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name:match("Fruit") then
            table.insert(fruits, obj)
        end
    end
    return fruits
end

-- Teleport simples do personagem
local function teleportTo(pos)
    local char = player.Character or player.CharacterAdded:Wait()
    local hum = char:FindFirstChild("HumanoidRootPart")
    if hum then
        hum.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
    end
end

-- Loop principal
RunSvc.RenderStepped:Connect(function()
    if not active then return end

    local fruits = getFruitInstances()
    for _, fruit in ipairs(fruits) do
        if fruit and fruit:IsDescendantOf(game.Workspace) then
            local id = fruit:GetDebugId() or fruit:GetFullName()
            if not lastFruitPos[id] then
                lastFruitPos[id] = true
                teleportTo(fruit.Position)
                wait(1) -- tempo para evitar muitos teleports
            end
        end
    end
end)
