# scriptpiano-- 📜 PIANO PLAYER COM MENU GUI + ESP
-- Coloque em StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

-- 🎵 CONFIGURAÇÕES DO PIANO
local PIANO_KEYS = {}
local currentSong = nil
local isPlaying = false
local songSpeed = 1

-- 🎶 MÚSICAS
local SONGS = {
    Megalovania = {
        {key = "C5", time = 0.15}, {key = "C5", time = 0.15}, {key = "C5", time = 0.15},
        {key = "Eb5", time = 0.15}, {key = "G4", time = 0.30}, {key = "Bb4", time = 0.15},
        {key = "C5", time = 0.15}, {key = "Eb5", time = 0.30}, {key = "G4", time = 0.15},
        {key = "Bb4", time = 0.15}, {key = "C5", time = 0.30}, {key = "Eb5", time = 0.15},
        {key = "G4", time = 0.15}, {key = "Bb4", time = 0.15}, {key = "C5", time = 0.45},
        {key = "Eb5", time = 0.15}, {key = "G5", time = 0.15}, {key = "Bb5", time = 0.15}, {key = "C6", time = 0.45}
    },
    
    Minecraft = {
        {key = "F4", time = 0.4}, {key = "G4", time = 0.4}, {key = "A4", time = 0.4},
        {key = "Bb4", time = 0.4}, {key = "C5", time = 0.8}, {key = "Bb4", time = 0.4},
        {key = "A4", time = 0.4}, {key = "G4", time = 0.4}, {key = "F4", time = 0.8},
        {key = "Eb4", time = 0.4}, {key = "D4", time = 0.4}, {key = "Eb4", time = 0.4}, {key = "F4", time = 1.2}
    }
}

-- 🎮 MENU GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PianoMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 350, 0, 250)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -125)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

-- Cantos arredondados
local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 12)
Corner.Parent = MainFrame

-- Sombra
local Shadow = Instance.new("Frame")
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(1, 10, 1, 10)
Shadow.Position = UDim2.new(0, -5, 0, 5)
Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Shadow.BackgroundTransparency = 0.7
Shadow.BorderSizePixel = 0
Shadow.ZIndex = MainFrame.ZIndex - 1
Shadow.Parent = MainFrame

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 12)
ShadowCorner.Parent = Shadow

-- Título
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 0)
Title.Text = "🎹 PIANO PLAYER"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

-- Botão Megalovania
local MegaBtn = Instance.new("TextButton")
MegaBtn.Name = "MegaBtn"
MegaBtn.Size = UDim2.new(0.85, 0, 0, 60)
MegaBtn.Position = UDim2.new(0.075, 0, 0, 70)
MegaBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
MegaBtn.BorderSizePixel = 0
MegaBtn.Text = "🎵 MEGALOVANIA"
MegaBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MegaBtn.TextScaled = true
MegaBtn.Font = Enum.Font.GothamBold
MegaBtn.Parent = MainFrame

local MegaCorner = Instance.new("UICorner")
MegaCorner.CornerRadius = UDim.new(0, 8)
MegaCorner.Parent = MegaBtn

-- Botão Minecraft
local MineBtn = Instance.class("TextButton")
MineBtn.Name = "MineBtn"
MineBtn.Size = UDim2.new(0.85, 0, 0, 60)
MineBtn.Position = UDim2.new(0.075, 0, 0, 150)
MineBtn.BackgroundColor3 = Color3.fromRGB(50, 150, 255)
MineBtn.BorderSizePixel = 0
MineBtn.Text = "🎹 MINECRAFT"
MineBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MineBtn.TextScaled = true
MineBtn.Font = Enum.Font.GothamBold
MineBtn.Parent = MainFrame

local MineCorner = Instance.new("UICorner")
MineCorner.CornerRadius = UDim.new(0, 8)
MineCorner.Parent = MineBtn

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Name = "CloseBtn"
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -40, 0, 10)
CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseBtn.BorderSizePixel = 0
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextScaled = true
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = MainFrame

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0.5, 0)
CloseCorner.Parent = CloseBtn

