while wait() do
local players = game:GetService("Players")
local char = players.LocalPlayer.Character
local torso = char:FindFirstChild("Torso")
function getNearbyAogiri()
local aogiri = {}
for _, v in ipairs(game:GetService("Workspace"):GetDescendants()) do
if v.Name == "Mid Rank Aogiri Member" or v.Name == "Low Rank Aogiri Member" or v.Name == "High Rank Aogiri Member" then
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
