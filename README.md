# Roblox
Roblox speed script
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SpeedGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Основной прямоугольный GUI
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,200,0,120)
frame.Position = UDim2.new(0,30,0,30)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0.2
frame.Parent = screenGui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0,10)
frameCorner.Parent = frame

-- Drag GUI
local draggingWindow = false
local dragStart = Vector2.new()
local startPos = UDim2.new()
local targetPos = frame.Position
local velocity = Vector2.new()

-- Заголовок RGB
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-50,0,20)
title.Position = UDim2.new(0,5,0,5)
title.BackgroundTransparency = 1
title.Text = "Roblox Egor by Donronx"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

RunService.RenderStepped:Connect(function()
	local t = tick()
	title.TextColor3 = Color3.new(math.sin(t*2)*0.5+0.5, math.sin(t*2+2)*0.5+0.5, math.sin(t*2+4)*0.5+0.5)
end)

title.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		draggingWindow = true
		dragStart = input.Position
		startPos = frame.Position
	end
end)

title.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		draggingWindow = false
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if draggingWindow and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		targetPos = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

RunService.RenderStepped:Connect(function()
	local currentPos = Vector2.new(frame.Position.X.Offset, frame.Position.Y.Offset)
	local targetVector = Vector2.new(targetPos.X.Offset, targetPos.Y.Offset)
	local distance = targetVector - currentPos
	velocity = velocity + distance * 0.3
	velocity = velocity * 0.75
	local newPos = currentPos + velocity
	frame.Position = UDim2.new(frame.Position.X.Scale, newPos.X, frame.Position.Y.Scale, newPos.Y)
end)

-- Ползунок скорости
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(1,-10,0,25)
speedLabel.Position = UDim2.new(0,5,0,40)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed: "..humanoid.WalkSpeed
speedLabel.TextColor3 = Color3.new(1,1,1)
speedLabel.Font = Enum.Font.GothamBold
speedLabel.TextScaled = true
speedLabel.Parent = frame

local sliderBack = Instance.new("Frame")
sliderBack.Size = UDim2.new(1,-20,0,15)
sliderBack.Position = UDim2.new(0,10,0,75)
sliderBack.BackgroundColor3 = Color3.fromRGB(60,60,60)
sliderBack.BorderSizePixel = 0
sliderBack.Parent = frame

local sliderCorner = Instance.new("UICorner")
sliderCorner.CornerRadius = UDim.new(0,10)
sliderCorner.Parent = sliderBack

local knob = Instance.new("Frame")
knob.Size = UDim2.new(0,20,1,0)
knob.Position = UDim2.new(0,0,0,0)
knob.BackgroundColor3 = Color3.fromRGB(200,200,200)
knob.BorderSizePixel = 0
knob.Parent = sliderBack

local knobCorner = Instance.new("UICorner")
knobCorner.CornerRadius = UDim.new(0,10)
knobCorner.Parent = knob

local draggingKnob = false

local function updateSpeed(inputX)
	local sliderAbsPos = sliderBack.AbsolutePosition.X
	local sliderAbsSize = sliderBack.AbsoluteSize.X
	local relativeX = math.clamp(inputX - sliderAbsPos, 0, sliderAbsSize - knob.AbsoluteSize.X)
	knob.Position = UDim2.new(0, relativeX, 0, 0)
	local ratio = relativeX / (sliderAbsSize - knob.AbsoluteSize.X)
	local newSpeed = math.floor(1 + ratio * 29)
	humanoid.WalkSpeed = newSpeed
	speedLabel.Text = "Speed: "..newSpeed
end

knob.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		draggingKnob = true
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) and draggingKnob then
		draggingKnob = false
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if draggingKnob and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		updateSpeed(input.Position.X)
	end
end)

-- Кнопка-свертыватель
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0,25,0,20)
toggleButton.Position = UDim2.new(1,-30,0,5)
toggleButton.BackgroundColor3 = Color3.fromRGB(80,80,80)
toggleButton.Text = ""
toggleButton.Parent = frame

local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0,5)
buttonCorner.Parent = toggleButton

local arrow = Instance.new("TextLabel")
arrow.Size = UDim2.new(1,0,1,0)
arrow.BackgroundTransparency = 1
arrow.Text = "▼"
arrow.Font = Enum.Font.GothamBold
arrow.TextColor3 = Color3.new(1,1,1)
arrow.TextScaled = true
arrow.Parent = toggleButton

local guiExpanded = true
local animationSpeed = 0.2
local targetRotation = 0
local currentRotation = 0
local bounceAmplitude = 5
local bounceSpeed = 6

toggleButton.MouseButton1Click:Connect(function()
	guiExpanded = not guiExpanded
	targetRotation = targetRotation + math.pi
	speedLabel.Visible = guiExpanded
	sliderBack.Visible = guiExpanded
	knob.Visible = guiExpanded
end)

RunService.RenderStepped:Connect(function()
	local targetHeight = guiExpanded and 120 or 30 -- ровно под заголовок и кнопку
	local currentHeight = frame.Size.Y.Offset
	local newHeight = currentHeight + (targetHeight - currentHeight) * animationSpeed
	frame.Size = UDim2.new(frame.Size.X.Scale, frame.Size.X.Offset, 0, newHeight)

	local visibleRatio = math.clamp((newHeight - 30)/90,0,1)
	speedLabel.TextTransparency = guiExpanded and (1 - visibleRatio) or 1
	sliderBack.BackgroundTransparency = guiExpanded and (1 - visibleRatio) or 1
	knob.BackgroundTransparency = guiExpanded and (1 - visibleRatio) or 1

	-- вращение стрелки
	currentRotation = currentRotation + (targetRotation - currentRotation) * 0.2
	arrow.Rotation = math.deg(currentRotation)

	-- подпрыгивание стрелки
	local bounce = math.sin(tick()*bounceSpeed) * bounceAmplitude * (targetRotation-currentRotation)/math.pi
	arrow.Position = UDim2.new(0,0,0,bounce)
end)