-- Status Label
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "Status"
StatusLabel.Size = UDim2.new(1, -20, 0, 30)
StatusLabel.Position = UDim2.new(0, 10, 1, -40)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Piano não encontrado"
StatusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
StatusLabel.TextScaled = true
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.Parent = MainFrame

-- 🔍 ESP CONFIG
local ESP_ENABLED = false

-- 🎹 FUNÇÕES DO PIANO
local function findPiano()
    PIANO_KEYS = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("piano") or obj.Name:lower():find("key") then
            if obj:IsA("BasePart") and obj.Parent:FindFirstChild("ClickDetector") then
                PIANO_KEYS = obj.Parent:GetChildren()
                StatusLabel.Text = "✅ Piano encontrado! (" .. #PIANO_KEYS .. " teclas)"
                StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
                return true
            end
        end
    end
    StatusLabel.Text = "❌ Nenhum piano encontrado"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    return false
end

local function playNote(keyName)
    for _, key in pairs(PIANO_KEYS) do
        if key.Name:upper():find(keyName) or key.Name:lower() == keyName:lower() then
            if key:IsA("BasePart") and key:FindFirstChild("ClickDetector") then
                fireclickdetector(key:FindFirstChild("ClickDetector"))
                key.Color = Color3.fromRGB(255, 255, 0)
                wait(0.05)
                key.Color = Color3.fromRGB(255, 255, 255)
            end
            break
        end
    end
end

local function playSong(songName)
    if not PIANO_KEYS[1] then
        StatusLabel.Text = "❌ Encontre um piano primeiro!"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        return
    end
    
    local song = SONGS[songName]
    if not song then return end
    
    isPlaying = true
    StatusLabel.Text = "🎵 Tocando " .. songName
    StatusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    
    spawn(function()
        for _, note in pairs(son) do
            if not isPlaying then break end
            playNote(note.key)
            wait(note.time * songSpeed)
        end
        isPlaying = false
        StatusLabel.Text = "✅ Música terminada!"
    end)
end

-- 🎮 FUNÇÕES DO MENU
local menuVisible = false
local function toggleMenu()
    menuVisible = not menuVisible
    MainFrame.Visible = menuVisible
    
    if menuVisible then
        findPiano()
    end
end

-- Conexões dos botões
MegaBtn.MouseButton1Click:Connect(function()
    playSong("Megalovania")
end)

MineBtn.MouseButton1Click:Connect(function()
    playSong("Minecraft")
end)

CloseBtn.MouseButton1Click:Connect(function()
    toggleMenu()
end)

-- Efeitos hover
local function addHover(button, hoverColor, normalColor)
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = hoverColor}):Play()
    end)
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = normalColor}):Play()
    end)
end

addHover(MegaBtn, Color3.fromRGB(255, 100, 100), Color3.fromRGB(255, 50, 50))
addHover(MineBtn, Color3.fromRGB(100, 200, 255), Color3.fromRGB(50, 150, 255))
addHover(CloseBtn, Color3.fromRGB(255, 50, 50), Color3.fromRGB(255, 0, 0))

-- CONTROLES DE TECLA
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.I then
        toggleMenu()
    elseif input.KeyCode == Enum.KeyCode.M then
        playSong("Megalovania")
    elseif input.KeyCode == Enum.KeyCode.O then
        playSong("Minecraft")
    end
end)

-- 🔄 AUTO DETECT
spawn(function()
    while wait(2) do
        if menuVisible and #PIANO_KEYS == 0 then
            findPiano()
        end
    end
end)

print("🎹 PIANO MENU CARREGADO!")
print("🔑 I = Abrir Menu")
print("🔑 M = Megalovania")
print("🔑 O = Minecraft")
