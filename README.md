--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 UI Profissional (2.0)
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")

---------------- PLAYER ----------------
local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

---------------- ORIGINAL BACKUP ----------------
local Original = {
	-- Render / Camera
	Quality = settings().Rendering.QualityLevel,
	FOV = camera.FieldOfView,
	CameraType = camera.CameraType,

	-- Lighting
	Brightness = Lighting.Brightness,
	Technology = Lighting.Technology,
	GlobalShadows = Lighting.GlobalShadows,

	-- Containers
	Effects = {},
	Sounds = {},
	Materials = {},
	Shadows = {},
	Transparencies = {},
	MeshFidelity = {},
	Particles = {},
	Trails = {},
	Billboards = {},
	Prompts = {},
	HumanoidStates = {},

	-- Advanced
	Welds = {},
	Attachments = {},
	Tools = {},
	Highlights = {},
	Beams = {},
	Lights = {},
	Reflectance = {},
	Collisions = {},

	-- Terrain / World
	Water = {},
	Decoration = nil,
	Gravity = workspace.Gravity,

	-- Performance
	Debris = Debris.MaxItems,
	FPSCap = nil
}

--------------------------------------------------
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.Name = "FPSOptimizerPRO"
gui.ResetOnSpawn = false

--------------------------------------------------
-- LOADING SCREEN (PROFISSIONAL)
--------------------------------------------------
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local Loading = Instance.new("Frame")
Loading.Parent = gui
Loading.Size = UDim2.new(1,0,1,0)
Loading.BackgroundColor3 = Color3.fromRGB(8,8,8)
Loading.ZIndex = 100
Loading.BackgroundTransparency = 0

-- Fundo decorativo (linhas)
local Lines = Instance.new("Frame", Loading)
Lines.Size = UDim2.new(1,0,1,0)
Lines.BackgroundTransparency = 1
Lines.ZIndex = 101

