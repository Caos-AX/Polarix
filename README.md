local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local flying = false
local noclip = false -- Nova variável para Noclip
local speed = 100 

-- GUI PRINCIPAL
local gui = Instance.new("ScreenGui")
gui.Name = "FlyHeroCompact"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player:WaitForChild("PlayerGui")

-- BOTÃO DO MENU
local menuButton = Instance.new("ImageButton")
menuButton.Size = UDim2.new(0, 40, 0, 40)
menuButton.Position = UDim2.new(0, 50, 0, 200)
menuButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
menuButton.Image = "rbxassetid://7072718381" 
menuButton.Active = true
menuButton.Parent = gui

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(1, 0)
menuCorner.Parent = menuButton

-- PAINEL DE CONTROLE (Aumentado para 200 de altura)
local panel = Instance.new("Frame")
panel.Size = UDim2.new(0, 120, 0, 200) 
panel.Position = UDim2.new(0, 100, 0, 200)
panel.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
panel.BackgroundTransparency = 0.2
panel.Visible = false 
panel.Parent = gui

local panelCorner = Instance.new("UICorner")
panelCorner.Parent = panel

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 25)
title.Text = "FLY & CLIP V18"
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 12
title.Parent = panel

-- BOTÃO FLY
local flyToggle = Instance.new("TextButton")
flyToggle.Size = UDim2.new(0, 100, 0, 30)
flyToggle.Position = UDim2.new(0.5, -50, 0, 30)
flyToggle.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
flyToggle.Text = "FLY: OFF"
flyToggle.TextColor3 = Color3.new(1, 1, 1)
flyToggle.Font = Enum.Font.SourceSansBold
flyToggle.Parent = panel

local flyCorner = Instance.new("UICorner")
flyCorner.Parent = flyToggle

-- BOTÃO NOCLIP (Novo)
local noclipToggle = Instance.new("TextButton")
noclipToggle.Size = UDim2.new(0, 100, 0, 30)
noclipToggle.Position = UDim2.new(0.5, -50, 0, 65)
noclipToggle.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
noclipToggle.Text = "CLIP: OFF"
noclipToggle.TextColor3 = Color3.new(1, 1, 1)
noclipToggle.Font = Enum.Font.SourceSansBold
noclipToggle.Parent = panel

local clipCorner = Instance.new("UICorner")
clipCorner.Parent = noclipToggle

-- AJUSTE DE VELOCIDADE
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(1, 0, 0, 20)
speedLabel.Position = UDim2.new(0, 0, 0, 105)
speedLabel.Text = "V: " .. speed
speedLabel.TextColor3 = Color3.new(1, 1, 1)
speedLabel.BackgroundTransparency = 1
speedLabel.Font = Enum.Font.SourceSans
speedLabel.TextSize = 14
speedLabel.Parent = panel

local btnPlus = Instance.new("TextButton")
btnPlus.Size = UDim2.new(0, 35, 0, 35)
btnPlus.Position = UDim2.new(0.75, -17, 0, 130)
btnPlus.Text = "+"
btnPlus.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
btnPlus.TextColor3 = Color3.new(1, 1, 1)
btnPlus.Parent = panel

local btnMinus = Instance.new("TextButton")
btnMinus.Size = UDim2.new(0, 35, 0, 35)
btnMinus.Position = UDim2.new(0.25, -17, 0, 130)
btnMinus.Text = "-"
btnMinus.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
btnMinus.TextColor3 = Color3.new(1, 1, 1)
btnMinus.Parent = panel

-- LÓGICA DE ARRASTAR
local dStart, sPos, dragging, moved
menuButton.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dStart = i.Position
		sPos = menuButton.Position
		moved = false
	end
end)

UserInputService.InputChanged:Connect(function(i)
	if dragging and (i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement) then
		local delta = i.Position - dStart
		if delta.Magnitude > 5 then
			moved = true
			menuButton.Position = UDim2.new(sPos.X.Scale, sPos.X.Offset + delta.X, sPos.Y.Scale, sPos.Y.Offset + delta.Y)
			panel.Position = UDim2.new(sPos.X.Scale, sPos.X.Offset + delta.X + 50, sPos.Y.Scale, sPos.Y.Offset + delta.Y)
		end
	end
end)

UserInputService.InputEnded:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

menuButton.Activated:Connect(function()
	if not moved then panel.Visible = not panel.Visible end
end)

-- LÓGICA DO NOCLIP
noclipToggle.Activated:Connect(function()
	noclip = not noclip
	if noclip then
		noclipToggle.Text = "CLIP: ON"
		noclipToggle.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
	else
		noclipToggle.Text = "CLIP: OFF"
		noclipToggle.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
	end
end)

-- SISTEMA DE VOO
local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(0,0,0)

flyToggle.Activated:Connect(function()
	flying = not flying
	local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
	if flying then
		flyToggle.Text = "FLY: ON"
		flyToggle.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
		if hum then hum.AutoRotate = false end
	else
		flyToggle.Text = "FLY: OFF"
		flyToggle.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
		bv.MaxForce = Vector3.new(0,0,0)
		bv.Parent = nil
		if hum then 
			hum.AutoRotate = true 
			hum:ChangeState(Enum.HumanoidStateType.GettingUp)
		end
	end
end)

-- VELOCIDADE
btnPlus.Activated:Connect(function()
	speed = math.min(speed + 25, 1000)
	speedLabel.Text = "V: " .. speed
end)

btnMinus.Activated:Connect(function()
	speed = math.max(speed - 25, 25)
	speedLabel.Text = "V: " .. speed
end)

-- LOOP PRINCIPAL (FLY + NOCLIP)
RunService.RenderStepped:Connect(function()
	local char = player.Character
	if not char then return end

	-- Lógica de Noclip (Atravessar paredes)
	if noclip then
		for _, part in pairs(char:GetDescendants()) do
			if part:IsA("BasePart") and part.CanCollide then
				part.CanCollide = false
			end
		end
	end

	-- Lógica de Voo (Mantive a sua original)
	if flying then
		local root = char:FindFirstChild("HumanoidRootPart")
		local hum = char:FindFirstChildOfClass("Humanoid")
		local cam = workspace.CurrentCamera
		
		if root and hum then
			bv.Parent = root
			bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
			hum:ChangeState(Enum.HumanoidStateType.Physics)
			local moveDir = hum.MoveDirection
			local camCF = cam.CFrame
			
			root.CFrame = CFrame.new(root.Position, root.Position + camCF.LookVector)
			
			if moveDir.Magnitude > 0 then
				local flatLook = Vector3.new(camCF.LookVector.X, 0, camCF.LookVector.Z).Unit
				local flatRight = Vector3.new(camCF.RightVector.X, 0, camCF.RightVector.Z).Unit
				local forwardAmount = moveDir:Dot(flatLook)
				local rightAmount = moveDir:Dot(flatRight)
				local direction = (camCF.LookVector * forwardAmount) + (camCF.RightVector * rightAmount)
				
				bv.Velocity = direction.Unit * speed
			else
				bv.Velocity = Vector3.zero
			end
			root.RotVelocity = Vector3.zero
		end
	end
end)
