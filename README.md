local args = {
    [2] = workspace:WaitForChild("Entities"):WaitForChild("Titans"):WaitForChild("ColossalTitan"):WaitForChild("Humanoid"),
    [3] = "&@&*&@&",
    [4] = workspace:WaitForChild("Entities"):WaitForChild("Titans"):WaitForChild("ColossalTitan")
}

game:GetService("ReplicatedStorage"):WaitForChild("DamageEvent"):FireServer(unpack(args))
