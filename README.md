--[[=====================================================
 FPS OPTIMIZER PRO
 Criador: Frostzn
 Versão: FINAL STABLE (PC + MOBILE)
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

---------------- GUI BASE ----------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FPS_OPTIMIZER_PRO"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = player:WaitForChild("PlayerGui")

---------------- MAIN FRAME ----------------
local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.fromOffset(460, 560)
Main.Position = UDim2.fromScale(0.5, 0.5)
Main.AnchorPoint = Vector2.new(0.5, 0.5)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)

---------------- DRAG ----------------
do
	local dragging, dragStart, startPos
	Main.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = i.Position
			startPos = Main.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
			local delta = i.Position - dragStart
			Main.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)
end

---------------- HEADER ----------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,45)
Header.BackgroundColor3 = Color3.fromRGB(25,25,25)
Header.BorderSizePixel = 0
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Text = "🔥 FPS OPTIMIZER PRO"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(220,60,60)
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1,-120,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.TextXAlignment = Left

---------------- BUTTONS ----------------
local Minimize = Instance.new("TextButton", Header)
Minimize.Text = "_"
Minimize.Size = UDim2.fromOffset(36,28)
Minimize.Position = UDim2.new(1,-80,0,8)
Minimize.BackgroundColor3 = Color3.fromRGB(40,40,40)
Minimize.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Minimize)

local Close = Instance.new("TextButton", Header)
Close.Text = "X"
Close.Size = UDim2.fromOffset(36,28)
Close.Position = UDim2.new(1,-40,0,8)
Close.BackgroundColor3 = Color3.fromRGB(120,0,0)
Close.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Close)

Close.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)

---------------- SCROLL ----------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,55)
Scroll.Size = UDim2.new(1,-20,1,-65)
Scroll.ScrollBarThickness = 6
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,6)

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	Scroll.CanvasSize = UDim2.new(0,0,0,Layout.AbsoluteContentSize.Y + 10)
end)

---------------- TOGGLE CREATOR ----------------
local function Toggle(text, func)
	local state = false
	local b = Instance.new("TextButton", Scroll)
	b.Size = UDim2.new(1,0,0,36)
	b.Text = text.." [OFF]"
	b.Font = Enum.Font.Gotham
	b.TextSize = 14
	b.TextColor3 = Color3.new(1,1,1)
	b.BackgroundColor3 = Color3.fromRGB(32,32,32)
	b.BorderSizePixel = 0
	Instance.new("UICorner", b)

	b.MouseButton1Click:Connect(function()
		state = not state
		if state then
			pcall(func)
			b.Text = text.." [ON]"
			b.BackgroundColor3 = Color3.fromRGB(120,0,0)
		else
			b.Text = text.." [OFF]"
			b.BackgroundColor3 = Color3.fromRGB(32,32,32)
		end
	end)
end

--------------------------------------------------
-- FUNÇÕES ANTIGAS (PRESERVADAS)
--------------------------------------------------
Toggle("Low Graphics", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end)

Toggle("Disable Shadows", function()
	Lighting.GlobalShadows = false
end)

Toggle("Remove Particles", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") then v.Enabled = false end
	end
end)

Toggle("Remove Decals", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("Decal") or v:IsA("Texture") then v:Destroy() end
	end
end)

--------------------------------------------------
-- FUNÇÕES NOVAS (ADICIONADAS, NÃO ALTERAM AS ANTIGAS)
--------------------------------------------------
Toggle("Mobile FPS Unlock", function()
	if setfpscap then setfpscap(0) end
end)

Toggle("Disable Beams", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("Beam") then v.Enabled = false end
	end
end)

Toggle("Disable Highlights", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("Highlight") then v.Enabled = false end
	end
end)

Toggle("Disable Trails", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("Trail") then v.Enabled = false end
	end
end)

Toggle("Deep Client Cleanup", function()
	collectgarbage("collect")
end)
