--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: 1.6 FINAL
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

--------------------------------------------------
-- DRAG
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	local dragging,startPos,startInput
	frame.InputBegan:Connect(function(i)
		if i.UserInputType==Enum.UserInputType.MouseButton1 then
			dragging=true
			startPos=frame.Position
			startInput=i.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType==Enum.UserInputType.MouseMovement then
			local d=i.Position-startInput
			frame.Position=UDim2.new(
				startPos.X.Scale,startPos.X.Offset+d.X,
				startPos.Y.Scale,startPos.Y.Offset+d.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType==Enum.UserInputType.MouseButton1 then
			dragging=false
		end
	end)
end

--------------------------------------------------
-- BACKUP ORIGINAL
--------------------------------------------------
local Original={
	Quality=settings().Rendering.QualityLevel,
	GlobalShadows=Lighting.GlobalShadows,
	Brightness=Lighting.Brightness,
	Technology=Lighting.Technology,
	FOV=camera.FieldOfView,
	Sky=Lighting:FindFirstChildOfClass("Sky"),
	Parts={}, Decals={}, Particles={}, Trails={}, Fires={}, Smokes={}, Meshes={}, Highlights={}
}

for _,v in ipairs(workspace:GetDescendants()) do
	if v:IsA("BasePart") then
		Original.Parts[v]={Material=v.Material,CastShadow=v.CastShadow,Reflectance=v.Reflectance,CanCollide=v.CanCollide}
	elseif v:IsA("Decal") or v:IsA("Texture") then
		Original.Decals[v]=v.Transparency
	elseif v:IsA("ParticleEmitter") then
		Original.Particles[v]=v.Enabled
	elseif v:IsA("Trail") then
		Original.Trails[v]=v.Enabled
	elseif v:IsA("Fire") then
		Original.Fires[v]=v.Enabled
	elseif v:IsA("Smoke") then
		Original.Smokes[v]=v.Enabled
	elseif v:IsA("MeshPart") then
		Original.Meshes[v]=v.RenderFidelity
	elseif v:IsA("Highlight") then
		Original.Highlights[v]=v.Enabled
	end
end

--------------------------------------------------
-- THEME
--------------------------------------------------
local RED=Color3.fromRGB(220,40,40)
local DARK=Color3.fromRGB(18,18,18)
local DARK2=Color3.fromRGB(30,30,30)

--------------------------------------------------
-- GUI
--------------------------------------------------
local gui=Instance.new("ScreenGui",guiParent)
gui.ResetOnSpawn=false

local Main=Instance.new("Frame",gui)
Main.Size=UDim2.fromOffset(400,560)
Main.Position=UDim2.new(0.5,-200,0.5,-280)
Main.BackgroundColor3=DARK
Instance.new("UICorner",Main)
makeDraggable(Main)

---------------- HEADER ----------------
local Header=Instance.new("Frame",Main)
Header.Size=UDim2.new(1,0,0,46)
Header.BackgroundColor3=DARK2
Instance.new("UICorner",Header)

local Title=Instance.new("TextLabel",Header)
Title.Size=UDim2.new(1,-90,1,0)
Title.Position=UDim2.new(0,12,0,0)
Title.Text="🔥 FPS OPTIMIZER PRO v1.6"
Title.Font=Enum.Font.GothamBold
Title.TextSize=16
Title.TextColor3=RED
Title.BackgroundTransparency=1
Title.TextXAlignment=Left

local Minimize=Instance.new("TextButton",Header)
Minimize.Size=UDim2.fromOffset(32,32)
Minimize.Position=UDim2.new(1,-72,0,7)
Minimize.Text="–"
Minimize.Font=Enum.Font.GothamBold
Minimize.TextSize=22
Minimize.BackgroundTransparency=1
Minimize.TextColor3=Color3.new(1,1,1)

local Close=Instance.new("TextButton",Header)
Close.Size=UDim2.fromOffset(32,32)
Close.Position=UDim2.new(1,-36,0,7)
Close.Text="X"
Close.Font=Enum.Font.GothamBold
Close.TextSize=16
Close.TextColor3=RED
Close.BackgroundTransparency=1

---------------- SCROLL ----------------
local Scroll=Instance.new("ScrollingFrame",Main)
Scroll.Position=UDim2.new(0,10,0,56)
Scroll.Size=UDim2.new(1,-20,1,-90)
Scroll.CanvasSize=UDim2.new()
Scroll.ScrollBarImageTransparency=0.4
Scroll.BackgroundTransparency=1

local Layout=Instance.new("UIListLayout",Scroll)
Layout.Padding=UDim.new(0,8)
Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize=UDim2.new(0,0,0,Layout.AbsoluteContentSize.Y+10)
end)

--------------------------------------------------
-- TOGGLE CREATOR
--------------------------------------------------
local function toggle(text,on,off)
	local state=false
	local b=Instance.new("TextButton",Scroll)
	b.Size=UDim2.new(1,0,0,38)
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
			b.BackgroundColor3=Color3.fromRGB(120,0,0)
		else
			if off then off() end
			b.Text=text.." [OFF]"
			b.BackgroundColor3=DARK2
		end
	end)
