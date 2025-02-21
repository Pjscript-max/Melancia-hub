# Melancia-hubrepeat wait() until game:IsLoaded()

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local backpack = player.Backpack

-- Função para teleportar
local function teleportTo(position)
    humanoidRootPart.CFrame = CFrame.new(position)
    wait(1)
end

-- Localizações importantes
local locations = {
    tushitaBoss = Vector3.new(1000, 100, 2000), -- Boss da Tushita
    yamaBoss = Vector3.new(1500, 100, 2500), -- Boss da Yama
    farmArea = Vector3.new(1200, 100, 2200), -- Área para farmar NPCs
    altar1 = Vector3.new(1800, 120, 2800), -- Altar 1
    altar2 = Vector3.new(1900, 130, 2900), -- Altar 2
    cdkNPC = Vector3.new(2000, 150, 3000) -- NPC para pegar a CDK
}

-- Verifica se já tem as espadas
local function hasSword(swordName)
    for _, tool in ipairs(backpack:GetChildren()) do
        if tool:IsA("Tool") and tool.Name == swordName then
            return true
        end
    end
    return false
end

-- Farmar Boss da Tushita
if not hasSword("Tushita") then
    print("Obtendo Tushita...")
    teleportTo(locations.tushitaBoss)
    -- Auto atacar o boss
    for _, enemy in ipairs(workspace.Enemies:GetChildren()) do
        if enemy.Name == "Boss da Tushita" and enemy:FindFirstChild("Humanoid") then
            humanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame
            repeat
                wait(0.5)
                game.ReplicatedStorage.Remotes.Combat:FireServer("Ataque")
            until enemy.Humanoid.Health <= 0
        end
    end
end

-- Farmar Boss da Yama
if not hasSword("Yama") then
    print("Obtendo Yama...")
    teleportTo(locations.yamaBoss)
    -- Auto atacar o boss
    for _, enemy in ipairs(workspace.Enemies:GetChildren()) do
        if enemy.Name == "Boss da Yama" and enemy:FindFirstChild("Humanoid") then
            humanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame
            repeat
                wait(0.5)
                game.ReplicatedStorage.Remotes.Combat:FireServer("Ataque")
            until enemy.Humanoid.Health <= 0
        end
    end
end

-- Auto Farm de NPCs (30 kills)
local function farmKills(swordName)
    if hasSword(swordName) then
        print("Farmando kills com " .. swordName)
        teleportTo(locations.farmArea)
        
        -- Equipa a espada
        local sword = backpack:FindFirstChild(swordName)
        if sword then
            sword.Parent = character
        end

        -- Ataca NPCs automaticamente
        local killCount = 0
        while killCount < 30 do
            for _, enemy in ipairs(workspace.Enemies:GetChildren()) do
                if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                    humanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame
                    repeat
                        wait(0.5)
                        game.ReplicatedStorage.Remotes.Combat:FireServer("Ataque")
                    until enemy.Humanoid.Health <= 0
                    killCount = killCount + 1
                    print("Kills com " .. swordName .. ": " .. killCount)
                    if killCount >= 30 then break end
                end
            end
        end
    else
        print(swordName .. " não encontrada!")
    end
end

farmKills("Tushita")
farmKills("Yama")

-- Ativar os altares
print("Ativando os altares...")
teleportTo(locations.altar1)
wait(2)
teleportTo(locations.altar2)
wait(2)

-- Ir até o NPC pegar a CDK
print("Indo buscar a CDK...")
teleportTo(locations.cdkNPC)

print("Processo finalizado!")
