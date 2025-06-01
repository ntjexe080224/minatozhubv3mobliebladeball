--⚡ MINATOZ HUB V3 (Versão Mobile) ⚡--

-- Som de entrada
local Sound = Instance.new("Sound", game:GetService("SoundService"))
Sound.SoundId = "rbxassetid://102756203656099"
Sound.Volume = 3
Sound:Play()

-- Key System simples
local key = "minatoscriptsisthebest"
local inputGui = Instance.new("ScreenGui", game.CoreGui)
local frame = Instance.new("Frame", inputGui)
frame.Size = UDim2.new(0, 300, 0, 150)
frame.Position = UDim2.new(0.5, -150, 0.5, -75)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)

local textBox = Instance.new("TextBox", frame)
textBox.PlaceholderText = "Digite a Key"
textBox.Size = UDim2.new(0.9, 0, 0.3, 0)
textBox.Position = UDim2.new(0.05, 0, 0.2, 0)
textBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
textBox.TextColor3 = Color3.new(1, 1, 1)

local enterBtn = Instance.new("TextButton", frame)
enterBtn.Text = "Entrar"
enterBtn.Size = UDim2.new(0.9, 0, 0.3, 0)
enterBtn.Position = UDim2.new(0.05, 0, 0.6, 0)
enterBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
enterBtn.TextColor3 = Color3.new(1, 1, 1)

enterBtn.MouseButton1Click:Connect(function()
    if textBox.Text == key then
        inputGui:Destroy()
        loadMobileUI()
    else
        enterBtn.Text = "Key incorreta!"
    end
end)

