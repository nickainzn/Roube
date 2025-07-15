local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local mt = getrawmetatable(game)
setreadonly(mt, false)
local namecall = mt.__namecall
mt.__namecall = newcclosure(function(self, ...)
    if getnamecallmethod() == "Kick" then return nil end
    return namecall(self, ...)
end)

Humanoid.WalkSpeed = 100
Humanoid.JumpPower = 150

local flying = false
local flySpeed = 50
local bodyVelocity, bodyGyro

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.E then
        flying = not flying
        if flying then
            bodyVelocity = Instance.new("BodyVelocity", Character.HumanoidRootPart)
            bodyGyro = Instance.new("BodyGyro", Character.HumanoidRootPart)
            bodyVelocity.MaxForce = Vector3.new(1e5, 1e5, 1e5)
            bodyGyro.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
        else
            bodyVelocity:Destroy()
            bodyGyro:Destroy()
        end
    end
end)

RunService.Heartbeat:Connect(function()
    if flying then
        local moveDir = Vector3.new()
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            moveDir = moveDir + workspace.CurrentCamera.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            moveDir = moveDir - workspace.CurrentCamera.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            moveDir = moveDir - workspace.CurrentCamera.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            moveDir = moveDir + workspace.CurrentCamera.CFrame.RightVector
        end
        moveDir = moveDir.Unit * flySpeed
        bodyVelocity.Velocity = Vector3.new(moveDir.X, 0, moveDir.Z)
        bodyGyro.CFrame = workspace.CurrentCamera.CFrame
    end
end)

spawn(function()
    while wait(3) do
        local btn = Workspace:FindFirstChild("BrainrotBuy") or Workspace:FindFirstChildWhichIsA("Part", true)
        if btn and btn:FindFirstChild("ClickDetector") then
            fireclickdetector(btn.ClickDetector)
        end
    end
end)

spawn(function()
    while wait(2) do
        for _, item in pairs(Workspace:GetDescendants()) do
            if item.Name == "BrainrotBag" or item.Name:lower():find("loot") then
                LocalPlayer.Character:PivotTo(item.CFrame)
                wait(0.8)
                local base = Workspace:FindFirstChild("SafeZone") or Workspace:FindFirstChild("MyBase")
                if base then
                    LocalPlayer.Character:PivotTo(base.CFrame)
                end
            end
        end
    end
end)

for _, p in pairs(Players:GetPlayers()) do
    if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
        local gui = Instance.new("BillboardGui", p.Character.Head)
        gui.Size = UDim2.new(0, 100, 0, 40)
        gui.AlwaysOnTop = true
        local label = Instance.new("TextLabel", gui)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Text = p.Name
        label.TextColor3 = Color3.fromRGB(255, 0, 0)
    end
end

spawn(function()
    while wait(1) do
        local timer = Workspace:FindFirstChild("BaseTimer") or Workspace:FindFirstChild("Timer")
        if timer and timer:IsA("TextLabel") then
            print("Tempo base: " .. timer.Text)
        end
    end
end)

Workspace.DescendantAdded:Connect(function(v)
    if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
        pcall(function() v:Destroy() end)
    end
end)
