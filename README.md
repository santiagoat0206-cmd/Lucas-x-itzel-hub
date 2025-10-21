
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui", 5)
if not playerGui then
    warn("Error: PlayerGui no encontrado")
    return
end

-- Mensaje de depuración
print("Lucas x Itzel Hub: Iniciando para " .. player.Name)

-- Crear ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "LucasXItzelHub"
gui.ResetOnSpawn = false -- Evita que el GUI se reinicie al respawnear
gui.Parent = playerGui

-- Crear marco principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(0.5, -150, 0.5, -100)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.BorderSizePixel = 2
frame.Visible = true -- Asegurar visibilidad
frame.Parent = gui

-- Título del Hub
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "Lucas x Itzel Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.TextScaled = true
title.Parent = frame

-- Campo de texto para la clave
local keyInput = Instance.new("TextBox")
keyInput.Size = UDim2.new(0.8, 0, 0, 30)
keyInput.Position = UDim2.new(0.1, 0, 0.3, 0)
keyInput.Text = "Ingresa la clave"
keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
keyInput.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
keyInput.TextScaled = true
keyInput.Parent = frame

-- Botón para verificar la clave
local verifyButton = Instance.new("TextButton")
verifyButton.Size = UDim2.new(0.8, 0, 0, 30)
verifyButton.Position = UDim2.new(0.1, 0, 0.5, 0)
verifyButton.Text = "Verificar Clave"
verifyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
verifyButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
verifyButton.TextScaled = true
verifyButton.Parent = frame

-- Botones de funciones (inicialmente ocultos)
local saveButton = Instance.new("TextButton")
saveButton.Size = UDim2.new(0.4, 0, 0, 30)
saveButton.Position = UDim2.new(0.1, 0, 0.7, 0)
saveButton.Text = "Save TP"
saveButton.TextColor3 = Color3.fromRGB(255, 255, 255)
saveButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
saveButton.TextScaled = true
saveButton.Visible = false
saveButton.Parent = frame

local tpButton = Instance.new("TextButton")
tpButton.Size = UDim2.new(0.4, 0, 0, 30)
tpButton.Position = UDim2.new(0.5, 0, 0.7, 0)
tpButton.Text = "TP"
tpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
tpButton.BackgroundColor3 = Color3.fromRGB(0, 0, 200)
tpButton.TextScaled = true
tpButton.Visible = false
tpButton.Parent = frame

-- Botón para cerrar el GUI
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -40, 0, 10)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
closeButton.TextScaled = true
closeButton.Parent = frame

-- Almacenar posición guardada
local savedPosition = nil

-- Función para verificar la clave
verifyButton.MouseButton1Click:Connect(function()
    if keyInput.Text == "hechoporsanjaja" then
        keyInput.Text = "¡Clave Correcta!"
        keyInput.TextColor3 = Color3.fromRGB(0, 255, 0)
        verifyButton.Visible = false
        keyInput.Visible = false
        saveButton.Visible = true
        tpButton.Visible = true
        print("Clave verificada correctamente")
    else
        keyInput.Text = "Clave Incorrecta"
        keyInput.TextColor3 = Color3.fromRGB(255, 0, 0)
        print("Clave incorrecta ingresada")
    end
end)

-- Función para cerrar el GUI
closeButton.MouseButton1Click:Connect(function()
    gui.Enabled = false
    print("GUI cerrado")
end)

-- Función para guardar la posición
saveButton.MouseButton1Click:Connect(function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        savedPosition = player.Character.HumanoidRootPart.Position
        print("Posición guardada: " .. tostring(savedPosition))
    else
        warn("Error: No se encontró HumanoidRootPart")
    end
end)

-- Función para teletransportarse
tpButton.MouseButton1Click:Connect(function()
    if savedPosition and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid and humanoid.SeatPart then
            -- Si está en un vehículo, mover el vehículo
            local vehicle = humanoid.SeatPart:FindFirstAncestorOfClass("Model")
            if vehicle and vehicle:IsA("Model") then
                local primaryPart = vehicle.PrimaryPart or vehicle:FindFirstChildWhichIsA("BasePart")
                if primaryPart then
                    vehicle:SetPrimaryPartCFrame(CFrame.new(savedPosition))
                    print("Vehículo teletransportado a: " .. tostring(savedPosition))
                else
                    warn("Error: Vehículo sin PrimaryPart")
                end
            end
        else
            -- Si no está en un vehículo, mover al personaje
            player.Character.HumanoidRootPart.CFrame = CFrame.new(savedPosition)
            print("Personaje teletransportado a: " .. tostring(savedPosition))
        end
    else
        warn("Error: No se puede teletransportar, falta posición o HumanoidRootPart")
    end
end)

-- Hacer el marco arrastrable
local dragging
local dragInput
local dragStart
local startPos

frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
    end
end)

frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

