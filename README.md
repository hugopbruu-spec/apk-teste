--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.5.1 FIXED
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- DRAG FUNCTION
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local dragging, dragInput, startPos, startInput

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = frame.Position
			startInput = input.Position
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = input.Position - startInput
			frame.Position = UDim2.new(
				startPos.X.Scale,
				startPos.X.Offset + delta.X,
				startPos.Y.Scale,
				startPos.Y.Offset + delta.Y
			)
		end
	end)

	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

--------------------------------------------------
-- ORIGINAL SETTINGS BACKUP
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Brightness = Lighting.Brightness,
	Parts = {},
	Decals = {},
	Particles = {}
}

for _,v in ipairs(workspace:GetDescendants()) do
	if v:IsA("BasePart") then
		Original.Parts[v] = {
			Material = v.Material,
			CastShadow = v.CastShadow,
			Reflectance = v.Reflectance
		}
	elseif v:IsA("Decal") or v:IsA("Texture") then
		Original.Decals[v] = v.Transparency
	elseif v:IsA("ParticleEmitter") then
		Original.Particles[v] = v.Enabled
	end
end

--------------------------------------------------
-- THEME
--------------------------------------------------
local RED = Color3.fromRGB(220,40,40)
local DARK = Color3.fromRGB(18,18,18)
local DARK2 = Color3.fromRGB(30,30,30)

--------------------------------------------------
-- GUI
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(380,540)
Main.Position = UDim2.new(0.5,-190,0.5,-270)
Main.BackgroundColor3 = DARK
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)
makeDraggable(Main)

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,46)
Header.BackgroundColor3 = DARK2
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-90,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = RED
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32,32)
Minimize.Position = UDim2.new(1,-72,0,7)
Minimize.Text = "–"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 22
Minimize.TextColor3 = Color3.new(1,1,1)
Minimize.BackgroundTransparency = 1

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32,32)
Close.Position = UDim2.new(1,-36,0,7)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = RED
Close.BackgroundTransparency = 1

--------------------------------------------------
-- SCROLL (FIXED)
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,56)
Scroll.Size = UDim2.new(1,-20,1,-90)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.ScrollBarImageTransparency = 0.4
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0,Layout.AbsoluteContentSize.Y + 10)
end)

--------------------------------------------------
-- TOGGLE CREATOR (FIXED)
--------------------------------------------------
local function createToggle(text, on, off)
	local enabled = false

	local btn = Instance.new("TextButton")
	btn.Parent = Scroll
	btn.Size = UDim2.new(1,0,0,38)
	btn.Text = text .. " [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = DARK2
	btn.AutoButtonColor = false
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		enabled = not enabled
		if enabled then
			on()
			btn.Text = text .. " [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
		else
			if off then off() end
			btn.Text = text .. " [OFF]"
			btn.BackgroundColor3 = DARK2
		end
	end)
end

--------------------------------------------------
-- FUNCTIONS (18 FUNCIONANDO)
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

createToggle("Materiais Leves", function()
	for p,_ in pairs(Original.Parts) do
		p.Material = Enum.Material.Plastic
		p.CastShadow = false
	end
end, function()
	for p,d in pairs(Original.Parts) do
		p.Material = d.Material
		p.CastShadow = d.CastShadow
	end
end)

createToggle("Remover Decals", function()
	for d,_ in pairs(Original.Decals) do
		d.Transparency = 1
	end
end, function()
	for d,v in pairs(Original.Decals) do
		d.Transparency = v
	end
end)

createToggle("Remover Partículas", function()
	for p,_ in pairs(Original.Particles) do
		p.Enabled = false
	end
end, function()
	for p,v in pairs(Original.Particles) do
		p.Enabled = v
	end
end)

createToggle("Reduzir Água", function()
	if Terrain then
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterTransparency = 1
	end
end, function()
end)

createToggle("Reduzir Reflectance", function()
	for p,_ in pairs(Original.Parts) do
		p.Reflectance = 0
	end
end, function()
	for p,d in pairs(Original.Parts) do
		p.Reflectance = d.Reflectance
	end
end)

createToggle("Garbage Collector", function()
	collectgarbage("collect")
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

--------------------------------------------------
-- CREDIT
--------------------------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,18)
Credit.Position = UDim2.new(0,0,1,-18)
Credit.Text = "Criador: Frostzn | FPS Optimizer PRO"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 11
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1
