local player = game.Players.LocalPlayer
local playerGui = player.PlayerGui

-- Criar a tela e os botões
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = playerGui

local autoFarmButton = Instance.new("TextButton")
autoFarmButton.Size = UDim2.new(0, 150, 0, 50)
autoFarmButton.Position = UDim2.new(1, -160, 0, 50)
autoFarmButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
autoFarmButton.Text = "AutoFarm"
autoFarmButton.TextSize = 20
autoFarmButton.Parent = screenGui

local stopButton = Instance.new("TextButton")
stopButton.Size = UDim2.new(0, 150, 0, 50)
stopButton.Position = UDim2.new(1, -160, 0, 110)
stopButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
stopButton.Text = "Stop"
stopButton.TextSize = 20
stopButton.Parent = screenGui

-- Variáveis de controle
local isAutoFarming = false
local teleporting = false

-- Função para pegar o item mais próximo
local function getClosestItem()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
    local debrisFolder = workspace:FindFirstChild("Debris")

    if not debrisFolder then return nil end

    local closestItem = nil
    local minDistance = math.huge

    for _, item in pairs(debrisFolder:GetChildren()) do
        if item:IsA("MeshPart") then
            local distance = (humanoidRootPart.Position - item.Position).Magnitude
            if distance < minDistance then
                minDistance = distance
                closestItem = item
            end
        end
    end

    return closestItem
end

-- Função para pressionar a tecla "E"
local function pressKeyE()
    local VirtualInputManager = game:GetService("VirtualInputManager")
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
    wait(2)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
end

-- Função que será chamada quando o botão AutoFarm for pressionado
autoFarmButton.MouseButton1Click:Connect(function()
    isAutoFarming = true
    teleporting = true

    while isAutoFarming do
        local item = getClosestItem()
        if item then
            local humanoidRootPart = player.Character:WaitForChild("HumanoidRootPart")
            humanoidRootPart.CFrame = item.CFrame
            wait(0.5)

            -- Enquanto o item estiver na cena, pressiona a tecla "E"
            while item.Parent ~= nil and isAutoFarming do
                pressKeyE()
                wait(1)
            end
        end
        wait(2)
    end
end)

-- Função que será chamada quando o botão Stop for pressionado
stopButton.MouseButton1Click:Connect(function()
    isAutoFarming = false
    teleporting = false
end)