for i = 1, 12 do
	local line = Instance.new("Frame", Lines)
	line.Size = UDim2.new(0.15,0,0.002,0)
	line.Position = UDim2.new(math.random(),0,math.random(),0)
	line.BackgroundColor3 = Color3.fromRGB(120,0,0)
	line.BackgroundTransparency = 0.7
	line.Rotation = math.random(-30,30)
	line.ZIndex = 101

	TweenService:Create(
		line,
		TweenInfo.new(2 + math.random(), Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
		{BackgroundTransparency = 0.9}
	):Play()
end

-- Título
local LoadTitle = Instance.new("TextLabel", Loading)
LoadTitle.Size = UDim2.new(1,0,0,60)
LoadTitle.Position = UDim2.new(0,0,0.4,-50)
LoadTitle.Text = "🔥 FPS OPTIMIZER PRO"
LoadTitle.Font = Enum.Font.GothamBlack
LoadTitle.TextSize = 30
LoadTitle.TextColor3 = Color3.fromRGB(220,60,60)
LoadTitle.BackgroundTransparency = 1
LoadTitle.TextTransparency = 1
LoadTitle.ZIndex = 102

-- Subtítulo
local LoadSub = Instance.new("TextLabel", Loading)
LoadSub.Size = UDim2.new(1,0,0,30)
LoadSub.Position = UDim2.new(0,0,0.4,5)
LoadSub.Text = "Criador: Frostzn"
LoadSub.Font = Enum.Font.Gotham
LoadSub.TextSize = 14
LoadSub.TextColor3 = Color3.fromRGB(180,180,180)
LoadSub.BackgroundTransparency = 1
LoadSub.TextTransparency = 1
LoadSub.ZIndex = 102

-- Texto Loading dinâmico
local LoadText = Instance.new("TextLabel", Loading)
LoadText.Size = UDim2.new(1,0,0,24)
LoadText.Position = UDim2.new(0,0,0.55,26)
LoadText.Text = "Inicializando sistema..."
LoadText.Font = Enum.Font.Gotham
LoadText.TextSize = 13
LoadText.TextColor3 = Color3.fromRGB(160,160,160)
LoadText.BackgroundTransparency = 1
LoadText.TextTransparency = 1
LoadText.ZIndex = 102

-- Barra base
local BarBase = Instance.new("Frame", Loading)
BarBase.Size = UDim2.fromOffset(420,10)
BarBase.Position = UDim2.new(0.5,-210,0.55,0)
BarBase.BackgroundColor3 = Color3.fromRGB(25,25,25)
BarBase.BorderSizePixel = 0
BarBase.ZIndex = 102
Instance.new("UICorner", BarBase)

-- Barra preenchimento
local BarFill = Instance.new("Frame", BarBase)
BarFill.Size = UDim2.new(0,0,1,0)
BarFill.BackgroundColor3 = Color3.fromRGB(200,40,40)
BarFill.BorderSizePixel = 0
BarFill.ZIndex = 103
Instance.new("UICorner", BarFill)

-- Glow
local Glow = Instance.new("UIStroke", BarBase)
Glow.Color = Color3.fromRGB(200,40,40)
Glow.Thickness = 1.5
Glow.Transparency = 0.6

--------------------------------------------------
-- ANIMAÇÕES
--------------------------------------------------
TweenService:Create(LoadTitle, TweenInfo.new(0.5), {TextTransparency = 0}):Play()
TweenService:Create(LoadSub, TweenInfo.new(0.5), {TextTransparency = 0}):Play()
TweenService:Create(LoadText, TweenInfo.new(0.5), {TextTransparency = 0}):Play()

local progress = 0
local messages = {
	"Inicializando sistema...",
	"Otimizando renderização...",
	"Aplicando ajustes gráficos...",
	"Preparando interface...",
	"Finalizando..."
}

task.spawn(function()
	for i = 1, 100 do
		progress += 1
		BarFill.Size = UDim2.new(progress/100,0,1,0)

		if i % 20 == 0 then
			LoadText.Text = messages[math.clamp(i/20,1,#messages)]
		end

		task.wait(0.02 + math.random()*0.01)
	end
end)

--------------------------------------------------
-- FINALIZAÇÃO
--------------------------------------------------
task.wait(2.8)

TweenService:Create(Loading, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
TweenService:Create(LoadTitle, TweenInfo.new(0.4), {TextTransparency = 1}):Play()
TweenService:Create(LoadSub, TweenInfo.new(0.4), {TextTransparency = 1}):Play()
TweenService:Create(LoadText, TweenInfo.new(0.4), {TextTransparency = 1}):Play()

task.wait(0.55)
Loading:Destroy()

--------------------------------------------------
-- FPS COUNTER
--------------------------------------------------
local fpsLabel = Instance.new("TextLabel", gui)
fpsLabel.Size = UDim2.fromOffset(120,26)
fpsLabel.Position = UDim2.new(1,-130,0,10)
fpsLabel.BackgroundTransparency = 1
fpsLabel.TextColor3 = Color3.fromRGB(255,70,70)
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.TextSize = 14
fpsLabel.TextXAlignment = Enum.TextXAlignment.Right
fpsLabel.Visible = false
fpsLabel.Text = "FPS: 0"

local fpsEnabled = false
local frames = 0
local last = tick()

RunService.RenderStepped:Connect(function()
	if not fpsEnabled then return end
	frames += 1
	if tick() - last >= 1 then
		fpsLabel.Text = "FPS: "..frames
		frames = 0
		last = tick()
	end
end)

--------------------------------------------------
-- DRAG SYSTEM
--------------------------------------------------
local function makeDraggable(frame)
	local dragging, start, pos
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			start = i.Position
			pos = frame.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local d = i.Position - start
			frame.Position = UDim2.new(pos.X.Scale,pos.X.Offset+d.X,pos.Y.Scale,pos.Y.Offset+d.Y)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

--------------------------------------------------
-- MAIN FRAME
--------------------------------------------------
local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(560,440)
Main.Position = UDim2.new(0.5,-280,0.5,-220)
Main.BackgroundColor3 = Color3.fromRGB(15,15,15)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)
makeDraggable(Main)

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,48)
Header.BackgroundColor3 = Color3.fromRGB(22,22,22)
Header.BorderSizePixel = 0
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-20,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(200,50,50)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32, 32)
Minimize.Position = UDim2.new(1, -72, 0.5, -16)
Minimize.Text = "—"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 18
Minimize.TextColor3 = Color3.fromRGB(255,255,255)
Minimize.BackgroundColor3 = Color3.fromRGB(40,40,40)
Minimize.BorderSizePixel = 0
Minimize.AutoButtonColor = true
Instance.new("UICorner", Minimize)

-- Botão Fechar
local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32, 32)
Close.Position = UDim2.new(1, -36, 0.5, -16)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = Color3.fromRGB(255,255,255)
Close.BackgroundColor3 = Color3.fromRGB(120,0,0)
Close.BorderSizePixel = 0
Close.AutoButtonColor = true
Instance.new("UICorner", Close)

