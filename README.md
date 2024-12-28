local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local PotionRandomizer = Instance.new("TextButton")
local CloseButton = Instance.new("TextButton")
local Dragging = false
local DragStart = nil
local StartPos = nil

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

CloseButton.Name = "CloseButton"
CloseButton.Parent = Title
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(0.9, 0, 0.1, 0)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14

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

local PotionRandomizerEnabled = false
local Items = {}
local RemoteEvent = ReplicatedStorage.RemoteEvent

-- Make GUI draggable
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

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and Dragging then
        local delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(
            StartPos.X.Scale, 
            StartPos.X.Offset + delta.X,
            StartPos.Y.Scale, 
            StartPos.Y.Offset + delta.Y
        )
    end
end)

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

PotionRandomizer.MouseButton1Click:Connect(function()
    PotionRandomizerEnabled = not PotionRandomizerEnabled
    PotionRandomizer.Text = "Potion Randomizer: " .. (PotionRandomizerEnabled and "ON" or "OFF")
end)

local function FindPlayerCauldron()
    for _, v in pairs(Workspace.PlayerCauldrons:GetDescendants()) do
        if v.ClassName == "TextLabel" and v.Text == "YOUR CAULDRON" then
            return v.Parent.Parent.Parent.Name
        end
    end
    return nil
end

spawn(function()
    while wait(0.1) do
        if not PotionRandomizerEnabled then continue end
        
        pcall(function()
            local playerName = FindPlayerCauldron()
            if not playerName then return end
            
            -- Collect items
            Items = {}
            for _, v in pairs(Workspace.Interactions[playerName]:GetChildren()) do
                if v.ClassName == "Model" then
                    table.insert(Items, v.Name)
                end
            end
            
            -- Add random ingredients
            for i, _ in pairs(Items) do
                local randomItem = Items[math.random(1, i)]
                local itemPath = Workspace.Interactions[playerName][randomItem]
                local cauldronPath = Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron
                
                RemoteEvent:FireServer("PickUpItem", itemPath)
                RemoteEvent:FireServer("FireAllClients", "WeldItemToHand", itemPath.Main.GripAttachment, Workspace[LocalPlayer.Name].RightHand.RightGripAttachment)
                RemoteEvent:FireServer("FireAllClients", "UnweldItemFromHand", itemPath.Main)
                RemoteEvent:FireServer("AddIngredientToCauldron", cauldronPath, itemPath)
                RemoteEvent:FireServer("FireAllClients", "EmitParticles", cauldronPath.Contents.ItemAdded, {Duration = 0.8})
            end
            
            wait(3)
            RemoteEvent:FireServer("AttemptBottlePotion", Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron)
            
            wait(3)
            for _, v in pairs(Workspace.Interactions:GetChildren()) do
                RemoteEvent:FireServer("PickUpPotion", v)
            end
            
            RemoteEvent:FireServer("AttemptDrainCauldron", Workspace.PlayerCauldrons[playerName].CauldronSet.Cauldron)
        end)
    end
end)
