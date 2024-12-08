while wait() do
local args = {
    [1] = "Mouse1",
    [2] = "Down",
    [3] = CFrame.new(645.7730102539062, 65.71050262451172, 1046.96044921875, -0.980191707611084, 0.05249250680208206, -0.19096803665161133, 3.725290298461914e-09, 0.9642360210418701, 0.2650451958179474, 0.19805113971233368, 0.25979509949684143, -0.9451361298561096),
    [5] = CFrame.new(643.7172241210938, 68.56385803222656, 1036.7861328125, -0.9329720139503479, 0.11909022182226181, -0.33967751264572144, -7.450581485102248e-09, 0.9436823129653931, 0.33085304498672485, 0.3599490225315094, 0.3086766302585602, -0.8804290890693665),
    [6] = Vector3.new(645.7730102539062, 65.71050262451172, 1046.96044921875)
}

game:GetService("Players").LocalPlayer.Character.Remotes.KeyEvent:FireServer(unpack(args))

local players = game:GetService("Players")
local char = players.LocalPlayer.Character
local torso = char:FindFirstChild("Torso")
function getNearbyAogiri()
local aogiri = {}
for _, v in ipairs(game:GetService("Workspace"):GetDescendants()) do
if v.Name == "Mid Rank Aogiri Member" or v.Name == "Low Rank Aogiri Member" or v.Name == "High Rank Aogiri Member" or v.Name == "Rank 2 Investigator" then
table.insert(aogiri, v)
end
end
return aogiri
end
function teleportBehindAogiri()
local aogiri = getNearbyAogiri()
for _, v in ipairs(aogiri) do
local rootPart = v:FindFirstChild("HumanoidRootPart")
if rootPart then
local charRootPart = char:FindFirstChild("HumanoidRootPart")
charRootPart.CFrame = rootPart.CFrame * CFrame.new(0, 0, 7)
end
end
end
teleportBehindAogiri()
end