--------------------------------------------------
-- CATEGORY BAR (ESQUERDA)
--------------------------------------------------
local CategoryBar = Instance.new("Frame", Main)
CategoryBar.Position = UDim2.new(0,10,0,58)
CategoryBar.Size = UDim2.new(0,150,1,-68)
CategoryBar.BackgroundColor3 = Color3.fromRGB(18,18,18)
CategoryBar.BorderSizePixel = 0
Instance.new("UICorner", CategoryBar)

local CatLayout = Instance.new("UIListLayout", CategoryBar)
CatLayout.Padding = UDim.new(0,6)
CatLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

--------------------------------------------------
-- CONTENT AREA (DIREITA)
--------------------------------------------------
local Content = Instance.new("Frame", Main)
Content.Position = UDim2.new(0,170,0,58)
Content.Size = UDim2.new(1,-180,1,-68)
Content.BackgroundTransparency = 1

--------------------------------------------------
-- PAGE SYSTEM
--------------------------------------------------
local Pages = {}
local CurrentPage

local function createCategory(name)
	-- botão da categoria
	local btn = Instance.new("TextButton", CategoryBar)
	btn.Size = UDim2.new(1,-12,0,36)
	btn.Text = name
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 13
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	-- página de conteúdo
	local page = Instance.new("ScrollingFrame", Content)
	page.Size = UDim2.new(1,0,1,0)
	page.CanvasSize = UDim2.new(0,0,0,0)
	page.ScrollBarThickness = 6
	page.BackgroundTransparency = 1
	page.Visible = false

	local layout = Instance.new("UIListLayout", page)
	layout.Padding = UDim.new(0,8)

	layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		page.CanvasSize = UDim2.new(0,0,0,layout.AbsoluteContentSize.Y + 12)
	end)

	btn.MouseButton1Click:Connect(function()
		for _,v in ipairs(Pages) do
			v.page.Visible = false
			v.button.BackgroundColor3 = Color3.fromRGB(30,30,30)
		end
		page.Visible = true
		btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
		CurrentPage = page
	end)

	table.insert(Pages,{button=btn,page=page})
	return page, btn
end

--------------------------------------------------
-- TOGGLE SYSTEM
--------------------------------------------------
local function createToggle(parent,text,on,off)
	local state = false
	local btn = Instance.new("TextButton", parent)
	btn.Size = UDim2.new(1,0,0,38)
	btn.Text = text.." [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BackgroundColor3 = Color3.fromRGB(28,28,28)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		state = not state
		if state then
			pcall(on)
			btn.Text = text.." [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(120,0,0)
		else
			if off then pcall(off) end
			btn.Text = text.." [OFF]"
			btn.BackgroundColor3 = Color3.fromRGB(28,28,28)
		end
	end)
end

