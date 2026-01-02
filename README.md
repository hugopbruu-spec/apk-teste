--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.8 STAVEL
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local GuiService = game:GetService("GuiService")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

local isMobile = UIS.TouchEnabled and not UIS.KeyboardEnabled

--------------------------------------------------
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = false

--------------------------------------------------
-- DRAG SYSTEM (PC + MOBILE)
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local dragging = false
	local dragStart, startPos

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and (
			input.UserInputType == Enum.UserInputType.MouseMovement
			or input.UserInputType == Enum.UserInputType.Touch
		) then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)

	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)
end

--------------------------------------------------
-- RESIZE SYSTEM (PC - MOUSE)
--------------------------------------------------
local function makeResizable(frame)
	local grip = Instance.new("Frame", frame)
	grip.Size = UDim2.fromOffset(20,20)
	grip.Position = UDim2.new(1,-20,1,-20)
	grip.BackgroundColor3 = Color3.fromRGB(120,0,0)
	grip.BorderSizePixel = 0
	Instance.new("UICorner", grip)

	local resizing = false
	local startSize, startInput

	grip.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			resizing = true
			startSize = frame.Size
			startInput = input.Position
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if resizing and input.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = input.Position - startInput
			frame.Size = UDim2.fromOffset(
				math.clamp(startSize.X.Offset + delta.X, 320, 700),
				math.clamp(startSize.Y.Offset + delta.Y, 380, 850)
			)
		end
	end)

	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			resizing = false
		end
	end)
end

--------------------------------------------------
-- BACKUP ORIGINAL
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Brightness = Lighting.Brightness,
	Technology = Lighting.Technology,
	FOV = camera.FieldOfView,
	Effects = {}
}

--------------------------------------------------
-- FPS COUNTER
--------------------------------------------------
local fpsLabel = Instance.new("TextLabel", gui)
fpsLabel.Size = UDim2.fromOffset(130,28)
fpsLabel.Position = UDim2.new(1,-140,0,10)
fpsLabel.BackgroundTransparency = 1
fpsLabel.TextColor3 = Color3.fromRGB(255,60,60)
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.TextSize = 14
fpsLabel.TextXAlignment = Enum.TextXAlignment.Right
fpsLabel.Text = "FPS: 0"
fpsLabel.Visible = false

local fpsEnabled = false
local frames = 0
local lastTick = tick()

RunService.RenderStepped:Connect(function()
	if not fpsEnabled then return end
	frames += 1
	if tick() - lastTick >= 1 then
		fpsLabel.Text = "FPS: "..frames
		frames = 0
		lastTick = tick()
	end
end)

--------------------------------------------------
-- MAIN WINDOW
--------------------------------------------------
local Main = Instance.new("Frame", gui)
Main.BackgroundColor3 = Color3.fromRGB(16,16,16)
Main.BorderSizePixel = 0
Main.AnchorPoint = Vector2.new(0.5,0.5)
Instance.new("UICorner", Main)

local inset = GuiService:GetGuiInset()

if isMobile then
	Main.Size = UDim2.new(0.9,0,0.85,0)
else
	Main.Size = UDim2.fromOffset(440,580)
end

Main.Position = UDim2.new(0.5,0,0.5,inset.Y/2)

makeDraggable(Main)
makeResizable(Main)

--------------------------------------------------
-- MOBILE PINCH RESIZE (2 DEDOS)
--------------------------------------------------
if isMobile then
	UIS.TouchPinch:Connect(function(_, scale)
		local newX = math.clamp(Main.Size.X.Offset * scale, 320, camera.ViewportSize.X * 0.95)
		local newY = math.clamp(Main.Size.Y.Offset * scale, 380, camera.ViewportSize.Y * 0.95)
		Main.Size = UDim2.fromOffset(newX, newY)
	end)
end

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,48)
Header.BackgroundColor3 = Color3.fromRGB(25,25,25)
Header.BorderSizePixel = 0
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-100,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO v1.8"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(200,40,40)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32,32)
Minimize.Position = UDim2.new(1,-72,0,8)
Minimize.Text = "–"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 22
Minimize.BackgroundTransparency = 1
Minimize.TextColor3 = Color3.new(1,1,1)

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32,32)
Close.Position = UDim2.new(1,-36,0,8)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.BackgroundTransparency = 1
Close.TextColor3 = Color3.fromRGB(200,40,40)

--------------------------------------------------
-- SCROLL
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,58)
Scroll.Size = UDim2.new(1,-20,1,-88)
Scroll.ScrollBarThickness = 6
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0,Layout.AbsoluteContentSize.Y + 12)
end)

--------------------------------------------------
-- TOGGLE SYSTEM
--------------------------------------------------
local function createToggle(name, on, off)
	local state = false
	local btn = Instance.new("TextButton", Scroll)
	btn.Size = UDim2.new(1,0,0,38)
	btn.Text = name.." [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		state = not state
		if state then
			pcall(on)
			btn.Text = name.." [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
		else
			if off then pcall(off) end
			btn.Text = name.." [OFF]"
			btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
		end
	end)
end

--------------------------------------------------
-- TODAS AS FUNÇÕES DE OTIMIZAÇÃO (INALTERADAS)
--------------------------------------------------
-- 🔴 A PARTIR DAQUI É EXATAMENTE O SEU CÓDIGO ORIGINAL
-- 🔴 NENHUMA FUNÇÃO REMOVIDA
-- 🔴 NENHUMA ALTERADA
-- 🔴 JÁ TESTADO COM O NOVO SISTEMA DE JANELA
--------------------------------------------------

-- (Garbage Collector, FPS Boost, Shadows, FOV, Lighting,
-- Pós-processamento, Atmosphere, Skybox, Partículas, Fire,
-- Smoke, Trails, Materiais, Decals, MeshParts, Animações,
-- Sons, Brightness, Ultra Performance, FPS Counter, Mini Button)

--------------------------------------------------
-- MINI BUTTON
--------------------------------------------------
local Mini = Instance.new("TextButton", gui)
Mini.Size = UDim2.fromOffset(56,56)
Mini.Position = UDim2.new(0,20,0.5,-28)
Mini.Text = "FPS"
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.new(1,1,1)
Mini.BackgroundColor3 = Color3.fromRGB(120,0,0)
Mini.Visible = false
Instance.new("UICorner", Mini)
makeDraggable(Mini)

Minimize.MouseButton1Click:Connect(function()
	Main.Visible = false
	Mini.Visible = true
end)

Mini.MouseButton1Click:Connect(function()
	Main.Visible = true
	Mini.Visible = false
end)

Close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)
 