frame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Mensaje final de depuración
print("Lucas x Itzel Hub: GUI creado exitosamente")-- Verificar que el entorno es correcto
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui", 5)
if not playerGui then
    warn("Error: PlayerGui no encontrado")
    return
end

-- Mensaje de depuración
print("Lucas x Itzel Hub: Iniciando para " .. player.Name)

-- Crear ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "LucasXItzelHub"
gui.ResetOnSpawn = false -- Evita que el GUI se reinicie al respawnear
gui.Parent = playerGui

-- Crear marco principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(0.5, -150, 0.5, -100)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.BorderSizePixel = 2
frame.Visible = true -- Asegurar visibilidad
frame.Parent = gui

-- Título del Hub
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "Lucas x Itzel Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.TextScaled = true
title.Parent = frame

-- Campo de texto para la clave
local keyInput = Instance.new("TextBox")
keyInput.Size = UDim2.new(0.8, 0, 0, 30)
keyInput.Position = UDim2.new(0.1, 0, 0.3, 0)
keyInput.Text = "Ingresa la clave"
keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
keyInput.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
keyInput.TextScaled = true
keyInput.Parent = frame

-- Botón para verificar la clave
local verifyButton = Instance.new("TextButton")
verifyButton.Size = UDim2.new(0.8, 0, 0, 30)
verifyButton.Position = UDim2.new(0.1, 0, 0.5, 0)
verifyButton.Text = "Verificar Clave"
verifyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
verifyButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
verifyButton.TextScaled = true
verifyButton.Parent = frame

-- Botones de funciones (inicialmente ocultos)
local saveButton = Instance.new("TextButton")
saveButton.Size = UDim2.new(0.4, 0, 0, 30)
saveButton.Position = UDim2.new(0.1, 0, 0.7, 0)
saveButton.Text = "Save TP"
saveButton.TextColor3 = Color3.fromRGB(255, 255, 255)
saveButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
saveButton.TextScaled = true
saveButton.Visible = false
saveButton.Parent = frame

local tpButton = Instance.new("TextButton")
tpButton.Size = UDim2.new(0.4, 0, 0, 30)
tpButton.Position = UDim2.new(0.5, 0, 0.7, 0)
tpButton.Text = "TP"
tpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
tpButton.BackgroundColor3 = Color3.fromRGB(0, 0, 200)
tpButton.TextScaled = true
tpButton.Visible = false
tpButton.Parent = frame

-- Botón para cerrar el GUI
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -40, 0, 10)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
closeButton.TextScaled = true
closeButton.Parent = frame

-- Almacenar posición guardada
local savedPosition = nil

-- Función para verificar la clave
verifyButton.MouseButton1Click:Connect(function()
    if keyInput.Text == "hechoporsanjaja" then
        keyInput.Text = "¡Clave Correcta!"
        keyInput.TextColor3 = Color3.fromRGB(0, 255, 0)
        verifyButton.Visible = false
        keyInput.Visible = false
        saveButton.Visible = true
        tpButton.Visible = true
        print("Clave verificada correctamente")
    else
        keyInput.Text = "Clave Incorrecta"
        keyInput.TextColor3 = Color3.fromRGB(255, 0, 0)
        print("Clave incorrecta ingresada")
    end
end)

-- Función para cerrar el GUI
closeButton.MouseButton1Click:Connect(function()
    gui.Enabled = false
    print("GUI cerrado")
end)

-- Función para guardar la posición
saveButton.MouseButton1Click:Connect(function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        savedPosition = player.Character.HumanoidRootPart.Position
        print("Posición guardada: " .. tostring(savedPosition))
    else
        warn("Error: No se encontró HumanoidRootPart")
    end
end)

-- Función para teletransportarse
tpButton.MouseButton1Click:Connect(function()
    if savedPosition and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid and humanoid.SeatPart then
            -- Si está en un vehículo, mover el vehículo
            local vehicle = humanoid.SeatPart:FindFirstAncestorOfClass("Model")
            if vehicle and vehicle:IsA("Model") then
                local primaryPart = vehicle.PrimaryPart or vehicle:FindFirstChildWhichIsA("BasePart")
                if primaryPart then
                    vehicle:SetPrimaryPartCFrame(CFrame.new(savedPosition))
                    print("Vehículo teletransportado a: " .. tostring(savedPosition))
                else
                    warn("Error: Vehículo sin PrimaryPart")
                end
            end
        else
            -- Si no está en un vehículo, mover al personaje
            player.Character.HumanoidRootPart.CFrame = CFrame.new(savedPosition)
            print("Personaje teletransportado a: " .. tostring(savedPosition))
        end
    else
        warn("Error: No se puede teletransportar, falta posición o HumanoidRootPart")
    end
end)

-- Hacer el marco arrastrable
local dragging
local dragInput
local dragStart
local startPos

frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
    end
end)

frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

frame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Mensaje final de depuración
print("Lucas x Itzel Hub: GUI creado exitosamente")
