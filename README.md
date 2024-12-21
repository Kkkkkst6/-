----------------------------------------------------
---  A redistribution of https://wearedevs.net/  ---
----------------------------------------------------

--Waits until the player is in game
repeat wait()
until game.Players.LocalPlayer and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:findFirstChild("Torso") and game.Players.LocalPlayer.Character:findFirstChild("Humanoid")
local mouse = game.Players.LocalPlayer:GetMouse()

--Waits until the player's mouse is found
repeat wait() until mouse

--Variables
local plr = game.Players.LocalPlayer
local torso = plr.Character.Torso
local flying = false
local deb = true
local ctrl = {f = 0, b = 0, l = 0, r = 0}
local lastctrl = {f = 0, b = 0, l = 0, r = 0}
local maxspeed = 50
local speed = 0
local bg = nil
local bv = nil

--Actual flying function
function Fly()
	game.StarterGui:SetCore("SendNotification", {Title="Fly Activated"; Text="WeAreDevs.net"; Duration=1;})
    bg = Instance.new("BodyGyro", torso)
    bg.P = 9e4
    bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.cframe = torso.CFrame
    bv = Instance.new("BodyVelocity", torso)
    bv.velocity = Vector3.new(0,0.1,0)
    bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
    repeat wait()
      plr.Character.Humanoid.PlatformStand = true
      if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then
        speed = speed+.5+(speed/maxspeed)
        if speed > maxspeed then
          speed = maxspeed
        end
      elseif not (ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0) and speed ~= 0 then
        speed = speed-1
        if speed < 0 then
          speed = 0
        end
      end
      if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then
        bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f+ctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l+ctrl.r,(ctrl.f+ctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
        lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r}
      elseif (ctrl.l + ctrl.r) == 0 and (ctrl.f + ctrl.b) == 0 and speed ~= 0 then
        bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f+lastctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l+lastctrl.r,(lastctrl.f+lastctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
      else
        bv.velocity = Vector3.new(0,0.1,0)
      end
      bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame * CFrame.Angles(-math.rad((ctrl.f+ctrl.b)*50*speed/maxspeed),0,0)
    until not flying
    ctrl = {f = 0, b = 0, l = 0, r = 0}
    lastctrl = {f = 0, b = 0, l = 0, r = 0}
    speed = 0
    bg:Destroy()
	bg = nil
    bv:Destroy()
	bv = nil
    plr.Character.Humanoid.PlatformStand = false
	game.StarterGui:SetCore("SendNotification", {Title="Fly Deactivated"; Text="WeAreDevs.net"; Duration=1;})
end

-- GUI Setup
local screenGui = Instance.new("ScreenGui", plr.PlayerGui)
local dragFrame = Instance.new("Frame", screenGui)
dragFrame.Size = UDim2.new(0, 250, 0, 75)  -- ขนาดของพื้นที่ลาก
dragFrame.Position = UDim2.new(0.5, -125, 0.8, -37)  -- ตำแหน่งเริ่มต้นของพื้นที่ลาก
dragFrame.BackgroundColor3 = Color3.fromRGB(102, 153, 204)  -- สีที่ต้องการ (#6699CC)
dragFrame.BackgroundTransparency = 0.5
dragFrame.BorderSizePixel = 0

local flyButton = Instance.new("TextButton", dragFrame)
flyButton.Size = UDim2.new(0, 200, 0, 50)
flyButton.Position = UDim2.new(0.5, -100, 0.5, -25)
flyButton.Text = "Toggle Fly"
flyButton.BackgroundColor3 = Color3.fromRGB(102, 153, 204)  -- สีที่ต้องการ (#6699CC)
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.Font = Enum.Font.SourceSans
flyButton.TextSize = 24

-- Variables for dragging
local dragging = false
local dragInput, mousePos, offset

-- Function to start dragging
dragFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		offset = dragFrame.Position - UDim2.new(0, input.Position.X, 0, input.Position.Y)
		dragFrame.BackgroundColor3 = Color3.fromRGB(77, 121, 153)  -- เปลี่ยนสีเมื่อเริ่มลาก
	end
end)

-- Function to stop dragging
dragFrame.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
		dragFrame.BackgroundColor3 = Color3.fromRGB(102, 153, 204)  -- เปลี่ยนสีเมื่อหยุดลาก
	end
end)

-- Function to update the position of the frame during drag
game:GetService("UserInputService").InputChanged:Connect(function(input)
	if dragging then
		mousePos = input.Position
		dragFrame.Position = UDim2.new(0, mousePos.X, 0, mousePos.Y) + offset
	end
end)

-- Toggle flying when button is clicked
flyButton.MouseButton1Click:Connect(function()
    if flying then
        flying = false
    else
        flying = true
        Fly()
    end
end)

--Controls
mouse.KeyDown:connect(function(key)
	if key:lower() == "w" then
		ctrl.f = 1
	elseif key:lower() == "s" then
		ctrl.b = -1
	elseif key:lower() == "a" then
		ctrl.l = -1
	elseif key:lower() == "d" then
		ctrl.r = 1
	end
end)

mouse.KeyUp:connect(function(key)
	if key:lower() == "w" then
		ctrl.f = 0
	elseif key:lower() == "s" then
		ctrl.b = 0
	elseif key:lower() == "a" then
		ctrl.l = 0
	elseif key:lower() == "d" then
		ctrl.r = 0
	end
end)

Fly()
