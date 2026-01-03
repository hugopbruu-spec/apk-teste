--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.9.3
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
-- DRAG SYSTEM
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local dragging, dragStart, startPos

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
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
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

--------------------------------------------------
-- FPS COUNTER (NOVO)
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
-- MAIN
--------------------------------------------------
local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(460, 600)
Main.Position = UDim2.new(0.5,-230,0.5,-300)
Main.BackgroundColor3 = Color3.fromRGB(16,16,16)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)
makeDraggable(Main)

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
Title.Text = "🔥 FPS OPTIMIZER PRO v1.9.3 BETA"
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
-- OPTIMIZATION LEVEL SYSTEM
--------------------------------------------------
_G.OptimizationLevel = "MEDIO" -- BAIXO | MEDIO | ALTO | ULTRA

local LevelBar = Instance.new("Frame", Main)
LevelBar.Size = UDim2.new(1,-20,0,42)
LevelBar.Position = UDim2.new(0,10,0,52)
LevelBar.BackgroundColor3 = Color3.fromRGB(20,20,20)
LevelBar.BorderSizePixel = 0
Instance.new("UICorner", LevelBar)

local LevelLayout = Instance.new("UIListLayout", LevelBar)
LevelLayout.FillDirection = Enum.FillDirection.Horizontal
LevelLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
LevelLayout.VerticalAlignment = Enum.VerticalAlignment.Center
LevelLayout.Padding = UDim.new(0,8)

local function createLevelButton(text, level)
	local btn = Instance.new("TextButton", LevelBar)
	btn.Size = UDim2.fromOffset(95,28)
	btn.Text = text
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 13
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		_G.OptimizationLevel = level

		for _,b in ipairs(LevelBar:GetChildren()) do
			if b:IsA("TextButton") then
				b.BackgroundColor3 = Color3.fromRGB(35,35,35)
			end
		end

		btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
	end)

	return btn
end

local LowBtn   = createLevelButton("BAIXO", "BAIXO")
local MidBtn   = createLevelButton("MÉDIO", "MEDIO")
local HighBtn  = createLevelButton("ALTO",  "ALTO")
local UltraBtn = createLevelButton("ULTRA", "ULTRA")

-- nível padrão visual
MidBtn.BackgroundColor3 = Color3.fromRGB(120,0,0)

--------------------------------------------------
-- SCROLL (MANTIDO)
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,104)
Scroll.Size = UDim2.new(1,-20,1,-134)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.ScrollBarThickness = 6
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0, Layout.AbsoluteContentSize.Y + 12)
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
-- FUNÇÕES ANTIGAS (100% MANTIDAS)
--------------------------------------------------

createToggle("📊 Mostrar FPS (tempo real)", function()
	fpsEnabled = true
	fpsLabel.Visible = true
end,function()
	fpsEnabled = false
	fpsLabel.Visible = false
end)

local gcRunning = false
createToggle("🧹 Garbage Collector", function()
	gcRunning = true
	task.spawn(function()
		while gcRunning do
			collectgarbage("collect")
			task.wait(
				OptimizationLevel == "BAIXO" and 20 or
				OptimizationLevel == "MEDIO" and 10 or
				OptimizationLevel == "ALTO" and 5 or
				1
			)
		end
	end)
end,function()
	gcRunning = false
end)


createToggle("⚡ FPS Boost", function()
	if OptimizationLevel ~= "BAIXO" then
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end
end,function()
	settings().Rendering.QualityLevel = Original.Quality
end)


createToggle("📉 Reduzir FOV", function()
	camera.FieldOfView =
		OptimizationLevel == "BAIXO" and 70 or
		OptimizationLevel == "MEDIO" and 65 or
		OptimizationLevel == "ALTO" and 58 or
		50
end,function()
	camera.FieldOfView = Original.FOV
end)

createToggle("🚫 Desativar Pós-Processamento", function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then
			Original.Effects[v] = v.Enabled
			if OptimizationLevel ~= "BAIXO" then
				v.Enabled = false
			end
		end
	end
end,function()
	for v,state in pairs(Original.Effects) do
		if v then v.Enabled = state end
	end
end)



createToggle("☁️ Remover Atmosphere", function()
	if OptimizationLevel ~= "BAIXO" then
		local a = Lighting:FindFirstChildOfClass("Atmosphere")
		if a then a.Enabled = false end
	end
end)

createToggle("🌌 Remover Skybox", function()
	if OptimizationLevel ~= "BAIXO" then
		local s = Lighting:FindFirstChildOfClass("Sky")
		if s then s.Parent = nil end
	end
end)

createToggle("✨ Desativar Partículas", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then
			v.Enabled = OptimizationLevel == "BAIXO"
		end
	end
end)