end

--------------------------------------------------
-- GARBAGE COLLECTOR FIX
--------------------------------------------------
local gcRunning=false
local function startGC()
	gcRunning=true
	task.spawn(function()
		while gcRunning do
			collectgarbage("collect")
			task.wait(5)
		end
	end)
end
local function stopGC() gcRunning=false end

--------------------------------------------------
-- FUNCTIONS (26)
--------------------------------------------------

toggle("FPS Boost",function()
	settings().Rendering.QualityLevel=Enum.QualityLevel.Level01
end,function()
	settings().Rendering.QualityLevel=Original.Quality
end)

toggle("Desativar Sombras",function()
	Lighting.GlobalShadows=false
end,function()
	Lighting.GlobalShadows=Original.GlobalShadows
end)

toggle("Materiais Leves",function()
	for p,_ in pairs(Original.Parts) do p.Material=Enum.Material.Plastic p.CastShadow=false end
end,function()
	for p,d in pairs(Original.Parts) do p.Material=d.Material p.CastShadow=d.CastShadow end
end)

toggle("Remover Decals",function()
	for d,_ in pairs(Original.Decals) do d.Transparency=1 end
end,function()
	for d,v in pairs(Original.Decals) do d.Transparency=v end
end)

toggle("Remover Partículas",function()
	for p,_ in pairs(Original.Particles) do p.Enabled=false end
end,function()
	for p,v in pairs(Original.Particles) do p.Enabled=v end
end)

toggle("Reduzir Água",function()
	if Terrain then Terrain.WaterWaveSize=0 Terrain.WaterWaveSpeed=0 Terrain.WaterTransparency=1 end
end)

toggle("Reduzir Reflectance",function()
	for p,_ in pairs(Original.Parts) do p.Reflectance=0 end
end,function()
	for p,d in pairs(Original.Parts) do p.Reflectance=d.Reflectance end
end)

toggle("Garbage Collector",startGC,stopGC)

-- NOVAS (18)

toggle("Desativar Pós-Processamento",function()
	for _,v in ipairs(Lighting:GetChildren()) do if v:IsA("PostEffect") then v.Enabled=false end end
end,function()
	for _,v in ipairs(Lighting:GetChildren()) do if v:IsA("PostEffect") then v.Enabled=true end end
end)

toggle("Desativar Atmosphere",function()
	local a=Lighting:FindFirstChildOfClass("Atmosphere")
	if a then a.Enabled=false end
end,function()
	local a=Lighting:FindFirstChildOfClass("Atmosphere")
	if a then a.Enabled=true end
end)

toggle("Simplificar MeshParts",function()
	for m,_ in pairs(Original.Meshes) do m.RenderFidelity=Enum.RenderFidelity.Performance end
end,function()
	for m,v in pairs(Original.Meshes) do m.RenderFidelity=v end
end)

toggle("Desativar Trails",function()
	for t,_ in pairs(Original.Trails) do t.Enabled=false end
end,function()
	for t,v in pairs(Original.Trails) do t.Enabled=v end
end)

toggle("Desativar Fire",function()
	for f,_ in pairs(Original.Fires) do f.Enabled=false end
end,function()
	for f,v in pairs(Original.Fires) do f.Enabled=v end
end)

toggle("Desativar Smoke",function()
	for s,_ in pairs(Original.Smokes) do s.Enabled=false end
end,function()
	for s,v in pairs(Original.Smokes) do s.Enabled=v end
end)

toggle("Lighting Compatibility",function()
	Lighting.Technology=Enum.Technology.Compatibility
end,function()
	Lighting.Technology=Original.Technology
end)

toggle("Desativar Skybox",function()
	if Original.Sky then Original.Sky.Parent=nil end
end,function()
	if Original.Sky then Original.Sky.Parent=Lighting end
end)

toggle("Desativar Highlights",function()
	for h,_ in pairs(Original.Highlights) do h.Enabled=false end
end,function()
	for h,v in pairs(Original.Highlights) do h.Enabled=v end
end)

toggle("Reduzir FOV",function()
	camera.FieldOfView=60
end,function()
	camera.FieldOfView=Original.FOV
end)

toggle("CanCollide OFF",function()
	for p,_ in pairs(Original.Parts) do p.CanCollide=false end
end,function()
	for p,d in pairs(Original.Parts) do p.CanCollide=d.CanCollide end
end)

toggle("Ultra Performance",function()
	settings().Rendering.QualityLevel=Enum.QualityLevel.Level01
	Lighting.GlobalShadows=false
	Lighting.Brightness=1
end,function()
	settings().Rendering.QualityLevel=Original.Quality
	Lighting.GlobalShadows=Original.GlobalShadows
	Lighting.Brightness=Original.Brightness
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
 
