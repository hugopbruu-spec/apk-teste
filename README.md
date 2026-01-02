--[[=====================================================
 FPS OPTIMIZER
 Criador: Frostzn
 Versão: 1.2 Beta
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- DRAG FUNCTION
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
-- ORIGINAL SETTINGS
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Specular = Lighting.EnvironmentSpecularScale,
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

--------------------------------------------------
-- KEYS SYSTEM
--------------------------------------------------
local ADMIN_KEY = "261120"
local MAX_USES = 3
local Keys = {}

math.randomseed(1337)
for i = 1,1000 do
	local key = "FPS-"..math.random(100000,999999).."-"..i
	Keys[key] = MAX_USES
end

--------------------------------------------------
-- LOADING SCREEN
--------------------------------------------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

local bg = Instance.new("Frame", LoadGui)
bg.Size = UDim2.fromScale(1,1)
bg.BackgroundColor3 = Color3.fromRGB(10,10,10)

local title = Instance.new("TextLabel", bg)
title.Size = UDim2.new(1,0,0,40)
title.Position = UDim2.new(0,0,0.43,0)
title.Text = "Carregando FPS Optimizer v1.2 Beta"
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1

local percent = Instance.new("TextLabel", bg)
percent.Size = UDim2.new(1,0,0,30)
percent.Position = UDim2.new(0,0,0.48,0)
percent.Text = "0%"
percent.Font = Enum.Font.Gotham
percent.TextSize = 16
percent.TextColor3 = Color3.fromRGB(200,200,200)
percent.BackgroundTransparency = 1

local barBG = Instance.new("Frame", bg)
barBG.Size = UDim2.new(0.4,0,0,12)
barBG.Position = UDim2.new(0.3,0,0.53,0)
barBG.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", barBG)

local bar = Instance.new("Frame", barBG)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", bar)

for i = 0,100 do
	percent.Text = i.."%"
	bar.Size = UDim2.new(i/100,0,1,0)
	task.wait(0.02)
end

LoadGui:Destroy()

--------------------------------------------------
-- FPS OPTIMIZER MENU
--------------------------------------------------
local function openFPSOptimizer()

	local ScreenGui = Instance.new("ScreenGui", guiParent)
	ScreenGui.ResetOnSpawn = false

	local Main = Instance.new("Frame", ScreenGui)
	Main.Size = UDim2.fromOffset(340,520)
	Main.Position = UDim2.new(0.5,-170,0.5,-260)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Main.BorderSizePixel = 0
	Instance.new("UICorner", Main)
	makeDraggable(Main)

	---------------- HEADER ----------------
	local Header = Instance.new("Frame", Main)
	Header.Size = UDim2.new(1,0,0,44)
	Header.BackgroundColor3 = Color3.fromRGB(22,22,22)
	Instance.new("UICorner", Header)

	local Title = Instance.new("TextLabel", Header)
	Title.Size = UDim2.new(1,-80,1,0)
	Title.Position = UDim2.new(0,10,0,0)
	Title.Text = "🚀 FPS OPTIMIZER | v1.2 Beta"
	Title.Font = Enum.Font.GothamBold
	Title.TextSize = 15
	Title.TextColor3 = Color3.fromRGB(0,255,120)
	Title.BackgroundTransparency = 1

	local Minimize = Instance.new("TextButton", Header)
	Minimize.Size = UDim2.fromOffset(30,30)
	Minimize.Position = UDim2.new(1,-64,0,7)
	Minimize.Text = "-"
	Minimize.Font = Enum.Font.GothamBold
	Minimize.TextSize = 22
	Minimize.BackgroundTransparency = 1
	Minimize.TextColor3 = Color3.new(1,1,1)

	local Close = Instance.new("TextButton", Header)
	Close.Size = UDim2.fromOffset(30,30)
	Close.Position = UDim2.new(1,-32,0,7)
	Close.Text = "X"
	Close.Font = Enum.Font.GothamBold
	Close.TextSize = 16
	Close.TextColor3 = Color3.fromRGB(255,80,80)
	Close.BackgroundTransparency = 1

	---------------- SCROLL ----------------
	local Scroll = Instance.new("ScrollingFrame", Main)
	Scroll.Position = UDim2.new(0,10,0,54)
	Scroll.Size = UDim2.new(1,-20,1,-90)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.ScrollBarImageTransparency = 0.4
	Scroll.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Scroll)
	Layout.Padding = UDim.new(0,8)

	---------------- TOGGLE CREATOR ----------------
	local function toggle(text,on,off,locked)
		local state=false
		local b=Instance.new("TextButton",Scroll)
		b.Size=UDim2.new(1,0,0,36)
		b.Text=locked and text or text.." [OFF]"
		b.Font=Enum.Font.Gotham
		b.TextSize=14
		b.TextColor3=Color3.new(1,1,1)
		b.BackgroundColor3=locked and Color3.fromRGB(70,70,70) or Color3.fromRGB(40,40,40)
		Instance.new("UICorner",b)

		b.MouseButton1Click:Connect(function()
			if locked then return end
			state=not state
			if state then
				on()
				b.Text=text.." [ON]"
				b.BackgroundColor3=Color3.fromRGB(0,160,90)
			else
				if off then off() end
				b.Text=text.." [OFF]"
				b.BackgroundColor3=Color3.fromRGB(40,40,40)
			end
		end)
	end

	---------------- FPS LIMIT ----------------
	local fpsConn
	local function fps120On()
		fpsConn = RunService.RenderStepped:Connect(function(dt)
			local target = 1/120
			if dt < target then
				task.wait(target - dt)
			end
		end)
	end
	local function fps120Off()
		if fpsConn then fpsConn:Disconnect() end
	end

	---------------- FPS COUNTER ----------------
	local FPSLabel = Instance.new("TextLabel", ScreenGui)
	FPSLabel.Size = UDim2.fromOffset(120,30)
	FPSLabel.Position = UDim2.new(1,-130,0,10)
	FPSLabel.Text = "FPS: 0"
	FPSLabel.Font = Enum.Font.GothamBold
	FPSLabel.TextSize = 16
	FPSLabel.TextColor3 = Color3.fromRGB(0,255,120)
	FPSLabel.BackgroundTransparency = 1
	FPSLabel.Visible = false

	local fpsEnabled=false
	local frames,last=0,tick()
	RunService.RenderStepped:Connect(function()
		if fpsEnabled then
			frames+=1
			if tick()-last>=1 then
				FPSLabel.Text="FPS: "..frames
				frames=0
				last=tick()
			end
		end
	end)

	---------------- TOGGLES ----------------
	toggle("⚡ FPS Boost",function()
		settings().Rendering.QualityLevel=Enum.QualityLevel.Level01
	end,function()
		settings().Rendering.QualityLevel=Original.Quality
	end)

	toggle("🎯 Limite de FPS: 120",fps120On,fps120Off)

	toggle("📊 Mostrar FPS em tempo real",function()
		fpsEnabled=true
		FPSLabel.Visible=true
	end,function()
		fpsEnabled=false
		FPSLabel.Visible=false
	end)

	toggle("🧱 Texturas Leves",function()
		for _,v in ipairs(workspace:GetDescendants()) do
			if v:IsA("BasePart") then v.CastShadow=false end
		end
	end,function()
		for _,v in ipairs(workspace:GetDescendants()) do
			if v:IsA("BasePart") then v.CastShadow=true end
		end
	end)

	toggle("💡 Otimizar Iluminação",function()
		Lighting.GlobalShadows=false
		Lighting.EnvironmentSpecularScale=0
	end,function()
		Lighting.GlobalShadows=Original.GlobalShadows
		Lighting.EnvironmentSpecularScale=Original.Specular
	end)

	toggle("🌊 Otimizar Água",function()
		Terrain.WaterWaveSize=0
		Terrain.WaterWaveSpeed=0
		Terrain.WaterTransparency=1
	end,function()
		Terrain.WaterWaveSize=Original.Water.WaveSize
		Terrain.WaterWaveSpeed=Original.Water.WaveSpeed
		Terrain.WaterTransparency=Original.Water.Transparency
	end)

	toggle("🔒 Mais funções em breve...",function()end,nil,true)

	---------------- MINI BUTTON ----------------
	local Mini=Instance.new("TextButton",ScreenGui)
	Mini.Size=UDim2.fromOffset(50,50)
	Mini.Position=UDim2.new(0,20,0.5,-25)
	Mini.Text="FPS"
	Mini.Font=Enum.Font.GothamBold
	Mini.TextSize=16
	Mini.TextColor3=Color3.new(1,1,1)
	Mini.BackgroundColor3=Color3.fromRGB(0,170,90)
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
		ScreenGui:Destroy()
	end)

	---------------- CREDIT ----------------
	local Credit = Instance.new("TextLabel", Main)
	Credit.Size = UDim2.new(1,0,0,20)
	Credit.Position = UDim2.new(0,0,1,-20)
	Credit.Text = "Criador: Frostzn"
	Credit.Font = Enum.Font.Gotham
	Credit.TextSize = 12
	Credit.TextColor3 = Color3.fromRGB(150,150,150)
	Credit.BackgroundTransparency = 1