local function disableClass(class)
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA(class) then
			v.Enabled = OptimizationLevel ~= "BAIXO"
		end
	end
end

createToggle("🔥 Desativar Fire", function() disableClass("Fire") end)
createToggle("💨 Desativar Smoke", function() disableClass("Smoke") end)
createToggle("🧵 Desativar Trails", function() disableClass("Trail") end)


createToggle("🧱 Materiais Plásticos", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Material = Enum.Material.Plastic
			v.CastShadow = OptimizationLevel ~= "MEDIO"
		end
	end
end)


createToggle("🖼️ Remover Decals", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Decal") or v:IsA("Texture") then
			v.Transparency =
				OptimizationLevel == "BAIXO" and 0 or
				OptimizationLevel == "MEDIO" and 0.6 or
				1
		end
	end
end)

createToggle("📦 Simplificar MeshParts", function()
	if OptimizationLevel ~= "BAIXO" then
		for _,v in ipairs(workspace:GetDescendants()) do
			if v:IsA("MeshPart") then
				v.RenderFidelity = Enum.RenderFidelity.Performance
			end
		end
	end
end)


createToggle("🛑 Desativar Sounds", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Sound") then
			v.Volume =
				OptimizationLevel == "BAIXO" and v.Volume or
				OptimizationLevel == "MEDIO" and 0.3 or
				0
		end
	end
end)



createToggle("💡 Lighting Compatibility", function()
	if OptimizationLevel ~= "BAIXO" then
		Lighting.Technology = Enum.Technology.Compatibility
	end
end,function()
	Lighting.Technology = Original.Technology
end)

createToggle("📉 Reduzir Brightness", function()
	Lighting.Brightness =
		OptimizationLevel == "BAIXO" and Original.Brightness or
		1
end,function()
	Lighting.Brightness = Original.Brightness
end)

createToggle("⚙️ Ultra Performance Mode", function()
	if OptimizationLevel == "ULTRA" then
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
		Lighting.GlobalShadows = false
	end
end)

createToggle("📏 Reduzir Distance Rendering", function()
	if OptimizationLevel == "BAIXO" then return end
	camera:GetPropertyChangedSignal("CFrame"):Wait()
	if OptimizationLevel == "MEDIO" then
		camera.MaxAxisFieldOfView = 70
	elseif OptimizationLevel == "ALTO" then
		camera.MaxAxisFieldOfView = 60
	elseif OptimizationLevel == "ULTRA" then
		camera.MaxAxisFieldOfView = 50
	end
end)

createToggle("🖼️ Limitar Textures Grandes", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Texture") and v.StudsPerTileU > 4 then
			if OptimizationLevel ~= "MEDIO" then
				v.Transparency = 1
			end
		end
	end
end)

createToggle("🌘 Reduzir CastShadow Dinâmico", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			if OptimizationLevel == "MEDIO" then
				v.CastShadow = math.random() > 0.5
			else
				v.CastShadow = false
			end
		end
	end
end)

createToggle("⚖️ Limitar Física de Objetos", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and not v.Anchored then
			if OptimizationLevel ~= "MEDIO" then
				v.CustomPhysicalProperties = PhysicalProperties.new(0,0,0,0,0)
			end
		end
	end
end)

createToggle("🫥 Reduzir Transparências", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Transparency > 0 then
			if OptimizationLevel == "MEDIO" then
				v.Transparency = math.clamp(v.Transparency,0,0.5)
			else
				v.Transparency = 1
			end
		end
	end
end)

createToggle("📍 Desativar ProximityPrompts", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ProximityPrompt") then
			v.Enabled = false
		end
	end
end)

createToggle("⏳ Reduzir Trail Lifetime", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then
			v.Lifetime = OptimizationLevel == "MEDIO" and 0.3 or 0.1
		end
	end
end)

createToggle("🪟 Limitar ViewportFrames", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ViewportFrame") then
			v:Destroy()
		end
	end
end)

createToggle("🔢 Reduzir Updates Numéricos", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("NumberValue") then
			v.Changed:Connect(function() end)
		end
	end
end)

createToggle("🎭 Desativar SurfaceAppearance", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SurfaceAppearance") then
			v:Destroy()
		end
	end
end)

createToggle("🌍 Otimizar Streaming", function()
	if OptimizationLevel == "BAIXO" then return end
	pcall(function()
		workspace.StreamingEnabled = OptimizationLevel ~= "MEDIO"
	end)
end)

createToggle("🧍 Limitar Estados do Humanoid", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Humanoid") then
			v:SetStateEnabled(Enum.HumanoidStateType.Climbing,false)
			v:SetStateEnabled(Enum.HumanoidStateType.Swimming,false)
		end
	end
end)

