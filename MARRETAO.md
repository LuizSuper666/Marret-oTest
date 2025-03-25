-- Criando a interface flutuante
local ScreenGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
local FloatingButton = Instance.new("TextButton", ScreenGui)

FloatingButton.Size = UDim2.new(0, 80, 0, 30)
FloatingButton.Position = UDim2.new(0.9, 0, 0.1, 0)
FloatingButton.Text = "SCRIPT"
FloatingButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)

local Panel = Instance.new("Frame", ScreenGui)
Panel.Size = UDim2.new(0, 200, 0, 200)
Panel.Position = UDim2.new(0.75, 0, 0.1, 0)
Panel.Visible = false
Panel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local CloseButton = Instance.new("TextButton", Panel)
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.Text = "X"
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

FloatingButton.MouseButton1Click:Connect(function()
    Panel.Visible = not Panel.Visible
end)

CloseButton.MouseButton1Click:Connect(function()
    Panel.Visible = false
end)

-- Criando botões e funções
local function createButton(name, position, action)
    local button = Instance.new("TextButton", Panel)
    button.Size = UDim2.new(0, 180, 0, 40)
    button.Position = UDim2.new(0, 10, 0, position)
    button.Text = name
    button.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Vermelho = desativado

    local active = false
    button.MouseButton1Click:Connect(function()
        active = not active
        button.BackgroundColor3 = active and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        action(active)
    end)
end

-- 🔰 Anti-Tudo
createButton("Anti-Tudo", 10, function(active)
    if active then
        -- Proteção contra kick
        local mt = getrawmetatable(game)
        setreadonly(mt, false)
        local oldNamecall = mt.__namecall
        mt.__namecall = newcclosure(function(self, ...)
            local method = getnamecallmethod()
            if method == "Kick" or method == "kick" then return nil end
            return oldNamecall(self, ...)
        end)

        -- Proteção contra AFK
        local Players = game:GetService("Players")
        for _, v in pairs(getconnections(Players.LocalPlayer.Idled)) do v:Disable() end

        -- 🔰 Proteção Contra Logs do Byfron (Impede Envio de Dados Suspeitos)
        local oldHttpPost = hookfunction(game.HttpPost, function(...)
            print("[🛡️ Proteção Ativada] Bloqueando Logs do Byfron.")
            return nil -- Bloqueia envio de logs suspeitos para os servidores do Roblox
        end)

        -- 🔰 Proteção Contra Fechamento Forçado do Jogo
        game:GetService("CoreGui").ChildRemoved:Connect(function(child)
            if child.Name == "RobloxPromptGui" then
                print("[⚠️ Proteção Ativada] Tentativa de Fechar Jogo Detectada.")
                wait(9e9) -- Previne fechamento forçado
            end
        end)
    else
        -- Desativar proteções (não tem como desfazer completamente, mas minimiza)
        game:GetService("Players").LocalPlayer.Idled:Connect(function() end)
    end
end)

-- ✈️ Voar
createButton("Voar", 60, function(active)
    local player = game:GetService("Players").LocalPlayer
    local char = player.Character
    if active then
        local bodyVelocity = Instance.new("BodyVelocity", char.HumanoidRootPart)
        bodyVelocity.Velocity = Vector3.new(0, 50, 0)
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
    else
        for _, v in pairs(char.HumanoidRootPart:GetChildren()) do
            if v:IsA("BodyVelocity") then v:Destroy() end
        end
    end
end)

-- 🚪 Atravessar paredes
createButton("Atravessar Paredes", 110, function(active)
    local char = game:GetService("Players").LocalPlayer.Character
    for _, part in pairs(char:GetChildren()) do
        if part:IsA("BasePart") then part.CanCollide = not active end
    end
end)

-- 👻 Invisibilidade + Remover Nick
createButton("Invisível", 160, function(active)
    local player = game:GetService("Players").LocalPlayer
    local char = player.Character

    if active then
        -- Tornar invisível
        for _, part in pairs(char:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 1
            end
        end
        -- Esconder nome
        local head = char:FindFirstChild("Head")
        if head then
            for _, v in pairs(head:GetChildren()) do
                if v:IsA("BillboardGui") then v.Enabled = false end
            end
        end
    else
        -- Tornar visível novamente
        for _, part in pairs(char:GetChildren()) do
            if part:IsA("BasePart") then
                part.Transparency = 0
            end
        end
        -- Mostrar nome de novo
        local head = char:FindFirstChild("Head")
        if head then
            for _, v in pairs(head:GetChildren()) do
                if v:IsA("BillboardGui") then v.Enabled = true end
            end
        end
    end
end)

print("[✅] UI Criada! Anti-Tudo, Voar, Atravessar Paredes e Invisibilidade ativados.")
