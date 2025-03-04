local player = game.Players.LocalPlayer
if not player then return end

local playerGui = player:WaitForChild("PlayerGui")
if not playerGui then return end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CustomGUI"
ScreenGui.Parent = playerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 300, 0, 400)
Frame.Position = UDim2.new(0.1, 0, 0.1, 0)
Frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Frame.Parent = ScreenGui
Frame.Draggable = true

local function toggleGUI()
    Frame.Visible = not Frame.Visible
end

local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if input.KeyCode == Enum.KeyCode.RightControl then
        toggleGUI()
    end
end)

local tabs = {
    {name = "Troll", functions = {
        {name = "Mudar Cor do Céu", function() game.Lighting.Ambient = Color3.new(1, 0, 0) end},
        {name = "Som de Sustos", function()
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://182787722"
            sound.Parent = player.Character.Head
            sound:Play()
            wait(5)
            sound:Destroy()
        end},
        {name = "Mensagem Global", function() game.ReplicatedStorage.DefaultChatEvents.SayMessagePublic:FireServer("Mensagem troll!", "All") end},
    }},
    {name = "Kill", functions = {
        {name = "Kill Player", function()
            local targetPlayer = game.Players:FindFirstChild("NomeDoJogador") -- Substitua pelo nome do jogador alvo
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Humanoid") then
                targetPlayer.Character.Humanoid.Health = 0
            end
        end},
    }},
    {name = "Audio", functions = {
        {name = "Audio Global 1", function()
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://182787722"
            sound.Volume = 1
            sound.Looped = true
            sound.Parent = game.Workspace
            sound:Play()
            wait(60)
            sound:Destroy()
        end},
        {name = "Audio Global 2", function()
            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://237487920"
            sound.Volume = 1
            sound.Looped = true
            sound.Parent = game.Workspace
            sound:Play()
            wait(60)
            sound:Destroy()
        end},
    }},
    {name = "Outros", functions = {
        {name = "Teleport para Casa", function()
            local house = workspace:FindFirstChild("House")
            if house then
                player.Character:MoveTo(house.Position)
            end
        end},
        {name = "Velocidade +", function() player.Character.Humanoid.WalkSpeed = player.Character.Humanoid.WalkSpeed + 5 end},
        {name = "Pulo +", function() player.Character.Humanoid.JumpPower = player.Character.Humanoid.JumpPower + 10 end},
        {name = "Mudar Cor da GUI", function()
            local colorMenu = Instance.new("Frame")
            colorMenu.Size = UDim2.new(0, 150, 0, 200)
            colorMenu.Position = UDim2.new(0.5, -75, 0.5, -100)
            colorMenu.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
            colorMenu.BorderSizePixel = 0
            colorMenu.Parent = Frame

            local colors = {
                {name = "Vermelho", color = Color3.new(1, 0, 0)},
                {name = "Verde", color = Color3.new(0, 1, 0)},
                {name = "Azul", color = Color3.new(0, 0, 1)},
                {name = "Amarelo", color = Color3.new(1, 1, 0)},
                {name = "Roxo", color = Color3.new(1, 0, 1)},
                {name = "Branco", color = Color3.new(1, 1, 1)},
                {name = "Preto", color = Color3.new(0, 0, 0)},
            }

            local buttonHeight = 30
            local buttonSpacing = 5

            for i, colorData in ipairs(colors) do
                local colorButton = Instance.new("TextButton")
                colorButton.Size = UDim2.new(1, -10, 0, buttonHeight)
                colorButton.Position = UDim2.new(0, 5, 0, (i - 1) * (buttonHeight + buttonSpacing) + 5)
                colorButton.Text = colorData.name
                colorButton.BackgroundColor3 = colorData.color
                colorButton.TextColor3 = Color3.new(0, 0, 0)
                colorButton.Parent = colorMenu

                colorButton.MouseButton1Click:Connect(function()
                    Frame.BackgroundColor3 = colorData.color
                    colorMenu:Destroy()
                end)
            end
        end},
    }},
}

local tabButtons = {}
local functionFrames = {}
local currentTab = 1

local tabButtonHeight = 30
local tabButtonSpacing = 5

for i, tabData in ipairs(tabs) do
    local tabButton = Instance.new("TextButton")
    tabButton.Size = UDim2.new(0, 80, 0, tabButtonHeight)
    tabButton.Position = UDim2.new(0, 5, 0, (i - 1) * (tabButtonHeight + tabButtonSpacing) + 5)
    tabButton.Text = tabData.name
    tabButton.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
    tabButton.Parent = Frame

    local functionFrame = Instance.new("Frame")
    functionFrame.Size = UDim2.new(1, -90, 1, -10)
    functionFrame.Position = UDim2.new(0, 85, 0, 5)
    functionFrame.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
    functionFrame.Visible = (i == currentTab)
    functionFrame.Parent = Frame

    tabButtons[i] = tabButton
    functionFrames[i] = functionFrame

    tabButton.MouseButton1Click:Connect(function()
        currentTab = i
        for j, frame in ipairs(functionFrames) do
            frame.Visible = (j == currentTab)
        end
    end)

    local functionButtonHeight = 30
    local functionButtonSpacing = 5

    for j, funcData in ipairs(tabData.functions) do
        local functionButton = Instance.new("TextButton")
        functionButton.Size = UDim2.new(1, -10, 0, functionButtonHeight)
        functionButton.Position = UDim2.new(0, 5, 0, (j - 1) * (functionButtonHeight + functionButtonSpacing) + 5)
        functionButton.Text = funcData.name
        functionButton.BackgroundColor3 = Color3.new(0.5, 0.5, 0.5)
        functionButton.Parent = functionFrame

        if type(funcData.function) == "function" then
            functionButton.MouseButton1Click:Connect(funcData.function)
        end
    end
end

Frame.Visible = false