createToggle("🏷️ Remover BillboardGui Distantes", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BillboardGui") then
			v.Enabled = false
		end
	end
end)

createToggle("🌫️ Remover Blur de UI", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("BlurEffect") then
			v.Enabled = false
		end
	end
end)

createToggle("🔗 Otimizar Welds", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("WeldConstraint") then
			v.Enabled = false
		end
	end
end)

createToggle("📌 Reduzir Attachments", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Attachment") then
			v.Visible = false
		end
	end
end)

createToggle("🗺️ Limitar Pathfinding", function()
	if OptimizationLevel ~= "ULTRA" then return end
	game:GetService("PathfindingService"):Destroy()
end)

createToggle("🎥 Reduzir Camera Shake", function()
	if OptimizationLevel ~= "BAIXO" then
		camera.CameraType = Enum.CameraType.Custom
	end
end)

createToggle("📡 Reduzir Raycasting", function()
	if OptimizationLevel ~= "ULTRA" then return end
	debug.setmemorycategory("Raycast",0)
end)

createToggle("🌌 Otimizar Gravidade", function()
	if OptimizationLevel == "ALTO" or OptimizationLevel == "ULTRA" then
		workspace.Gravity = 150
	end
end)

createToggle("🛠️ Desativar Animações de Tools", function()
	if OptimizationLevel ~= "ULTRA" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Tool") then
			v.Enabled = false
		end
	end
end)

createToggle("🧹 Reduzir Debris Lifetime", function()
	if OptimizationLevel ~= "BAIXO" then
		game:GetService("Debris").MaxItems = OptimizationLevel == "MEDIO" and 100 or 20
	end
end)

--------------------------------------------------
-- +10 NOVAS FUNÇÕES DE OTIMIZAÇÃO (EXTRAS)
--------------------------------------------------

createToggle("🎩 Ocultar Acessórios (Hats)", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Accessory") and v:FindFirstChild("Handle") then
			if OptimizationLevel == "BAIXO" then
				v.Handle.Transparency = 0
			elseif OptimizationLevel == "MEDIO" then
				v.Handle.Transparency = 0.5
			else
				v.Handle.Transparency = 1
				v.Handle.CanCollide = false
			end
		end
	end
end)

createToggle("💠 Desativar Highlights", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Highlight") then
			v.Enabled = false
		end
	end
end)


createToggle("📡 Desativar Beams", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Beam") then
			v.Enabled = false
		end
	end
end)


createToggle("💡 Desativar PointLights", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("PointLight") then
			v.Enabled = false
		end
	end
end)


createToggle("🔦 Desativar SpotLights", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SpotLight") then
			v.Enabled = false
		end
	end
end)


createToggle("🏮 Desativar SurfaceLights", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SurfaceLight") then
			v.Enabled = false
		end
	end
end)


createToggle("🪞 Reduzir Reflectance", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			if OptimizationLevel == "BAIXO" then
				-- não altera
			elseif OptimizationLevel == "MEDIO" then
				v.Reflectance = 0.2
			else
				v.Reflectance = 0
			end
		end
	end
end)


createToggle("📦 Otimizar Colisões", function()
	if OptimizationLevel == "BAIXO" then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Size.Magnitude < (OptimizationLevel == "MEDIO" and 3 or 6) then
			v.CanCollide = false
		end
	end
end)


createToggle("🌊 Simplificar Água", function()
	if OptimizationLevel == "BAIXO" then return end
	local terrain = workspace:FindFirstChildOfClass("Terrain")
	if terrain then
		if OptimizationLevel == "MEDIO" then
			terrain.WaterWaveSize = 0.2
			terrain.WaterWaveSpeed = 0.2
		else
			terrain.WaterWaveSize = 0
			terrain.WaterWaveSpeed = 0
		end
	end
end)


createToggle("🌑 Desativar Sombras", function()
	if OptimizationLevel == "BAIXO" then return end
	Lighting.GlobalShadows = false
end,function()
	Lighting.GlobalShadows = Original.GlobalShadows
end)


createToggle("🌱 Remover Grama do Terrain", function()
	if OptimizationLevel ~= "ULTRA" then return end
	local terrain = workspace:FindFirstChildOfClass("Terrain")
	if terrain then
		terrain.Decoration = false
	end
end)


createToggle("📱 Android FPS Unlocker", function()
	if setfpscap then
		setfpscap(
			OptimizationLevel == "MEDIO" and 90 or
			OptimizationLevel == "ALTO" and 120 or
			OptimizationLevel == "ULTRA" and 240 or
			60
		)
	end
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
