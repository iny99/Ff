# Ff--[[
✅ AUTO FARM POR NÍVEL COM ESCOLHA DE SEA E ESTILO
⚠️ Apenas uso pessoal e seguro (servidor privado)
--]]

-- CONFIG GERAL
getgenv().AutoFarm = false
getgenv().AutoQuest = false
getgenv().AutoHaki = true
getgenv().SafeMode = true
getgenv().Kaitun = false

-- ESCOLHA SEA
local seaChoice = nil
repeat
    seaChoice = tonumber(string.match(tostring(
        game:GetService("Players").LocalPlayer:PromptInput("Qual SEA você está? Digite:\n1 = First Sea\n2 = Second Sea\n3 = Third Sea")
    ), "%d+"))
    wait()
until seaChoice == 1 or seaChoice == 2 or seaChoice == 3

-- ESCOLHA ESTILO DE COMBATE
local combatStyle = nil
repeat
    combatStyle = string.lower(
        tostring(game:GetService("Players").LocalPlayer:PromptInput("Qual estilo de combate?\nDigite: espada / fruta / melee"))
    )
    wait()
until combatStyle == "espada" or combatStyle == "fruta" or combatStyle == "melee"

print("🌊 Sea escolhido:", seaChoice)
print("⚔️ Estilo de combate:", combatStyle)

-- Função utilitária
local function useAttack()
    local VirtualInput = game:GetService("VirtualInputManager")
    VirtualInput:SendKeyEvent(true, "Z", false, game)
    wait(0.1)
    VirtualInput:SendKeyEvent(false, "Z", false, game)
end

-- Função: Detectar inimigo certo baseado no SEA e level
local function getTarget()
    local level = game.Players.LocalPlayer.Data.Level.Value
    local target = nil
    local questName = nil

    if seaChoice == 1 then
        if level < 10 then
            target = "Bandit"
            questName = "BanditQuest1"
        elseif level < 30 then
            target = "Monkey"
            questName = "JungleQuest"
        elseif level < 60 then
            target = "Pirate"
            questName = "BuggyQuest1"
        end
        -- Adicione mais níveis conforme quiser
    elseif seaChoice == 2 then
        -- Adicione as condições para o Second Sea
    elseif seaChoice == 3 then
        -- Adicione as condições para o Third Sea
    end

    return target, questName
end

-- Função: Auto Haki
spawn(function()
    while wait(1) do
        if getgenv().AutoHaki then
            local char = game.Players.LocalPlayer.Character
            if char and not char:FindFirstChild("HasBuso") then
                game:GetService("VirtualInputManager"):SendKeyEvent(true, "J", false, game)
                game:GetService("VirtualInputManager"):SendKeyEvent(false, "J", false, game)
            end
        end
    end
end)

-- Função: Auto Quest + Farm por Nível
spawn(function()
    while wait(0.3) do
        if getgenv().AutoFarm then
            local char = game.Players.LocalPlayer.Character
            if not char then continue end
            local root = char:FindFirstChild("HumanoidRootPart")
            if not root then continue end

            local targetName, quest = getTarget()
            if not targetName or not quest then return end

            -- Pega a missão (simples)
            if getgenv().AutoQuest then
                for _, npc in pairs(workspace:GetDescendants()) do
                    if npc:IsA("Model") and npc:FindFirstChild("Head") and npc.Name:lower():find("quest") then
                        if npc:FindFirstChild("HumanoidRootPart") then
                            root.CFrame = npc.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0)
                            wait(0.5)
                            fireclickdetector(npc.Head:FindFirstChildOfClass("ClickDetector"))
                        end
                    end
                end
            end

            -- Procura e farma o inimigo
            for _, mob in pairs(workspace.Enemies:GetChildren()) do
                if mob.Name == targetName and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChildOfClass("Humanoid").Health > 0 then
                    repeat
                        wait()
                        local offset = getgenv().SafeMode and Vector3.new(0, 20, 0) or Vector3.new(0, 3, 0)
                        root.CFrame = mob.HumanoidRootPart.CFrame + offset
                        useAttack()
                    until mob.Humanoid.Health <= 0 or not getgenv().AutoFarm
                end
            end
        end
    end
end)

-- Função Kaitun (girar ao redor)
spawn(function()
    while wait(0.1) do
        if getgenv().Kaitun and getgenv().AutoFarm then
            local char = game.Players.LocalPlayer.Character
            local root = char:FindFirstChild("HumanoidRootPart")
            local closest = nil
            local dist = math.huge
            for _, mob in pairs(workspace.Enemies:GetChildren()) do
                if mob:FindFirstChild("HumanoidRootPart") then
                    local d = (mob.HumanoidRootPart.Position - root.Position).Magnitude
                    if d < dist then
                        closest = mob
                        dist = d
                    end
                end
            end
            if closest then
                for a = 0, 360, 30 do
                    if not getgenv().Kaitun then break end
                    local r = math.rad(a)
                    local offset = Vector3.new(math.cos(r) * 10, 0, math.sin(r) * 10)
                    root.CFrame = closest.HumanoidRootPart.CFrame * CFrame.new(offset)
                    wait(0.05)
                end
            end
        end
    end
end)

-- Atalhos
UIS.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.LeftControl then
        getgenv().AutoFarm = not getgenv().AutoFarm
        print("AutoFarm:", getgenv().AutoFarm)
    elseif input.KeyCode == Enum.KeyCode.L then
        getgenv().AutoQuest = not getgenv().AutoQuest
        print("AutoQuest:", getgenv().AutoQuest)
    elseif input.KeyCode == Enum.KeyCode.K then
        getgenv().Kaitun = not getgenv().Kaitun
        print("Kaitun:", getgenv().Kaitun)
    end
end)

print("✅ SCRIPT INICIADO - CTRL = AutoFarm | L = AutoQuest | K = Kaitun")
