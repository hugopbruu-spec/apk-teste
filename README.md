--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.8 STABLE
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
-- SCROLL (MANTIDO)
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

createToggle("🚫 Desativar Pós-Processamento", function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then
			Original.Effects[v] = v.Enabled
			v.Enabled = false
		end
	end
end,function()
	for v,state in pairs(Original.Effects) do
		if v then v.Enabled = state end
	end
end)

createToggle("☁️ Remover Atmosphere", function()
	local a = Lighting:FindFirstChildOfClass("Atmosphere")
	if a then a.Enabled = false end
end)

createToggle("🌌 Remover Skybox", function()
	local s = Lighting:FindFirstChildOfClass("Sky")
	if s then s.Parent = nil end
end)

createToggle("✨ Desativar Partículas", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then v.Enabled = false end
	end
end)

createToggle("🔥 Desativar Fire", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Fire") then v.Enabled = false end
	end
end)

createToggle("💨 Desativar Smoke", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Smoke") then v.Enabled = false end
	end
end)

createToggle("🧵 Desativar Trails", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then v.Enabled = false end
	end
end)

createToggle("🧱 Materiais Plásticos", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Material = Enum.Material.Plastic
			v.CastShadow = false
		end
	end
end)

createToggle("🖼️ Remover Decals", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Decal") or v:IsA("Texture") then
			v.Transparency = 1
		end
	end
end)

createToggle("📦 Simplificar MeshParts", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("MeshPart") then
			v.RenderFidelity = Enum.RenderFidelity.Performance
		end
	end
end)

createToggle("🧠 Desativar Animações", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Animator") then
			v.Parent = nil
		end
	end
end)

createToggle("🛑 Desativar Sounds", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Sound") then v.Volume = 0 end
	end
end)

createToggle("📉 Reduzir Brightness", function()
	Lighting.Brightness = 1
end,function()
	Lighting.Brightness = Original.Brightness
end)

createToggle("⚙️ Ultra Performance Mode", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	Lighting.GlobalShadows = false
	Lighting.Brightness = 1
end)

--------------------------------------------------
-- NOVA FUNÇÃO FPS
--------------------------------------------------
createToggle("📊 Mostrar FPS (tempo real)", function()
	fpsEnabled = true
	fpsLabel.Visible = true
end,function()
	fpsEnabled = false
	fpsLabel.Visible = false
end)

--------------------------------------------------
-- +10 NOVAS FUNÇÕES DE OTIMIZAÇÃO (EXTRAS)
--------------------------------------------------

createToggle("🎩 Ocultar Acessórios (Hats)", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Accessory") then
			v.Handle.Transparency = 1
			v.Handle.CanCollide = false
		end
	end
end)

createToggle("💠 Desativar Highlights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Highlight") then
			v.Enabled = false
		end
	end
end)

createToggle("📡 Desativar Beams", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Beam") then
			v.Enabled = false
		end
	end
end)

createToggle("💡 Desativar PointLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("PointLight") then
			v.Enabled = false
		end
	end
end)

createToggle("🔦 Desativar SpotLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SpotLight") then
			v.Enabled = false
		end
	end
end)

createToggle("🏮 Desativar SurfaceLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SurfaceLight") then
			v.Enabled = false
		end
	end
end)

createToggle("🪞 Reduzir Reflectance", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Reflectance = 0
		end
	end
end)

createToggle("📦 Otimizar Colisões", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Size.Magnitude < 5 then
			v.CanCollide = false
		end
	end
end)

createToggle("🌊 Simplificar Água", function()
	if workspace:FindFirstChildOfClass("Terrain") then
		workspace.Terrain.WaterWaveSize = 0
		workspace.Terrain.WaterWaveSpeed = 0
	end
end)

createToggle("🌱 Remover Grama do Terrain", function()
	if workspace:FindFirstChildOfClass("Terrain") then
		workspace.Terrain.Decoration = false
	end
end)

--------------------------------------------------
-- ANDROID FPS UNLOCKER (SEGURO)
--------------------------------------------------
createToggle("📱 Android FPS Unlocker", function()

	-- Qualidade mínima absoluta
	pcall(function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end)

	-- Força modo performance
	pcall(function()
		UserSettings():GetService("UserGameSettings").SavedQualityLevel = Enum.SavedQualitySetting.QualityLevel1
	end)

	-- Mantém render ativo
	pcall(function()
		RunService:Set3dRenderingEnabled(true)
	end)

	-- Tenta desbloquear FPS (APENAS SE O EXECUTOR SUPORTAR)
	pcall(function()
		if setfpscap then
			setfpscap(240) -- tenta liberar até 240fps
		end
	end)

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
