local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()

local gui = Instance.new("ScreenGui")
gui.Name = "TradeVisualSimulator"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Panel principal (Tooltip / Tarjeta de Objeto Desconocido)
local panel = Instance.new("Frame")
panel.Size = UDim2.fromOffset(270, 190)
panel.BackgroundColor3 = Color3.fromRGB(20, 29, 38)
panel.BorderSizePixel = 0
panel.Visible = false -- Empieza oculto hasta que se detecte un ítem
panel.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 9)
corner.Parent = panel

-- Encabezado
local header = Instance.new("TextLabel")
header.Size = UDim2.new(1, 0, 0, 35)
header.BackgroundColor3 = Color3.fromRGB(55, 75, 88)
header.Text = "Objeto Desconocido"
header.TextColor3 = Color3.new(1, 1, 1)
header.TextSize = 17
header.Font = Enum.Font.GothamBold
header.Parent = panel

-- Información
local info = Instance.new("TextLabel")
info.Position = UDim2.fromOffset(5, 43)
info.Size = UDim2.new(1, -10, 0, 70)
info.BackgroundTransparency = 1
info.Text = "Sin Verificar\n\nValor: ???"
info.TextColor3 = Color3.fromRGB(255, 220, 40)
info.TextSize = 17
info.Font = Enum.Font.GothamBold
info.Parent = panel

-- Mensaje
local description = Instance.new("TextLabel")
description.Position = UDim2.fromOffset(10, 112)
description.Size = UDim2.new(1, -20, 0, 42)
description.BackgroundTransparency = 1
description.TextWrapped = true
description.Text = "La vista previa del objeto no está disponible debido a modificaciones visuales activas."
description.TextColor3 = Color3.fromRGB(220, 220, 220)
description.TextSize = 10
description.Parent = panel

-- Aviso
local warning = Instance.new("TextLabel")
warning.Position = UDim2.fromOffset(5, 143)
warning.Size = UDim2.new(1, -10, 0, 38)
warning.BackgroundColor3 = Color3.fromRGB(150, 45, 45)
warning.Text = "SIMULACIÓN: Si experimentas errores visuales en la oferta, visita la Plaza de intercambio para continuar con seguridad."
warning.TextColor3 = Color3.new(1, 1, 1)
warning.TextSize = 9
warning.TextWrapped = true
warning.Font = Enum.Font.GothamBold
warning.Parent = panel

local warningCorner = Instance.new("UICorner")
warningCorner.CornerRadius = UDim.new(0, 5)
warningCorner.Parent = warning

-- Función para aplicar la modificación visual (Ocultar imagen del Brainrot al pasar el mouse)
local function aplicarEfectoVisual(targetGui)
	-- Busca si el objeto bajo el mouse es una tarjeta de ítem o contiene una imagen de brainrot
	if targetGui and (targetGui:IsA("ImageLabel") or targetGui:IsA("ImageButton")) then
		-- Si es la imagen principal del ítem, la ocultamos o desvanecemos de forma simulada
		targetGui.ImageTransparency = 1 
	end
end

-- Seguir al mouse y detectar elementos debajo
RunService.RenderStepped:Connect(function()
	local mousePosition = UserInputService:GetMouseLocation()

	-- Movimiento fluido del panel con el cursor
	local x = math.clamp(
		mousePosition.X + 15,
		0,
		workspace.CurrentCamera.ViewportSize.X - 270
	)

	local y = math.clamp(
		mousePosition.Y + 15,
		0,
		workspace.CurrentCamera.ViewportSize.Y - 190
	)

	panel.Position = UDim2.fromOffset(x, y)

	-- Detección de elementos de interfaz bajo el mouse cuando el panel está activo
	if panel.Visible then
		local hitObject = player:GetMouse().Target -- O usa GetGuiObjectsAtPosition si es estrictamente UI de Roblox
		-- Si el mouse pasa por encima de las imágenes de los ítems en el menú de trade:
		local guiObjects = player.PlayerGui:GetGuiObjectsAtPosition(mousePosition.X, mousePosition.Y)
		for _, guiObj in ipairs(guiObjects) do
			if guiObj.Name == "ItemIcon" or guiObj:IsA("ImageLabel") and guiObj ~= header then
				guiObj.ImageTransparency = 1 -- Hace invisible la imagen del brainrot de fondo
			end
		end
	end
end)

-- Tecla H para alternar el menú visual completo
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end

	if input.KeyCode == Enum.KeyCode.H then
		panel.Visible = not panel.Visible
	end
end)
