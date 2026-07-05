if not game:IsLoaded() then game.Loaded:Wait() end

local Player = game:GetService("Players").LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui", 10)
local RS = game:GetService("ReplicatedStorage")
local UIS = game:GetService("UserInputService")

if PlayerGui:FindFirstChild("CarFlipperUltimateFixedHub") then
	PlayerGui.CarFlipperUltimateFixedHub:Destroy()
end

local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "CarFlipperUltimateFixedHub"
ScreenGui.ResetOnSpawn = false

local Main = Instance.new("Frame", ScreenGui)
Main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Main.BorderColor3 = Color3.fromRGB(255, 255, 255)
Main.BorderSizePixel = 2
Main.Position = UDim2.new(0.2, 0, 0.2, 0)
Main.Size = UDim2.new(0, 250, 0, 240)
Main.Active, Main.Draggable = true, true

local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Title.BorderColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.SourceSansBold
Title.Text = "CAR FLIPPER PRO HUB"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 12

local function createButton(parent, text, pos, size, bg)
	local btn = Instance.new("TextButton", parent)
	btn.Size = size
	btn.Position = pos
	btn.BackgroundColor3 = bg or Color3.fromRGB(0, 0, 0)
	btn.BorderColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.Code
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Text = text
	btn.TextSize = 11
	return btn
end

createButton(ScreenGui, "ON / OFF", UDim2.new(0, 10, 0, 10), UDim2.new(0, 80, 0, 30)).MouseButton1Click:Connect(function() 
	Main.Visible = not Main.Visible 
end)

local BuyCarToggleBtn = createButton(Main, "[ BUY CAR 🔻 ]", UDim2.new(0.05, 0, 0, 50), UDim2.new(0.9, 0, 0, 35))
local FixAllBtn       = createButton(Main, "[ REPAIR ALL CARS ]", UDim2.new(0.05, 0, 0, 100), UDim2.new(0.9, 0, 0, 35))
local RollBtn         = createButton(Main, "[ ROLL CARD ]", UDim2.new(0.05, 0, 0, 150), UDim2.new(0.9, 0, 0, 35))

local FixCarEvent   = RS:WaitForChild("Remotes"):WaitForChild("Repair"):WaitForChild("FixCar")
local BuyCarEvent   = RS:WaitForChild("Remotes"):WaitForChild("Merchant"):WaitForChild("BuyCar")
local RollCarEvent  = RS:WaitForChild("Remotes"):WaitForChild("Junkyard"):WaitForChild("Roll")

local ScrollFrame = Instance.new("ScrollingFrame", ScreenGui)
ScrollFrame.Size = UDim2.new(0, 220, 0, 150)
ScrollFrame.Position = UDim2.new(0.2, 255, 0.2, 0)
ScrollFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ScrollFrame.BorderColor3 = Color3.fromRGB(255, 255, 255)
ScrollFrame.BorderSizePixel = 2
ScrollFrame.ScrollBarThickness = 6
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 240)
ScrollFrame.Visible = false

local function buyZoneRange(zoneName)
	if BuyCarEvent then
		for i = 1, 20 do
			BuyCarEvent:FireServer(zoneName, tostring(i))
			task.wait(0.02)
		end
	end
end

local zones = {"Junkyard", "LowTiers", "Auction", "Suburbs3", "Port"}
for i, zone in ipairs(zones) do
	createButton(ScrollFrame, i..". Buy "..zone.." (1-20)", UDim2.new(0.05, 0, 0, 10 + (i-1)*45), UDim2.new(0.9, 0, 0, 35), Color3.fromRGB(20, 20, 20)).MouseButton1Click:Connect(function() 
		buyZoneRange(zone) 
	end)
end

BuyCarToggleBtn.MouseButton1Click:Connect(function() ScrollFrame.Visible = not ScrollFrame.Visible end)
Main:GetPropertyChangedSignal("Visible"):Connect(function() if not Main.Visible then ScrollFrame.Visible = false end end)

FixAllBtn.MouseButton1Click:Connect(function()
	if FixCarEvent then
		for id = 1, 40 do
			for i = 1, 15 do FixCarEvent:FireServer(tostring(id)) end
			task.wait(0.02)
		end
	end
end)

RollBtn.MouseButton1Click:Connect(function()
	if RollCarEvent then
		for i = 1, 20 do
			RollCarEvent:FireServer(tostring(i))
			task.wait(0.02)
		end
	end
end)

UIS.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.LeftControl then
		Main.Visible = not Main.Visible
		if not Main.Visible then ScrollFrame.Visible = false end
	end
end)