end

--------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------
local function openAdminPanel()
	local gui=Instance.new("ScreenGui",guiParent)
	local f=Instance.new("Frame",gui)
	f.Size=UDim2.fromOffset(420,520)
	f.Position=UDim2.new(0.5,-210,0.5,-260)
	f.BackgroundColor3=Color3.fromRGB(18,18,18)
	Instance.new("UICorner",f)
	makeDraggable(f)

	local title=Instance.new("TextLabel",f)
	title.Size=UDim2.new(1,-40,0,40)
	title.Position=UDim2.new(0,10,0,0)
	title.Text="🔐 ADMIN PANEL"
	title.Font=Enum.Font.GothamBold
	title.TextSize=18
	title.TextColor3=Color3.fromRGB(0,255,120)
	title.BackgroundTransparency=1

	local close=Instance.new("TextButton",f)
	close.Size=UDim2.fromOffset(32,32)
	close.Position=UDim2.new(1,-36,0,4)
	close.Text="X"
	close.Font=Enum.Font.GothamBold
	close.TextSize=16
	close.TextColor3=Color3.fromRGB(255,80,80)
	close.BackgroundTransparency=1
	close.MouseButton1Click:Connect(function()
		gui:Destroy()
	end)

	local list=Instance.new("ScrollingFrame",f)
	list.Position=UDim2.new(0,10,0,46)
	list.Size=UDim2.new(1,-20,1,-56)
	list.AutomaticCanvasSize=Enum.AutomaticSize.Y
	list.BackgroundTransparency=1

	local lay=Instance.new("UIListLayout",list)
	lay.Padding=UDim.new(0,4)

	for k,v in pairs(Keys) do
		local t=Instance.new("TextLabel",list)
		t.Size=UDim2.new(1,0,0,22)
		t.Text=k.." | usos restantes: "..v
		t.Font=Enum.Font.Gotham
		t.TextSize=12
		t.TextColor3=Color3.new(1,1,1)
		t.BackgroundTransparency=1
	end
