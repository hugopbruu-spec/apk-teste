--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 2.0 STABLE MOBILE+
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
}

--------------------------------------------------
-- GUI BASE
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

--------------------------------------------------
-- FPS COUNTER
--------------------------------------------------
local fpsLabel = Instance.new("TextLabel", gui)
fpsLabel.AnchorPoint = Vector2.new(1,0)
fpsLabel.Position = UDim2.new(1,-10,0,10)
fpsLabel.Size = UDim2.fromOffset(130,28)
fpsLabel.BackgroundTransparency = 1
fpsLabel.TextColor3 = Color3.fromRGB(255,80,80)
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.TextSize = 14
fpsLabel.Text = "FPS: 0"
fpsLabel.Visible = false

local fpsEnabled, frames, lastTick = false, 0, tick()
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
Title.Text = "🔥 FPS OPTIMIZER PRO v2.0"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(200,40,40)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

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
-- NOVAS 15 FUNÇÕES ADICIONAIS
--------------------------------------------------

createToggle("📱 Mobile FPS Unlock", function()
	pcall(function()
		if setfpscap then
			setfpscap(0)
		end
	end)
end)

createToggle("🧊 Desativar Beams", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Beam") then v.Enabled = false end
	end
end)

createToggle("💡 Desativar PointLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("PointLight") then v.Enabled = false end
	end
end)

createToggle("🔦 Desativar SpotLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SpotLight") then v.Enabled = false end
	end
end)

createToggle("🪟 Desativar SurfaceLights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SurfaceLight") then v.Enabled = false end
	end
end)

createToggle("🧽 Remover SurfaceAppearance", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("SurfaceAppearance") then v:Destroy() end
	end
end)

createToggle("🧊 Desativar Highlights", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Highlight") then v.Enabled = false end
	end
end)

createToggle("📉 Reduzir Particle Rate", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then
			v.Rate = math.max(1, v.Rate * 0.2)
		end
	end
end)

createToggle("🧵 Reduzir Trail Lifetime", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then
			v.Lifetime = NumberRange.new(0.1)
		end
	end
end)

createToggle("🌊 Otimizar Água", function()
	if Terrain then
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterReflectance = 0
		Terrain.WaterTransparency = 1
	end
end)

createToggle("⚙️ Physics Throttle OFF", function()
	settings().Physics.AllowSleep = true
end)

createToggle("🧍 Simplificar Humanoids", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("Humanoid") then
			v:SetStateEnabled(Enum.HumanoidStateType.Climbing,false)
			v:SetStateEnabled(Enum.HumanoidStateType.Swimming,false)
		end
	end
end)

createToggle("🧹 Limpar Invisíveis", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and v.Transparency >= 1 then
			v.CastShadow = false
		end
	end
end)

createToggle("📦 Forçar LOD Baixo", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("MeshPart") then
			v.RenderFidelity = Enum.RenderFidelity.Performance
		end
	end
end)

createToggle("🧠 Animações Extra OFF", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("AnimationController") then
			v.Parent = nil
		end
	end
end)

--------------------------------------------------
-- FPS TOGGLE
--------------------------------------------------
createToggle("📊 Mostrar FPS", function()
	fpsEnabled = true
	fpsLabel.Visible = true
end,function()
	fpsEnabled = false
	fpsLabel.Visible = false
end)
