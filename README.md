local spawns = {
    workspace["Aogiri Spawn"]:GetChildren()[7]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[6]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[5]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[4]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[3]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[2]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"]:GetChildren()[1]["High Rank Aogiri Member"],
    workspace["Aogiri Spawn"].GhoulSpawns["High Rank Aogiri Member"]
}

for _, spawn in ipairs(spawns) do
    while wait(0.1) do
        game.Players.LocalPlayer.Character:MoveTo(spawn.PrimaryPart.Position)
    end
end