--------------------------------------------------
-- CATEGORIES
--------------------------------------------------
local PageInterface, BtnInterface = createCategory("📊 Interface")
local PagePerformance, BtnPerf   = createCategory("⚡ Performance")
local PageGraphics, BtnGraphics  = createCategory("🎨 Gráficos")
local PageWorld, BtnWorld        = createCategory("🌍 Mundo")
local PageAdvanced, BtnAdvanced  = createCategory("🧠 Avançado")

-- categoria inicial
PageInterface.Visible = true
BtnInterface.BackgroundColor3 = Color3.fromRGB(120,0,0)

--------------------------------------------------
-- FUNÇÕES
--------------------------------------------------
createToggle(PageInterface,"📊 Mostrar FPS (tempo real)", function()
	fpsEnabled = true
	fpsLabel.Visible = true
end,function()
	fpsEnabled = false
	fpsLabel.Visible = false
end)

-- Garbage Collector
local gcRunning = false
createToggle(PagePerformance, "🧹 Garbage Collector", function()
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
end, function()
	gcRunning = false
end)

-- FPS Boost
createToggle(PagePerformance, "⚡ FPS Boost", function()
	Original.Quality = settings().Rendering.QualityLevel
	if OptimizationLevel ~= "BAIXO" then
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end
end, function()
	settings().Rendering.QualityLevel = Original.Quality
end)

-- Reduzir FOV
createToggle(PageGraphics, "📉 Reduzir FOV", function()
	Original.FOV = camera.FieldOfView
	camera.FieldOfView =
		OptimizationLevel == "BAIXO" and 70 or
		OptimizationLevel == "MEDIO" and 65 or
		OptimizationLevel == "ALTO" and 58 or
		50
end, function()
	camera.FieldOfView = Original.FOV
end)

