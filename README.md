local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local isTeleporting = false
local moveSpeed = 300

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "bielExerc hub"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = PlayerGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 220)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -110)
MainFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 20)
MainCorner.Parent = MainFrame

local Gradient = Instance.new("UIGradient")
Gradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 25)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 0, 0))
})
Gradient.Rotation = 45
Gradient.Parent = MainFrame

local UIStroke = Instance.new("UIStroke")
UIStroke.Color = Color3.fromRGB(255, 255, 255)
UIStroke.Thickness = 2
UIStroke.Transparency = 0.2
UIStroke.Parent = MainFrame

local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(1, -20, 0, 30)
Title.Position = UDim2.new(0, 10, 0, 10)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.FredokaOne
Title.Text = "Xenon Safe Zone Area Tp"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
Title.TextScaled = true
Title.Parent = MainFrame

local SpeedContainer = Instance.new("Frame")
SpeedContainer.Name = "SpeedContainer"
SpeedContainer.Size = UDim2.new(0, 260, 0, 40)
SpeedContainer.Position = UDim2.new(0.5, -130, 0, 50)
SpeedContainer.BackgroundTransparency = 1
SpeedContainer.Parent = MainFrame

local SpeedTextBox = Instance.new("TextBox")
SpeedTextBox.Name = "SpeedTextBox"
SpeedTextBox.Size = UDim2.new(0, 150, 0, 40)
SpeedTextBox.Position = UDim2.new(0, 0, 0, 0)
SpeedTextBox.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
SpeedTextBox.Font = Enum.Font.FredokaOne
SpeedTextBox.PlaceholderText = "Bypass Speed"
SpeedTextBox.Text = tostring(moveSpeed)
SpeedTextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedTextBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
SpeedTextBox.TextSize = 14
SpeedTextBox.Parent = SpeedContainer

local SpeedBoxCorner = Instance.new("UICorner")
SpeedBoxCorner.CornerRadius = UDim.new(0, 12)
SpeedBoxCorner.Parent = SpeedTextBox

local SubmitSpeedButton = Instance.new("TextButton")
SubmitSpeedButton.Name = "SubmitSpeedButton"
SubmitSpeedButton.Size = UDim2.new(0, 100, 0, 40)
SubmitSpeedButton.Position = UDim2.new(0, 160, 0, 0)
SubmitSpeedButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
SubmitSpeedButton.Font = Enum.Font.FredokaOne
SubmitSpeedButton.Text = "Set Speed"
SubmitSpeedButton.TextColor3 = Color3.fromRGB(0, 0, 0)
SubmitSpeedButton.TextSize = 13
SubmitSpeedButton.AutoButtonColor = false
SubmitSpeedButton.Parent = SpeedContainer

local SubmitCorner = Instance.new("UICorner")
SubmitCorner.CornerRadius = UDim.new(0, 12)
SubmitCorner.Parent = SubmitSpeedButton

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "StatusLabel"
StatusLabel.Size = UDim2.new(1, -20, 0, 20)
StatusLabel.Position = UDim2.new(0, 10, 0, 100)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Font = Enum.Font.FredokaOne
StatusLabel.Text = "Bypass Speed: " .. moveSpeed .. " studs/s"
StatusLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
StatusLabel.TextSize = 12
StatusLabel.Parent = MainFrame

local TeleportButton = Instance.new("TextButton")
TeleportButton.Name = "TeleportButton"
TeleportButton.Size = UDim2.new(0, 260, 0, 50)
TeleportButton.Position = UDim2.new(0.5, -130, 0, 140)
TeleportButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TeleportButton.Font = Enum.Font.FredokaOne
TeleportButton.Text = "Bypass TP to Safe Zone"
TeleportButton.TextColor3 = Color3.fromRGB(0, 0, 0)
TeleportButton.TextSize = 15
TeleportButton.AutoButtonColor = false
TeleportButton.Parent = MainFrame

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(0, 20)
ButtonCorner.Parent = TeleportButton

-- ==========================================
-- SISTEMA DE MOVIMENTAÇÃO CORRIGIDO E SUAVE
-- ==========================================
local dragging = false
local dragStart = nil
local startPos = nil

MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		if dragging then
			local delta = input.Position - dragStart
			local targetPos = UDim2.new(
				startPos.X.Scale,
				startPos.X.Offset + delta.X,
				startPos.Y.Scale,
				startPos.Y.Offset + delta.Y
			)
			TweenService:Create(MainFrame, TweenInfo.new(0.08, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {
				Position = targetPos
			}):Play()
		end
	end
end)
-- ==========================================

SubmitSpeedButton.MouseButton1Click:Connect(function()
	local num = tonumber(SpeedTextBox.Text)
	if num and num > 0 then
		moveSpeed = num
		StatusLabel.Text = "Bypass Speed: " .. moveSpeed .. " studs/s"
	end
end)

local function findSafeZone()
	local strictPath = workspace:FindFirstChild("Game")
		and workspace.Game:FindFirstChild("Map")
		and workspace.Game.Map:FindFirstChild("Lobby")
		and workspace.Game.Map.Lobby:FindFirstChild("Floor")
		and workspace.Game.Map.Lobby.Floor:FindFirstChild("safe")
		
	if strictPath then return strictPath end

	for _, obj in ipairs(workspace:GetDescendants()) do
		if obj.Name == "safe" and obj:IsA("BasePart") then
			return obj
		end
	end

	return nil
end

local function bypassBypassTeleport()
	if isTeleporting then return end

	local character = LocalPlayer.Character
	if not character then return end

	local hrp = character:FindFirstChild("HumanoidRootPart")
	local humanoid = character:FindFirstChildOfClass("Humanoid")
	if not hrp or not humanoid then return end

	local safeZone = findSafeZone()
	if not safeZone then
		StatusLabel.Text = "Error: Safe zone part not found!"
		StatusLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
		return
	end

	isTeleporting = true
	StatusLabel.Text = "Bypassing anti-cheat..."
	StatusLabel.TextColor3 = Color3.fromRGB(255, 220, 100)

	local targetPos = safeZone.Position + Vector3.new(0, 3, 0)
	local distance = (targetPos - hrp.Position).Magnitude
	local totalTime = distance / moveSpeed

	humanoid:ChangeState(Enum.HumanoidStateType.Running)

	local tweenInfo = TweenInfo.new(
		totalTime,
		Enum.EasingStyle.Linear,
		Enum.EasingDirection.Out
	)

	local tpTween = TweenService:Create(hrp, tweenInfo, {CFrame = CFrame.new(targetPos)})
	tpTween:Play()

	local connection
	connection = game:GetService("RunService").Stepped:Connect(function()
		if not isTeleporting then
			connection:Disconnect()
			return
		end
		
		hrp.Velocity = Vector3.new(0, 0, 0)
		humanoid:ChangeState(Enum.HumanoidStateType.Running)
	end)

	tpTween.Completed:Wait()
	
	if connection then
		connection:Disconnect()
	end

	isTeleporting = false
	StatusLabel.Text = "Arrived Safely on Safe Zone."
	StatusLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
end

TeleportButton.MouseButton1Click:Connect(function()
	bypassBypassTeleport()
end)