function loadMobileUI()
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "MinatozHub"

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.new(0, 340, 0, 560)
    frame.Position = UDim2.new(0, 10, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    frame.Active = true
    frame.Draggable = true

    local function createBtn(text, pos, func)
        local b = Instance.new("TextButton", frame)
        b.Text = text
        b.Size = UDim2.new(0, 140, 0, 30)
        b.Position = pos
        b.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        b.TextColor3 = Color3.new(1, 1, 1)
        b.Font = Enum.Font.GothamBold
        b.TextSize = 14
        b.MouseButton1Click:Connect(func)
        return b
    end

    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local VirtualInput = game:GetService("VirtualInputManager")
    local localPlayer = Players.LocalPlayer

    local enabled = {
        autoparry = false,
        autospam = false,
        autoclash = false,
        infinitejump = false,
        gravity = false,
        esp = false,
        traceline = false
    }

    local speed = 16
    local jump = 50
    local gravityValue = workspace.Gravity
    local clashDistance = 12
    local clashDelay = 0.05

    function clickMouse()
        VirtualInput:SendMouseButtonEvent(0, 0, 0, true, game, 0)
        VirtualInput:SendMouseButtonEvent(0, 0, 0, false, game, 0)
    end

    function getBall()
        for _, b in ipairs(workspace:WaitForChild("Balls"):GetChildren()) do
            if b:GetAttribute("realBall") then return b end
        end
    end

    -- Auto Parry
    RunService.Heartbeat:Connect(function()
        if enabled.autoparry then
            local ball = getBall()
            local hrp = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
            if ball and hrp then
                local spd = ball.zoomies.VectorVelocity.Magnitude
                local dist = (hrp.Position - ball.Position).Magnitude
                if ball:GetAttribute("target") == localPlayer.Name and dist/spd <= 0.65 then
                    clickMouse()
                end
            end
        end
    end)

    -- Auto Spam
    task.spawn(function()
        while task.wait() do
            if enabled.autospam then
                local ball = getBall()
                local hrp = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
                if ball and hrp then
                    local spd = ball.zoomies.VectorVelocity.Magnitude
                    local dist = (hrp.Position - ball.Position).Magnitude
                    local eta = dist / spd
                    if ball:GetAttribute("target") == localPlayer.Name and eta <= 0.8 then
                        for _ = 1, 3 do clickMouse() end
                    end
                end
            end
        end
    end)

    -- Auto Clash
    task.spawn(function()
        while task.wait() do
            if enabled.autoclash then
                local ball = getBall()
                local hrp = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
                if ball and hrp then
                    local spd = ball.zoomies.VectorVelocity.Magnitude
                    local dist = (hrp.Position - ball.Position).Magnitude
                    local eta = dist / spd
                    if ball:GetAttribute("target") == localPlayer.Name and dist <= clashDistance and eta <= clashDelay then
                        clickMouse()
                    end
                end
            end
        end
    end)

    -- Pulo Infinito
    game:GetService("UserInputService").JumpRequest:Connect(function()
        if enabled.infinitejump then
            local char = localPlayer.Character
            if char and char:FindFirstChildOfClass("Humanoid") then
                char:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end)

    -- Gravidade
    RunService.Heartbeat:Connect(function()
        if enabled.gravity then workspace.Gravity = gravityValue end
    end)

    -- Velocidade e Pulo
    RunService.Heartbeat:Connect(function()
        local h = localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid")
        if h then h.WalkSpeed = speed h.JumpPower = jump end
    end)

    -- Tracer da bola
    local tracer = Instance.new("Beam", workspace.CurrentCamera)
    tracer.Width0 = 0.15
    tracer.Width1 = 0.15
    tracer.Color = ColorSequence.new(Color3.new(0, 1, 1))
    tracer.Enabled = false

    RunService.RenderStepped:Connect(function()
        if enabled.traceline then
            local ball = getBall()
            local char = localPlayer.Character
            if ball and char and char:FindFirstChild("HumanoidRootPart") then
                local a0 = ball:FindFirstChild("Attachment") or Instance.new("Attachment", ball)
                local a1 = char.HumanoidRootPart:FindFirstChild("Attachment") or Instance.new("Attachment", char.HumanoidRootPart)
                tracer.Attachment0 = a0
                tracer.Attachment1 = a1
                tracer.Enabled = true
            else
                tracer.Enabled = false
            end
        else
            tracer.Enabled = false
        end
    end)

    -- ESP dos jogadores
    RunService.RenderStepped:Connect(function()
        local ball = getBall()
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local HRP = player.Character.HumanoidRootPart
                local existing = HRP:FindFirstChild("ESP")
                if enabled.esp then
                    if not existing then
                        local box = Instance.new("BoxHandleAdornment")
                        box.Name = "ESP"
                        box.Adornee = HRP
                        box.AlwaysOnTop = true
                        box.ZIndex = 10
                        box.Size = Vector3.new(4, 5, 1)
                        box.Color3 = Color3.new(1, 0, 0)
                        box.Transparency = 0.4
                        box.Parent = HRP
                    else
                        if ball and ball:GetAttribute("target") == player.Name then
                            existing.Color3 = Color3.new(0, 0, 1)
                        else
                            existing.Color3 = Color3.new(1, 0, 0)
                        end
                    end
                elseif existing then
                    existing:Destroy()
                end
            end
        end
    end)

    -- Botões
    createBtn("Auto Parry", UDim2.new(0, 10, 0, 10), function() enabled.autoparry = not enabled.autoparry end)
    createBtn("Auto Spam", UDim2.new(0, 160, 0, 10), function() enabled.autospam = not enabled.autospam end)
    createBtn("Auto Clash", UDim2.new(0, 10, 0, 50), function() enabled.autoclash = not enabled.autoclash end)
    createBtn("Clash Dist +", UDim2.new(0, 10, 0, 90), function() clashDistance += 1 end)
    createBtn("Clash Dist -", UDim2.new(0, 160, 0, 90), function() clashDistance = math.max(1, clashDistance - 1) end)
    createBtn("Delay +", UDim2.new(0, 10, 0, 130), function() clashDelay += 0.01 end)
    createBtn("Delay -", UDim2.new(0, 160, 0, 130), function() clashDelay = math.max(0, clashDelay - 0.01) end)
    createBtn("Speed +", UDim2.new(0, 10, 0, 170), function() speed += 2 end)
    createBtn("Speed -", UDim2.new(0, 160, 0, 170), function() speed = math.max(0, speed - 2) end)
    createBtn("Jump +", UDim2.new(0, 10, 0, 210), function() jump += 5 end)
    createBtn("Jump -", UDim2.new(0, 160, 0, 210), function() jump = math.max(0, jump - 5) end)
    createBtn("Grav +", UDim2.new(0, 10, 0, 250), function() gravityValue += 10 enabled.gravity = true end)
    createBtn("Grav -", UDim2.new(0, 160, 0, 250), function() gravityValue = math.max(0, gravityValue - 10) enabled.gravity = true end)
    createBtn("Inf. Jump", UDim2.new(0, 10, 0, 290), function() enabled.infinitejump = not enabled.infinitejump end)
    createBtn("ESP Players", UDim2.new(0, 160, 0, 290), function() enabled.esp = not enabled.esp end)
    createBtn("Linha Bola", UDim2.new(0, 10, 0, 330), function() enabled.traceline = not enabled.traceline end)
end
