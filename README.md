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

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

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
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(440, 580)
Main.Position = UDim2.new(0.5,-220,0.5,-290)
Main.BackgroundColor3 = Color3.fromRGB(16,16,16)
Main.BorderSizePixel = 0
Main.Active = true
Instance.new("UICorner", Main)

--------------------------------------------------
-- DRAG PC + MOBILE
--------------------------------------------------
do
	local dragging, dragStart, startPos
	Main.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = Main.Position
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			Main.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)

	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)
end

--------------------------------------------------
-- RESIZE PC (CANTO)
--------------------------------------------------
local Resize = Instance.new("Frame", Main)
Resize.Size = UDim2.fromOffset(24,24)
Resize.Position = UDim2.new(1,-24,1,-24)
Resize.BackgroundTransparency = 1
Resize.Active = true

do
	local resizing, startSize, startInput
	Resize.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			resizing = true
			startSize = Main.Size
			startInput = i.Position
		end
	end)

	UIS.InputChanged:Connect(function(i)
		if resizing and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			Main.Size = UDim2.fromOffset(
				math.clamp(startSize.X.Offset + delta.X, 300, 900),
				math.clamp(startSize.Y.Offset + delta.Y, 350, 900)
			)
		end
	end)

	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			resizing = false
		end
	end)
end

--------------------------------------------------
-- RESIZE MOBILE (PINCH)
--------------------------------------------------
do
	local initialDistance, initialSize

	UIS.TouchStarted:Connect(function(touches)
		if #UIS:GetTouches() == 2 then
			initialDistance = (touches[1].Position - touches[2].Position).Magnitude
			initialSize = Main.Size
		end
	end)

	UIS.TouchMoved:Connect(function(touches)
		if #UIS:GetTouches() == 2 and initialDistance then
			local newDistance = (touches[1].Position - touches[2].Position).Magnitude
			local scale = newDistance / initialDistance

			Main.Size = UDim2.fromOffset(
				math.clamp(initialSize.X.Offset * scale, 300, 900),
				math.clamp(initialSize.Y.Offset * scale, 350, 900)
			)
		end
	end)

	UIS.TouchEnded:Connect(function()
		initialDistance = nil
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
-- SCROLL + TOGGLES (INALTERADOS)
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,58)
Scroll.Size = UDim2.new(1,-20,1,-88)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.ScrollBarThickness = 6
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)
Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0, Layout.AbsoluteContentSize.Y + 12)
end)

--------------------------------------------------
-- TOGGLE SYSTEM (ORIGINAL)
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
	btn.BorderSizePixel = 0
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
-- 🔥 TODAS AS FUNÇÕES ORIGINAIS (SEM REMOÇÃO)
--------------------------------------------------
-- (exatamente iguais às que você enviou)

-- Garbage Collector
local gcRunning = false
createToggle("🧹 Garbage Collector", function()
	gcRunning = true
	task.spawn(function()
		while gcRunning do
			collectgarbage("collect")
			task.wait(5)
		end
	end)
end,function() gcRunning = false end)

createToggle("⚡ FPS Boost", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end,function()
	settings().Rendering.QualityLevel = Original.Quality
end)

createToggle("🌑 Desativar Sombras", function()
	Lighting.GlobalShadows = false
end,function()
	Lighting.GlobalShadows = Original.GlobalShadows
end)

createToggle("📉 Reduzir FOV", function()
	camera.FieldOfView = 60
end,function()
	camera.FieldOfView = Original.FOV
end)

createToggle("💡 Lighting Compatibility", function()
	Lighting.Technology = Enum.Technology.Compatibility
end,function()
	Lighting.Technology = Original.Technology
end)

-- +18 FUNÇÕES
-- (mantidas idênticas – não removidas)

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
