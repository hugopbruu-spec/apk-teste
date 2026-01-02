--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.3 PRO
 Tema: Red Neon / Black
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- DRAG
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local dragging, startPos, startInput
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = frame.Position
			startInput = i.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

--------------------------------------------------
-- ORIGINAL BACKUP
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Brightness = Lighting.Brightness,
	Effects = {},
	Parts = {},
	Decals = {},
	Particles = {},
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

--------------------------------------------------
-- SAVE ORIGINAL STATES
--------------------------------------------------
for _,v in ipairs(Lighting:GetChildren()) do
	if v:IsA("PostEffect") then
		Original.Effects[v] = v.Enabled
	end
end

for _,v in ipairs(workspace:GetDescendants()) do
	if v:IsA("BasePart") then
		Original.Parts[v] = {
			Material = v.Material,
			Reflectance = v.Reflectance,
			CastShadow = v.CastShadow
		}
	elseif v:IsA("Decal") or v:IsA("Texture") then
		Original.Decals[v] = v.Transparency
	elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
		Original.Particles[v] = v.Enabled
	end
end

--------------------------------------------------
-- GUI THEME
--------------------------------------------------
local RED = Color3.fromRGB(255,40,40)
local DARK = Color3.fromRGB(15,15,15)
local DARK2 = Color3.fromRGB(25,25,25)

--------------------------------------------------
-- MAIN GUI
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(360,560)
Main.Position = UDim2.new(0.5,-180,0.5,-280)
Main.BackgroundColor3 = DARK
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)
makeDraggable(Main)

local Stroke = Instance.new("UIStroke", Main)
Stroke.Color = RED
Stroke.Thickness = 2

---------------- HEADER ----------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,48)
Header.BackgroundColor3 = DARK2
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-60,1,0)
Title.Position = UDim2.new(0,10,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO"
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 16
Title.TextColor3 = RED
Title.BackgroundTransparency = 1

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(36,36)
Close.Position = UDim2.new(1,-42,0,6)
Close.Text = "X"
Close.Font = Enum.Font.GothamBlack
Close.TextSize = 16
Close.TextColor3 = RED
Close.BackgroundTransparency = 1
Close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

---------------- SCROLL ----------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,58)
Scroll.Size = UDim2.new(1,-20,1,-80)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.CanvasSize = UDim2.new()
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

--------------------------------------------------
-- TOGGLE CREATOR PRO
--------------------------------------------------
local function toggle(text,on,off)
	local state=false
	local b=Instance.new("TextButton",Scroll)
	b.Size=UDim2.new(1,0,0,38)
	b.Text=text.." [OFF]"
	b.Font=Enum.Font.GothamBold
	b.TextSize=14
	b.TextColor3=Color3.new(1,1,1)
	b.BackgroundColor3=DARK2
	Instance.new("UICorner",b)

	local stroke=Instance.new("UIStroke",b)
	stroke.Color=RED
	stroke.Thickness=1

	b.MouseButton1Click:Connect(function()
		state=not state
		if state then
			on()
			b.Text=text.." [ON]"
			b.BackgroundColor3=Color3.fromRGB(120,0,0)
		else
			off()
			b.Text=text.." [OFF]"
			b.BackgroundColor3=DARK2
		end
	end)
end

--------------------------------------------------
-- FEATURES
--------------------------------------------------

toggle("⚡ FPS Boost Extremo",function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end,function()
	settings().Rendering.QualityLevel = Original.Quality
end)

toggle("💡 Desativar Pós-Processamento",function()
	for v,_ in pairs(Original.Effects) do
		v.Enabled=false
	end
end,function()
	for v,state in pairs(Original.Effects) do
		v.Enabled=state
	end
end)

toggle("🧱 Materiais Ultra Leves",function()
	for part,data in pairs(Original.Parts) do
		part.Material = Enum.Material.Plastic
		part.Reflectance = 0
		part.CastShadow = false
	end
end,function()
	for part,data in pairs(Original.Parts) do
		part.Material = data.Material
		part.Reflectance = data.Reflectance
		part.CastShadow = data.CastShadow
	end
end)

toggle("🎇 Remover Partículas",function()
	for p,_ in pairs(Original.Particles) do
		p.Enabled=false
	end
end,function()
	for p,state in pairs(Original.Particles) do
		p.Enabled=state
	end
end)

toggle("🖼️ Remover Decals",function()
	for d,_ in pairs(Original.Decals) do
		d.Transparency=1
	end
end,function()
	for d,val in pairs(Original.Decals) do
		d.Transparency=val
	end
end)

toggle("🌊 Água Ultra Leve",function()
	Terrain.WaterWaveSize=0
	Terrain.WaterWaveSpeed=0
	Terrain.WaterTransparency=1
end,function()
	Terrain.WaterWaveSize=Original.Water.WaveSize
	Terrain.WaterWaveSpeed=Original.Water.WaveSpeed
	Terrain.WaterTransparency=Original.Water.Transparency
end)

toggle("🔥 MODO ULTRA PERFORMANCE",function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	Lighting.GlobalShadows=false
	Lighting.Brightness=1
end,function()
	settings().Rendering.QualityLevel = Original.Quality
	Lighting.GlobalShadows=Original.GlobalShadows
	Lighting.Brightness=Original.Brightness
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