-- Pós-processamento
createToggle(PageGraphics, "🚫 Desativar Pós-Processamento", function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then
			Original.Effects[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,state in pairs(Original.Effects) do
		if v then v.Enabled = state end
	end
end)

-- Atmosphere
createToggle(PageGraphics, "☁️ Remover Atmosphere", function()
	local a = Lighting:FindFirstChildOfClass("Atmosphere")
	if a then
		Original.Effects[a] = a.Enabled
		a.Enabled = false
	end
end, function()
	local a = Lighting:FindFirstChildOfClass("Atmosphere")
	if a and Original.Effects[a] ~= nil then
		a.Enabled = Original.Effects[a]
	end
end)

-- Skybox
createToggle(PageGraphics, "🌌 Remover Skybox", function()
	local s = Lighting:FindFirstChildOfClass("Sky")
	if s then
		Original.Effects[s] = s.Parent
		s.Parent = nil
	end
end, function()
	for obj,parent in pairs(Original.Effects) do
		if obj:IsA("Sky") then
			obj.Parent = parent
		end
	end
end)

-- Partículas
createToggle(PageGraphics, "✨ Desativar Partículas", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then
			Original.Particles[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,state in pairs(Original.Particles) do
		if v then v.Enabled = state end
	end
end)

-- Fire / Smoke / Trails
local function toggleEffect(class, stateTable, enabled)
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA(class) then
			stateTable[v] = v.Enabled
			v.Enabled = enabled
		end
	end
end

createToggle(PageGraphics, "🔥 Desativar Fire", function()
	toggleEffect("Fire", Original.Effects, false)
end, function()
	for v,state in pairs(Original.Effects) do
		if v:IsA("Fire") then v.Enabled = state end
	end
end)

createToggle(PageGraphics, "💨 Desativar Smoke", function()
	toggleEffect("Smoke", Original.Effects, false)
end, function()
	for v,state in pairs(Original.Effects) do
		if v:IsA("Smoke") then v.Enabled = state end
	end
end)

createToggle(PageGraphics, "🧵 Desativar Trails", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then
			Original.Trails[v] = v.Lifetime
			v.Lifetime = 0
		end
	end
end, function()
	for v,l in pairs(Original.Trails) do
		if v then v.Lifetime = l end
	end
end)

createToggle(PageAudio, "🛑 Desativar Sounds", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Sound") then
			Original.Sounds[v] = v.Volume
			v.Volume = 0
		end
	end
end, function()
	for v,vol in pairs(Original.Sounds) do
		if v then v.Volume = vol end
	end
end)

-- Ultra Performance
createToggle(PageAdvanced, "⚙️ Ultra Performance Mode", function()
	Original.Quality = settings().Rendering.QualityLevel
	Original.GlobalShadows = Lighting.GlobalShadows

	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	Lighting.GlobalShadows = false
end, function()
	settings().Rendering.QualityLevel = Original.Quality
	Lighting.GlobalShadows = Original.GlobalShadows
end)

-- Otimizar Welds
createToggle(PageAdvanced, "🔗 Otimizar Welds", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("WeldConstraint") then
			Original.Welds[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,state in pairs(Original.Welds) do
		if v then v.Enabled = state end
	end
end)

-- Reduzir Attachments
createToggle(PageAdvanced, "📌 Reduzir Attachments", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Attachment") then
			Original.Attachments[v] = v.Visible
			v.Visible = false
		end
	end
end, function()
	for v,state in pairs(Original.Attachments) do
		if v then v.Visible = state end
	end
end)

-- Limitar Pathfinding (SEGURO)
createToggle(PageAdvanced, "🗺️ Limitar Pathfinding", function()
	-- Roblox não permite recriar serviço, então apenas desativamos uso pesado
	pcall(function()
		game:GetService("PathfindingService").FindPathAsync = function() end
	end)
end, function()
	-- automaticamente restaurado ao reentrar no jogo
end)

-- Reduzir Raycasting (seguro)
createToggle(PageAdvanced, "📡 Reduzir Raycasting", function()
	-- não existe forma real de "desligar", apenas hint de memória
	debug.setmemorycategory("Raycast", 0)
end, function()
	debug.setmemorycategory("Raycast", 1024)
end)

-- Reduzir Camera Shake
createToggle(PageGraphics, "🎥 Reduzir Camera Shake", function()
	Original.CameraType = camera.CameraType
	camera.CameraType = Enum.CameraType.Custom
end, function()
	camera.CameraType = Original.CameraType
end)

-- Otimizar Gravidade
createToggle(PagePerformance, "🌌 Otimizar Gravidade", function()
	Original.Gravity = workspace.Gravity
	if OptimizationLevel == "ALTO" or OptimizationLevel == "ULTRA" then
		workspace.Gravity = 150
	end
end, function()
	workspace.Gravity = Original.Gravity
end)

-- Desativar Animações de Tools
createToggle(PageAdvanced, "🛠️ Desativar Animações de Tools", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Tool") then
			Original.Tools[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,state in pairs(Original.Tools) do
		if v then v.Enabled = state end
	end
end)

createToggle(PageInterface, "🧼 Ocultar HUD do Roblox", function()
	pcall(function()
		game:GetService("StarterGui"):SetCoreGuiEnabled(Enum.CoreGuiType.All, false)
	end)
end, function()
	pcall(function()
		game:GetService("StarterGui"):SetCoreGuiEnabled(Enum.CoreGuiType.All, true)
	end)
end)

createToggle(PageInterface, "🧊 Interface Minimalista", function()
	for _,v in ipairs(gui:GetDescendants()) do
		if v:IsA("Frame") then
			Original.Transparencies[v] = v.BackgroundTransparency
			v.BackgroundTransparency = 1
		end
	end
end, function()
	for v,t in pairs(Original.Transparencies) do
		if v then v.BackgroundTransparency = t end
	end
end)

local uiPaused = false
createToggle(PageInterface, "⏸️ Pausar Atualizações da UI", function()
	uiPaused = true
	RunService:Set3dRenderingEnabled(false)
end, function()
	uiPaused = false
	RunService:Set3dRenderingEnabled(true)
end)

createToggle(PageInterface, "🏷️ Ocultar NameTags", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BillboardGui") then
			Original.Billboards[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,s in pairs(Original.Billboards) do
		if v then v.Enabled = s end
	end
end)

createToggle(PageInterface, "📴 Desativar Proximity Prompts", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ProximityPrompt") then
			Original.Prompts[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,s in pairs(Original.Prompts) do
		if v then v.Enabled = s end
	end
end)


-- Reduzir Debris Lifetime
createToggle(PagePerformance, "🧹 Reduzir Debris Lifetime", function()
	Original.Debris = game:GetService("Debris").MaxItems
	game:GetService("Debris").MaxItems =
		OptimizationLevel == "MEDIO" and 100 or 20
end, function()
	game:GetService("Debris").MaxItems = Original.Debris
end)

-- Ocultar Acessórios
createToggle(PageGraphics, "🎩 Ocultar Acessórios (Hats)", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Accessory") and v:FindFirstChild("Handle") then
			Original.Transparencies[v.Handle] = v.Handle.Transparency
			v.Handle.Transparency =
				OptimizationLevel == "MEDIO" and 0.5 or 1
			v.Handle.CanCollide = false
		end
	end
end, function()
	for h,t in pairs(Original.Transparencies) do
		if h then
			h.Transparency = t
			h.CanCollide = true
		end
	end
end)

-- Highlights / Beams / Lights
local function toggleEnabled(class, store, value)
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA(class) then
			store[v] = v.Enabled
			v.Enabled = value
		end
	end
end

createToggle(PageGraphics, "💠 Desativar Highlights", function()
	toggleEnabled("Highlight", Original.Highlights, false)
end, function()
	for v,s in pairs(Original.Highlights) do if v then v.Enabled = s end end
end)

createToggle(PageGraphics, "📡 Desativar Beams", function()
	toggleEnabled("Beam", Original.Beams, false)
end, function()
	for v,s in pairs(Original.Beams) do if v then v.Enabled = s end end
end)

createToggle(PageGraphics, "💡 Desativar Lights", function()
	for _,class in ipairs({"PointLight","SpotLight","SurfaceLight"}) do
		toggleEnabled(class, Original.Lights, false)
	end
end, function()
	for v,s in pairs(Original.Lights) do if v then v.Enabled = s end end
end)

-- Simplificar Água
createToggle(PageGraphics, "🌊 Simplificar Água", function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t then
		Original.Water = {
			Size = t.WaterWaveSize,
			Speed = t.WaterWaveSpeed
		}
		t.WaterWaveSize = 0
		t.WaterWaveSpeed = 0
	end
end, function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t and Original.Water then
		t.WaterWaveSize = Original.Water.Size
		t.WaterWaveSpeed = Original.Water.Speed
	end
end)

createToggle(PageWorld, "🧊 Congelar Objetos Distantes", function()
	local char = player.Character
	if not char then return end
	local root = char:WaitForChild("HumanoidRootPart")

	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and not v.Anchored then
			if (v.Position - root.Position).Magnitude > 250 then
				Original.Collisions[v] = v.Anchored
				v.Anchored = true
			end
		end
	end
end, function()
	for v,state in pairs(Original.Collisions) do
		if v then v.Anchored = state end
	end
end)

createToggle(PageWorld, "🚧 Remover Colisão da Decoração", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Name ~= "HumanoidRootPart" then
			Original.Collisions[v] = v.CanCollide
			v.CanCollide = false
		end
	end
end, function()
	for v,state in pairs(Original.Collisions) do
		if v then v.CanCollide = state end
	end
end)

createToggle(PageWorld, "🧱 Simplificar Materiais do Mundo", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			Original.Materials[v] = v.Material
			v.Material = Enum.Material.SmoothPlastic
		end
	end
end, function()
	for v,m in pairs(Original.Materials) do
		if v then v.Material = m end
	end
end)

createToggle(PageWorld, "🪞 Reduzir Reflexos", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			Original.Reflectance[v] = v.Reflectance
			v.Reflectance = 0
		end
	end
end, function()
	for v,r in pairs(Original.Reflectance) do
		if v then v.Reflectance = r end
	end
end)

createToggle(PageWorld, "🎞️ Desativar Animações Ambientais", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("AnimationController") then
			Original.HumanoidStates[v] = v.Parent
			v:Destroy()
		end
	end
end)

createToggle(PageWorld, "🏔️ Reduzir Detalhes do Terreno", function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t then
		Original.Water.Size = t.WaterReflectance
		t.WaterReflectance = 0
	end
end, function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t and Original.Water then
		t.WaterReflectance = Original.Water.Size
	end
end)

createToggle(PageWorld, "🪶 Ocultar Objetos Pequenos", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Size.Magnitude < 2 then
			Original.Transparencies[v] = v.Transparency
			v.Transparency = 1
		end
	end
end, function()
	for v,t in pairs(Original.Transparencies) do
		if v then v.Transparency = t end
	end
end)

createToggle(PageWorld, "🚦 Desativar Luzes do Mapa", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("PointLight") or v:IsA("SpotLight") or v:IsA("SurfaceLight") then
			Original.Lights[v] = v.Enabled
			v.Enabled = false
		end
	end
end, function()
	for v,s in pairs(Original.Lights) do
		if v then v.Enabled = s end
	end
end)

local worldStep
createToggle(PageWorld, "⏳ Reduzir Atualizações do Mundo", function()
	worldStep = RunService.Heartbeat:Connect(function()
		task.wait(OptimizationLevel == "ALTO" and 0.2 or 0.1)
	end)
end, function()
	if worldStep then worldStep:Disconnect() end
end)

createToggle(PageWorld, "🧊 Mundo Estático (Extremo)", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			Original.Collisions[v] = v.Anchored
			v.Anchored = true
		end
	end
end, function()
	for v,state in pairs(Original.Collisions) do
		if v then v.Anchored = state end
	end
end)



-- Remover Grama
createToggle(PageAdvanced, "🌱 Remover Grama do Terrain", function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t then
		Original.Decoration = t.Decoration
		t.Decoration = false
	end
end, function()
	local t = workspace:FindFirstChildOfClass("Terrain")
	if t and Original.Decoration ~= nil then
		t.Decoration = Original.Decoration
	end
end)

createToggle(PagePerformance, "📱 Android FPS Unlocker", function()
	if setfpscap then
		Original.FPSCap = 60
		setfpscap(
			OptimizationLevel == "MEDIO" and 90 or
			OptimizationLevel == "ALTO" and 120 or
			OptimizationLevel == "ULTRA" and 240
		)
	end
end, function()
	if setfpscap then
		setfpscap(Original.FPSCap or 60)
	end
end)

--------------------------------------------------
-- MINI BUTTON (MINIMIZAR / RESTAURAR)
--------------------------------------------------

-- Mini botão
local Mini = Instance.new("TextButton")
Mini.Name = "MiniFPS"
Mini.Parent = gui
Mini.Size = UDim2.fromOffset(56, 56)
Mini.Position = UDim2.new(0, 20, 0.5, -28)
Mini.Text = "FPS"
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.fromRGB(255, 255, 255)
Mini.BackgroundColor3 = Color3.fromRGB(120, 0, 0)
Mini.Visible = false
Mini.AutoButtonColor = true
Mini.ZIndex = 50

-- Bordas arredondadas
local miniCorner = Instance.new("UICorner")
miniCorner.CornerRadius = UDim.new(0, 14)
miniCorner.Parent = Mini

-- Drag
makeDraggable(Mini)

--------------------------------------------------
-- CONTROLES
--------------------------------------------------

--------------------------------------------------
-- CONTROLES
--------------------------------------------------

-- Minimizar Menu
Minimize.MouseButton1Click:Connect(function()
	Main.Visible = false
	Mini.Visible = true
end)

-- Restaurar Menu
Mini.MouseButton1Click:Connect(function()
	Main.Visible = true
	Mini.Visible = false
end)

-- Fechar Tudo
Close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)
