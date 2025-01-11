local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local teleportLocations = {
    ["Spawn"] = Vector3.new(1054, 16, 1426), -- Coordenadas do spawn
    ["Jungle"] = Vector3.new(-1249, 11, 340), -- Coordenadas da Jungle
    ["Pirate Village"] = Vector3.new(-1122, 4, 3855), -- Coordenadas do Pirate Village
    ["Marine Fortress"] = Vector3.new(-4998, 39, 4300) -- Coordenadas do Marine Fortress
}

local autoFarmEnabled = false

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.Name = "TeleportGui"

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 350)
frame.Position = UDim2.new(0.05, 0, 0.5, -175)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.BackgroundTransparency = 0.3
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "Teleporte Rápido"
title.TextScaled = true
title.Parent = frame

local function createButton(text, position, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.8, 0, 0, 40)
    button.Position = position
    button.Text = text
    button.TextScaled = true
    button.BackgroundColor3 = Color3.fromRGB(100, 100, 255)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Parent = frame
    button.MouseButton1Click:Connect(callback)
end

local yOffset = 40
for locationName, coordinates in pairs(teleportLocations) do
    createButton(locationName, UDim2.new(0.1, 0, 0, yOffset), function()
        humanoidRootPart.CFrame = CFrame.new(coordinates)
        print("Teleportado para:", locationName)
    end)
    yOffset = yOffset + 50
end

createButton("Auto Farm", UDim2.new(0.1, 0, 0, yOffset), function()
    autoFarmEnabled = not autoFarmEnabled
    if autoFarmEnabled then
        print("Auto Farm Ativado")
        while autoFarmEnabled do
            task.wait(0.1)

            local closestEnemy = nil
            local closestDistance = math.huge

            for _, npc in pairs(workspace:GetChildren()) do
                if npc:IsA("Model") and npc:FindFirstChild("HumanoidRootPart") and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
                    local distance = (npc.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
                    if distance < closestDistance then
                        closestDistance = distance
                        closestEnemy = npc
                    end
                end
            end

            if closestEnemy then
                humanoidRootPart.CFrame = CFrame.new(closestEnemy.HumanoidRootPart.Position) * CFrame.new(0, 5, 0)
                game:GetService("VirtualUser"):CaptureController()
                game:GetService("VirtualUser"):Button1Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
            end
        end
    else
        print("Auto Farm Desativado")
    end
end)
