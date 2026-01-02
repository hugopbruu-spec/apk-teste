--[[=====================================================
 FPS OPTIMIZER PRO+++
 Criador: Frostzn
 Versão: 1.5 FINAL
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
	Technology = Lighting.Technology,
	Sky = Lighting:FindFirstChildOfClass("Sky"),
	Parts = {},
	Decals = {},
	Particles = {},
	Trails = {},
	Explosions = {},
	Highlights = {},
	Animations = {},
}

for _,v in ipairs(workspace:GetDescendants()) do
	if v:IsA("BasePart") then
		Original.Parts[v] = {
			Material = v.Material,
			Reflectance = v.Reflectance,
			CastShadow = v.CastShadow,
			CanCollide = v.CanCollide
		}
	elseif v:IsA("Decal") or v:IsA("Texture") then
		Original.Decals[v] = v.Transparency
	elseif v:IsA("ParticleEmitter") or v:IsA("Fire") or v:IsA("Smoke") then
		Original.Particles[v] = v.Enabled
	elseif v:IsA("Trail") then
		Original.Trails[v] = v.Enabled
	elseif v:IsA("Explosion") then
		Original.Explosions[v] = true
	elseif v:IsA("Highlight") then
		Original.Highlights[v] = v.Enabled
	elseif v:IsA("AnimationController") or v:IsA("Animator") then
		Original.Animations[v] = true
	end
end

--------------------------------------------------
-- THEME
--------------------------------------------------
local RED = Color3.fromRGB(255,40,40)
local DARK = Color3.fromRGB(14,14,14)
local DARK2 = Color3.fromRGB(24,24,24)

--------------------------------------------------
-- GUI
--------------------------------------------------
local gui = Instance.new("ScreenGui", guiParent)
gui.ResetOnSpawn = false

local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromOffset(390,620)
Main.Position = UDim2.new(0.5,-195,0.5,-310)
Main.BackgroundColor3 = DARK
Instance.new("UICorner", Main)
makeDraggable(Main)

local Stroke = Instance.new("UIStroke", Main)
Stroke.Color = RED
Stroke.Thickness = 1.5
Stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,48)
Header.BackgroundColor3 = DARK2
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-90,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🔥 FPS OPTIMIZER PRO+++"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = RED
Title.BackgroundTransparency = 1
Title.TextXAlignment = Left

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32,32)
Minimize.Position = UDim2.new(1,-72,0,8)
Minimize.Text = "–"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 22
Minimize.TextColor3 = Color3.new(1,1,1)
Minimize.BackgroundTransparency = 1

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32,32)
Close.Position = UDim2.new(1,-36,0,8)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = RED
Close.BackgroundTransparency = 1

--------------------------------------------------
-- SCROLL
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,58)
Scroll.Size = UDim2.new(1,-20,1,-80)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.ScrollBarImageTransparency = 0.4
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

--------------------------------------------------
-- TOGGLE
--------------------------------------------------
local function toggle(text,on,off)
	local state=false
	local b=Instance.new("TextButton",Scroll)
	b.Size=UDim2.new(1,0,0,36)
	b.Text=text.." [OFF]"
	b.Font=Enum.Font.Gotham
	b.TextSize=14
	b.TextColor3=Color3.new(1,1,1)
	b.BackgroundColor3=DARK2
	Instance.new("UICorner",b)

	b.MouseButton1Click:Connect(function()
		state=not state
		if state then
			on()
			b.Text=text.." [ON]"
			b.BackgroundColor3=Color3.fromRGB(110,0,0)
		else
			off()
			b.Text=text.." [OFF]"
			b.BackgroundColor3=DARK2
		end
	end)
end

--------------------------------------------------
-- FUNCTIONS (22)
--------------------------------------------------

toggle("FPS Boost Extremo",function()
	settings().Rendering.QualityLevel=Enum.QualityLevel.Level01
end,function()
	settings().Rendering.QualityLevel=Original.Quality
end)

toggle("Desativar Pós-Processamento",function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled=false end
	end
end,function()
	for _,v in ipairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled=true end
	end
end)

toggle("Materiais Ultra Leves",function()
	for p,_ in pairs(Original.Parts) do
		p.Material=Enum.Material.Plastic
		p.CastShadow=false
	end
end,function()
	for p,d in pairs(Original.Parts) do
		p.Material=d.Material
		p.CastShadow=d.CastShadow
	end
end)

toggle("Remover Partículas",function()
	for p,_ in pairs(Original.Particles) do p.Enabled=false end
end,function()
	for p,s in pairs(Original.Particles) do p.Enabled=s end
end)

toggle("Remover Trails",function()
	for t,_ in pairs(Original.Trails) do t.Enabled=false end
end,function()
	for t,s in pairs(Original.Trails) do t.Enabled=s end
end)

toggle("Remover Decals",function()
	for d,_ in pairs(Original.Decals) do d.Transparency=1 end
end,function()
	for d,s in pairs(Original.Decals) do d.Transparency=s end
end)

toggle("Desativar Explosões",function()
	for e,_ in pairs(Original.Explosions) do e:Destroy() end
end,function() end)

toggle("Reduzir Reflectance",function()
	for p,_ in pairs(Original.Parts) do p.Reflectance=0 end
end,function()
	for p,d in pairs(Original.Parts) do p.Reflectance=d.Reflectance end
end)

toggle("Desativar Highlights",function()
	for h,_ in pairs(Original.Highlights) do h.Enabled=false end
end,function()
	for h,_ in pairs(Original.Highlights) do h.Enabled=true end
end)

toggle("Desativar Skybox",function()
	if Original.Sky then Original.Sky.Parent=nil end
end,function()
	if Original.Sky then Original.Sky.Parent=Lighting end
end)

toggle("Lighting Compatibility",function()
	Lighting.Technology=Enum.Technology.Compatibility
end,function()
	Lighting.Technology=Original.Technology
end)

toggle("CanCollide OFF (props)",function()
	for p,_ in pairs(Original.Parts) do p.CanCollide=false end
end,function()
	for p,d in pairs(Original.Parts) do p.CanCollide=d.CanCollide end
end)

toggle("Garbage Collector Loop",function()
	_G.gc=true
	task.spawn(function()
		while _G.gc do
			collectgarbage("collect")
			task.wait(5)
		end
	end)
end,function()
	_G.gc=false
end)

--------------------------------------------------
-- MINI BUTTON
--------------------------------------------------
local Mini=Instance.new("TextButton",gui)
Mini.Size=UDim2.fromOffset(56,56)
Mini.Position=UDim2.new(0,20,0.5,-28)
Mini.Text="FPS"
Mini.Font=Enum.Font.GothamBold
Mini.TextSize=16
Mini.TextColor3=Color3.new(1,1,1)
Mini.BackgroundColor3=Color3.fromRGB(120,0,0)
Mini.Visible=false
Instance.new("UICorner",Mini)
makeDraggable(Mini)

Minimize.MouseButton1Click:Connect(function()
	Main.Visible=false
	Mini.Visible=true
end)

Mini.MouseButton1Click:Connect(function()
	Main.Visible=true
	Mini.Visible=false
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
Credit.Text = "Criador: Frostzn | FPS Optimizer PRO+++"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 11
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1