end

--------------------------------------------------
-- KEY MENU
--------------------------------------------------
local KeyGui=Instance.new("ScreenGui",guiParent)

local f=Instance.new("Frame",KeyGui)
f.Size=UDim2.fromOffset(320,200)
f.Position=UDim2.new(0.5,-160,0.5,-100)
f.BackgroundColor3=Color3.fromRGB(18,18,18)
Instance.new("UICorner",f)
makeDraggable(f)

local t=Instance.new("TextLabel",f)
t.Size=UDim2.new(1,0,0,36)
t.Text="🔑 INSIRA SUA KEY"
t.Font=Enum.Font.GothamBold
t.TextSize=16
t.TextColor3=Color3.fromRGB(0,255,120)
t.BackgroundTransparency=1

local box=Instance.new("TextBox",f)
box.Size=UDim2.new(0.9,0,0,36)
box.Position=UDim2.new(0.05,0,0,55)
box.PlaceholderText="FPS-XXXXXX"
box.Text=""
box.TextColor3=Color3.new(1,1,1)
box.BackgroundColor3=Color3.fromRGB(30,30,30)
Instance.new("UICorner",box)

local status=Instance.new("TextLabel",f)
status.Size=UDim2.new(1,0,0,20)
status.Position=UDim2.new(0,0,0,100)
status.Text=""
status.Font=Enum.Font.Gotham
status.TextSize=12
status.BackgroundTransparency=1

local btn=Instance.new("TextButton",f)
btn.Size=UDim2.new(0.5,0,0,32)
btn.Position=UDim2.new(0.25,0,1,-44)
btn.Text="Confirmar"
btn.Font=Enum.Font.GothamBold
btn.TextSize=14
btn.TextColor3=Color3.new(1,1,1)
btn.BackgroundColor3=Color3.fromRGB(0,170,90)
Instance.new("UICorner",btn)

btn.MouseButton1Click:Connect(function()
	local k=box.Text
	if k==ADMIN_KEY then
		KeyGui:Destroy()
		openAdminPanel()
	elseif Keys[k] and Keys[k]>0 then
		Keys[k]-=1
		status.Text="✅ Key válida! Usos restantes: "..Keys[k]
		status.TextColor3=Color3.fromRGB(0,255,120)
		task.wait(0.6)
		KeyGui:Destroy()
		openFPSOptimizer()
	else
		status.Text="❌ Key inválida ou expirada"
		status.TextColor3=Color3.fromRGB(255,80,80)
		box.Text=""
	end
end)
 
