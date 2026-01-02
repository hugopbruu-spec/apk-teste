--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.7 FINAL FIX
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

--------------------------------------------------
-- DRAG SYSTEM
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local drag, startPos, startInput
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			drag = true
			startPos = frame.Position
			startInput = i.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if drag and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			drag = false
		end
	end)
end

--------------------------------------------------
-- BACKUP
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Brightness = Lighting.Brightness,
	Technology = Lighting.Technology,
	FOV = camera.FieldOfView
}

--------------------------------------------------
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(420, 560)
Main.Position = UDim2.new(0.5, -210, 0.5, -280)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Instance.new("UICorner", Main)
makeDraggable(Main)

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,46)
Header.BackgroundColor3 = Color3.fromRGB(28,28,28)
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-100,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO v1.7"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(220,40,40)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Left

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32,32)
Minimize.Position = UDim2.new(1,-72,0,7)
Minimize.Text = "–"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 22
Minimize.BackgroundTransparency = 1
Minimize.TextColor3 = Color3.new(1,1,1)

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32,32)
Close.Position = UDim2.new(1,-36,0,7)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.BackgroundTransparency = 1
Close.TextColor3 = Color3.fromRGB(220,40,40)

--------------------------------------------------
-- SCROLL (FIX DEFINITIVO)
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,56)
Scroll.Size = UDim2.new(1,-20,1,-80)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.ScrollBarThickness = 6
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.None
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0, Layout.AbsoluteContentSize.Y + 10)
end)

--------------------------------------------------
-- TOGGLE SYSTEM
--------------------------------------------------
local function createToggle(name, on, off)
	local state = false
	local btn = Instance.new("TextButton", Scroll)
	btn.Size = UDim2.new(1,0,0,38)
	btn.Text = name .. " [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = Color3.fromRGB(32,32,32)
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		state = not state
		if state then
			pcall(on)
			btn.Text = name .. " [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
		else
			if off then pcall(off) end
			btn.Text = name .. " [OFF]"
			btn.BackgroundColor3 = Color3.fromRGB(32,32,32)
		end
	end)
end

--------------------------------------------------
-- GARBAGE COLLECTOR FIX
--------------------------------------------------
local gc = false
createToggle("Garbage Collector", function()
	gc = true
	task.spawn(function()
		while gc do
			collectgarbage("collect")
			task.wait(4)
		end
	end)
end, function()
	gc = false
end)

--------------------------------------------------
-- FUNÇÕES (32)
--------------------------------------------------

createToggle("FPS Boost", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end, function()
	settings().Rendering.QualityLevel = Original.Quality
end)

createToggle("Desativar Sombras", function()
	Lighting.GlobalShadows = false
end, function()
	Lighting.GlobalShadows = Original.GlobalShadows
end)

createToggle("Reduzir FOV", function()
	camera.FieldOfView = 60
end, function()
	camera.FieldOfView = Original.FOV
end)

createToggle("Lighting Compatibility", function()
	Lighting.Technology = Enum.Technology.Compatibility
end, function()
	Lighting.Technology = Original.Technology
end)

createToggle("Reduzir Brightness", function()
	Lighting.Brightness = 1
end, function()
	Lighting.Brightness = Original.Brightness
end)

createToggle("Desativar Pós-Processamento", function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled = false end
	end
end)

createToggle("Desativar Atmosphere", function()
	local a = Lighting:FindFirstChildOfClass("Atmosphere")
	if a then a.Enabled = false end
end)

createToggle("Desativar Skybox", function()
	local s = Lighting:FindFirstChildOfClass("Sky")
	if s then s.Parent = nil end
end)

createToggle("Desativar Partículas", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then v.Enabled = false end
	end
end)

createToggle("Desativar Trails", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then v.Enabled = false end
	end
end)

createToggle("Desativar Smoke", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Smoke") then v.Enabled = false end
	end
end)

createToggle("Desativar Fire", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Fire") then v.Enabled = false end
	end
end)

createToggle("Materiais Leves", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Material = Enum.Material.Plastic
			v.CastShadow = false
		end
	end
end)

createToggle("Remover Decals", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Decal") or v:IsA("Texture") then
			v.Transparency = 1
		end
	end
end)

createToggle("Simplificar MeshParts", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("MeshPart") then
			v.RenderFidelity = Enum.RenderFidelity.Performance
		end
	end
end)

createToggle("Ultra Performance", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	Lighting.GlobalShadows = false
	Lighting.Brightness = 1
end)

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
