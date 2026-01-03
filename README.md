--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.9 STABLE EXTENDED (FULL)
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
-- DRAG SYSTEM (PC + MOBILE)
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local drag, startPos, startInput

	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1
		or i.UserInputType == Enum.UserInputType.Touch then
			drag = true
			startPos = frame.Position
			startInput = i.Position
		end
	end)

	UIS.InputChanged:Connect(function(i)
		if drag and (i.UserInputType == Enum.UserInputType.MouseMovement
		or i.UserInputType == Enum.UserInputType.Touch) then
			local delta = i.Position - startInput
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)

	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1
		or i.UserInputType == Enum.UserInputType.Touch then
			drag = false
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
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

local Main = Instance.new("Frame", gui)
Main.AnchorPoint = Vector2.new(0.5,0.5)
Main.Position = UDim2.fromScale(0.5,0.5)
Main.Size = UDim2.fromOffset(440,580)
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
Title.Text = "🔥 FPS OPTIMIZER PRO v1.9"
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
-- FUNÇÕES ORIGINAIS
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

createToggle("🚫 Pós-Processamento", function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled = false end
	end
end)

createToggle("☁️ Atmosphere OFF", function()
	local a = Lighting:FindFirstChildOfClass("Atmosphere")
	if a then a.Enabled = false end
end)

createToggle("🌌 Skybox OFF", function()
	local s = Lighting:FindFirstChildOfClass("Sky")
	if s then s.Parent = nil end
end)

createToggle("✨ Partículas OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then v.Enabled = false end
	end
end)

createToggle("🔥 Fire OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Fire") then v.Enabled = false end
	end
end)

createToggle("💨 Smoke OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Smoke") then v.Enabled = false end
	end
end)

createToggle("🧵 Trails OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then v.Enabled = false end
	end
end)

createToggle("🧱 Plastic Materials", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Material = Enum.Material.Plastic
			v.CastShadow = false
		end
	end
end)

createToggle("🖼️ Decals OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Decal") or v:IsA("Texture") then
			v.Transparency = 1
		end
	end
end)

createToggle("📦 Mesh Performance", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("MeshPart") then
			v.RenderFidelity = Enum.RenderFidelity.Performance
		end
	end
end)

createToggle("🧠 Animations OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Animator") then v.Parent = nil end
	end
end)

createToggle("🔇 Sounds OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Sound") then v.Volume = 0 end
	end
end)

--------------------------------------------------
-- FPS COUNTER
--------------------------------------------------
local fpsLabel = Instance.new("TextLabel", gui)
fpsLabel.AnchorPoint = Vector2.new(1,0)
fpsLabel.Position = UDim2.new(1,-10,0,10)
fpsLabel.Size = UDim2.fromOffset(120,28)
fpsLabel.BackgroundTransparency = 1
fpsLabel.TextColor3 = Color3.fromRGB(255,80,80)
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.TextSize = 14
fpsLabel.Text = "FPS: 0"
fpsLabel.Visible = false

local fpsOn, frames, last = false, 0, tick()
RunService.RenderStepped:Connect(function()
	if not fpsOn then return end
	frames += 1
	if tick() - last >= 1 then
		fpsLabel.Text = "FPS: "..frames
		frames = 0
		last = tick()
	end
end)

createToggle("📊 Mostrar FPS", function()
	fpsOn = true
	fpsLabel.Visible = true
end,function()
	fpsOn = false
	fpsLabel.Visible = false
end)

--------------------------------------------------
-- 🆕 MOBILE FPS UNLOCK
--------------------------------------------------
createToggle("📱 Mobile FPS Unlock", function()
	if setfpscap then setfpscap(0) end
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
