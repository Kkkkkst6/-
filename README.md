-- ตรวจสอบแพลตฟอร์ม
local UIS = game:GetService("UserInputService")
local isTouchDevice = UIS.TouchEnabled
local isKeyboardDevice = UIS.KeyboardEnabled

-- สร้างตัวแปรพื้นฐาน
local plr = game.Players.LocalPlayer
local torso = plr.Character:WaitForChild("Torso")
local flying = false
local ctrl = {f = 0, b = 0, l = 0, r = 0}
local speed = 0
local maxspeed = 50
local bg, bv = nil, nil

-- ฟังก์ชันปรับ CanCollide เพื่อทะลุวัตถุ
function SetCanCollide(character, state)
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = state
        end
    end
end

-- ฟังก์ชันการบิน
function Fly()
    game.StarterGui:SetCore("SendNotification", {Title = "Fly Activated"; Text = "WeAreDevs.net"; Duration = 1;})
    SetCanCollide(plr.Character, false) -- ทำให้ทะลุวัตถุได้
    bg = Instance.new("BodyGyro", torso)
    bg.P = 9e4
    bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.cframe = torso.CFrame

    bv = Instance.new("BodyVelocity", torso)
    bv.velocity = Vector3.new(0, 0.1, 0)
    bv.maxForce = Vector3.new(9e9, 9e9, 9e9)

    repeat wait()
        plr.Character.Humanoid.PlatformStand = true
        if ctrl.f + ctrl.b ~= 0 or ctrl.l + ctrl.r ~= 0 then
            speed = math.min(speed + 0.5 + speed / maxspeed, maxspeed)
        else
            speed = math.max(speed - 1, 0)
        end

        local lookVector = game.Workspace.CurrentCamera.CoordinateFrame.lookVector
        bv.velocity = lookVector * (ctrl.f + ctrl.b) * speed + Vector3.new(0, 0.1, 0)
        bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame
    until not flying

    SetCanCollide(plr.Character, true) -- คืนค่าให้เดินชนวัตถุได้
    ctrl = {f = 0, b = 0, l = 0, r = 0}
    speed = 0
    bg:Destroy()
    bv:Destroy()
    plr.Character.Humanoid.PlatformStand = false
    game.StarterGui:SetCore("SendNotification", {Title = "Fly Deactivated"; Text = "WeAreDevs.net"; Duration = 1;})
end

-- สร้าง GUI
local screenGui = Instance.new("ScreenGui", plr.PlayerGui)
local flyButton = Instance.new("TextButton", screenGui)
flyButton.Size = UDim2.new(0, 200, 0, 50)
flyButton.Position = UDim2.new(0.5, -100, 0.8, -25)
flyButton.Text = "Toggle Fly"
flyButton.BackgroundColor3 = Color3.fromRGB(102, 153, 204)
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.Font = Enum.Font.SourceSans
flyButton.TextSize = 24

-- ปุ่มควบคุมสำหรับมือถือ
if isTouchDevice then
    local upButton = Instance.new("TextButton", screenGui)
    upButton.Size = UDim2.new(0, 100, 0, 50)
    upButton.Position = UDim2.new(0.1, 0, 0.8, 0)
    upButton.Text = "Up"
    upButton.BackgroundColor3 = Color3.fromRGB(102, 153, 204)
    upButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    upButton.Font = Enum.Font.SourceSans
    upButton.TextSize = 24

    local downButton = Instance.new("TextButton", screenGui)
    downButton.Size = UDim2.new(0, 100, 0, 50)
    downButton.Position = UDim2.new(0.1, 0, 0.9, 0)
    downButton.Text = "Down"
    downButton.BackgroundColor3 = Color3.fromRGB(102, 153, 204)
    downButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    downButton.Font = Enum.Font.SourceSans
    downButton.TextSize = 24

    upButton.MouseButton1Down:Connect(function()
        ctrl.f = 1
    end)
    upButton.MouseButton1Up:Connect(function()
        ctrl.f = 0
    end)

    downButton.MouseButton1Down:Connect(function()
        ctrl.b = -1
    end)
    downButton.MouseButton1Up:Connect(function()
        ctrl.b = 0
    end)
end

-- การควบคุมด้วยคีย์บอร์ด (คอมพิวเตอร์)
if isKeyboardDevice then
    local mouse = plr:GetMouse()
    mouse.KeyDown:Connect(function(key)
        if key == "w" then
            ctrl.f = 1
        elseif key == "s" then
            ctrl.b = -1
        elseif key == "a" then
            ctrl.l = -1
        elseif key == "d" then
            ctrl.r = 1
        end
    end)

    mouse.KeyUp:Connect(function(key)
        if key == "w" then
            ctrl.f = 0
        elseif key == "s" then
            ctrl.b = 0
        elseif key == "a" then
            ctrl.l = 0
        elseif key == "d" then
            ctrl.r = 0
        end
    end)
end

-- ปุ่มเปิด/ปิดการบิน
flyButton.MouseButton1Click:Connect(function()
    flying = not flying
    if flying then
        Fly()
    end
end)
