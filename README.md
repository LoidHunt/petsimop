local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local PotionRandomizer = Instance.new("TextButton")
local Dragging = false
local DragStart = nil
local StartPos = nil

-- Cache services
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- GUI Setup
ScreenGui.Parent = game.CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.3, 0, 0.3, 0)
MainFrame.Size = UDim2.new(0, 200, 0, 100)

Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Title.BorderSizePixel = 0
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Font = Enum.Font.SourceSansBold
Title.Text = "Wacky Wizards"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16

PotionRandomizer.Name = "PotionRandomizer"
PotionRandomizer.Parent = MainFrame
PotionRandomizer.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PotionRandomizer.BorderSizePixel = 0
PotionRandomizer.Position = UDim2.new(0.1, 0, 0.4, 0)
PotionRandomizer.Size = UDim2.new(0.8, 0, 0, 30)
PotionRandomizer.Font = Enum.Font.SourceSans
PotionRandomizer.Text = "Potion Randomizer: OFF"
PotionRandomizer.TextColor3 = Color3.fromRGB(255, 255, 255)
PotionRandomizer.TextSize = 14

-- Variables
local PotionRandomizerEnabled = false
local Dragging = false
local DragStart
local StartPos
local Items = {}
local RemoteEvent = ReplicatedStorage.RemoteEvent

-- Dragging functionality
local function UpdateDrag(input)
    if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(
            StartPos.X.Scale,
            StartPos.X.Offset + delta.X,
            StartPos.Y.Scale,
            StartPos.Y.Offset + delta.Y
        )
    end
end

Title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
    end
end)

Title.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = false
    end
end)

UserInputService.InputChanged:Connect(UpdateDrag)

-- Helper functions
local function FindPlayerCauldron()
    for _, v in ipairs(Workspace.PlayerCauldrons:GetDescendants()) do
        if v.ClassName == "TextLabel" and v.Text == "YOUR CAULDRON" then
            return v.Parent.Parent.Parent.Name
        end
    end
end

local function FireRemote(...)
    RemoteEvent:FireServer(...)
end

-- Main functionality
PotionRandomizer.MouseButton1Click:Connect(function()
    PotionRandomizerEnabled = not PotionRandomizerEnabled
    PotionRandomizer.Text = "Potion Randomizer: " .. (PotionRandomizerEnabled and "ON" or "OFF")
end)

-- Main loop
spawn(function()
    while wait(0.1) do
        if not PotionRandomizerEnabled then continue end
        
        pcall(function()
            local playerName = FindPlayerCauldron()
            if not playerName then return end
            
            -- Collect items
            Items = {}
            for _, v in ipairs(Workspace.Interactions[playerName]:GetChildren()) do
                if v.ClassName == "Model" then
                    table.insert(Items, v.Name)
                end
            end
            
            -- Add random ingredients
            for i, _ in ipairs(Items) do
                local randomItem = Items[math.random(1, i)]
                local itemPath = Workspace.Interactions[playerName][randomItem]
                local cauldronPath = Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron
                
                FireRemote("PickUpItem", itemPath)
                FireRemote("FireAllClients", "WeldItemToHand", itemPath.Main.GripAttachment, Workspace[LocalPlayer.Name].RightHand.RightGripAttachment)
                FireRemote("FireAllClients", "UnweldItemFromHand", itemPath.Main)
                FireRemote("AddIngredientToCauldron", cauldronPath, itemPath)
                FireRemote("FireAllClients", "EmitParticles", cauldronPath.Contents.ItemAdded, {Duration = 0.8})
            end
            
            wait(3)
            
            -- Bottle potion
            FireRemote("AttemptBottlePotion", Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron)
            
            wait(3)
            
            -- Pickup and drain
            for _, v in ipairs(Workspace.Interactions:GetChildren()) do
                FireRemote("PickUpPotion", v)
            end
            
            FireRemote("AttemptDrainCauldron", Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron)
        end)
    end
end)
