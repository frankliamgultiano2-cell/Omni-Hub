local Lighting = game:GetService("Lighting")
local vu = game:GetService("VirtualUser")
local ts = game:GetService("TweenService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local vers = "1.0"
local InfJumpActive = false
local ActiveNoclip = false
local ActiveAntiAfk = false
local plr = game.Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")
local hrp = char:WaitForChild("HumanoidRootPart")

local savedWalkSpeed = 16
local savedJumpPower = 50
local savedJumpHeight = 7
local enabledFly = false
local flySpeed = 50
local up, down = false, false
local bodyVel, bodyGyro
local flyLoop
local mobileForward, mobileBack, mobileLeft, mobileRight, mobileUp, mobileDown =
    false, false, false, false, false, false
local saveChanges = true
local placeId = game.PlaceId
local espEnabled = false
local tpOption
local fbActive = false

local device
local windowSize

if UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled then
    device = "Mobile"
windowSize = UDim2.fromOffset(400, 300)
else
    device = "PC"
windowSize = UDim2.fromOffset(580, 400)
end

local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()

local Window = WindUI:CreateWindow({
    Title = "Omni Hub",
    Icon = "door-open",
    Author = "by MehDelight",
    Folder = "Omni Script",
    
    -- ↓ This all is Optional. You can remove it.
    Size = windowSize,
    Transparent = true,
    Theme = "Dark",
    Resizable = true,
    SideBarWidth = 200,
    BackgroundImageTransparency = 0.42,
    HideSearchBar = true,
    ScrollBarEnabled = false,
    
    
    -- ↓ Optional. You can remove it.
    User = {
        Enabled = true,
        Anonymous = false,
        Callback = function()
            print("clicked")
        end,
    },

    -- !  ↓  remove this all, 
    -- !  ↓  if you DON'T need the key system
    KeySystem = { 
        -- ↓ Optional. You can remove it.
        Key = { "MehDelight", "Omni Hub", "Omni Script"},
        
        Note = "Use MehDelight to unlock the script.",
        
        
        -- ↓ Optional. You can remove it.
        URL = "YOUR LINK TO GET KEY (Discord, Linkvertise, Pastebin, etc.)",
        
        -- ↓ Optional. You can remove it.
        SaveKey = true, -- automatically save and load the key.
        
        -- ↓ Optional. You can remove it.
        -- API = {} ← Services. Read about it below ↓
    },
})

Window:Tag({
    Title = "V "..vers,
    Color = Color3.fromHex("#30ff6a")
})


-------

local PlayerTab = Window:Tab({
    Title = "Player",
    Icon = "user-round",
    Locked = false,
})

local TeleportTab = Window:Tab({
    Title = "Teleport",
    Icon = "map-pin",
    Locked = false,
})

local ScriptsTab = Window:Tab({
    Title = "Scripts",
    Icon = "gamepad-2",
    Locked = false,
})

local Section = Window:Section({
    Title = "----------------",
    Icon = "",
    Opened = false,
})

local SettingsTab = Window:Tab({
    Title = "Settings",
    Icon = "settings",
    Locked = false,
})

-------

Window:SelectTab(1) -- Number of Tab

local WalkSpeedSlider = PlayerTab:Slider({
    Title = "Walk Speed",
    
    -- To make float number supported, 
    -- make the Step a float number.
    -- example: Step = 0.1
    Step = 1,
    
    Value = {
        Min = 16,
        Max = 500,
        Default = 16,
    },
    Callback = function(value)
savedWalkSpeed = value
        hum.WalkSpeed = value
    end
})

if hum.UseJumpPower == true then
local JumpPowerSlider = PlayerTab:Slider({
    Title = "Jump Power",
    
    -- To make float number supported, 
    -- make the Step a float number.
    -- example: Step = 0.1
    Step = 1,
    
    Value = {
        Min = 50,
        Max = 500,
        Default = 50,
    },
    Callback = function(value)
savedJumpPower = value
        hum.JumpPower = value
    end
})

else
local JumpHeightSlider = PlayerTab:Slider({
    Title = "Jump Height",
    
    -- To make float number supported, 
    -- make the Step a float number.
    -- example: Step = 0.1
    Step = 1,
    
    Value = {
        Min = 7,
        Max = 500,
        Default = 7,
    },
    Callback = function(value)
savedJumpHeight = value
        hum.JumpHeight = value
    end
})
end

UserInputService.JumpRequest:Connect(function()
    if InfJumpActive then
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

local InfiniteJumpToggle = PlayerTab:Toggle({
    Title = "Infinite Jump",
    Desc = "Jump Infinitely",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state) 
        InfJumpActive = state
    end
})

local NoclipToggle = PlayerTab:Toggle({
Title = "Noclip",
    Desc = "Noclip through walls",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state) 
ActiveNoclip = state
task.spawn(function()
            while ActiveNoclip do
            task.spawn(function()
                if char then
                for _, Parts in pairs(char:GetDescendants()) do
                if Parts:IsA("BasePart") and Parts.CanCollide then
                Parts.CanCollide = false
                end
                end
                end
                end)
            task.wait(0.1)
            end
            if char then
            for _, Parts in pairs(char:GetDescendants()) do
            if Parts:IsA("BasePart") and not Parts.CanCollide then
            Parts.CanCollide = true
            end
            end
            end
            end)
end
})

-- Create ScreenGui
local flyGui = Instance.new("ScreenGui")
flyGui.Name = "FlyControls"
flyGui.ResetOnSpawn = false
flyGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Common button style function
local function createButton(name, text, position, color)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 80, 0, 50)
    btn.Position = position
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.TextScaled = true
    btn.Parent = flyGui
    return btn
end

local upBtn = createButton(
    "FlyUp",
    "Up",
    UDim2.new(0, 20, 1, -120), -- 20px from left, 120px from bottom
    Color3.fromRGB(0, 170, 255)
)

-- Down Button (below Up)
local downBtn = createButton(
    "FlyDown",
    "Down",
    UDim2.new(0, 20, 1, -60), -- 20px from left, 60px from bottom
    Color3.fromRGB(255, 85, 85)
)

-- Button logic
upBtn.MouseButton1Down:Connect(function() mobileUp = true end)
upBtn.MouseButton1Up:Connect(function() mobileUp = false end)

downBtn.MouseButton1Down:Connect(function() mobileDown = true end)
downBtn.MouseButton1Up:Connect(function() mobileDown = false end)

UserInputService.InputBegan:Connect(function(input, gpe)
if gpe then return end

if input.KeyCode == Enum.KeyCode.F then
enabledFly = not enabledFly

hum.PlatformStand = enabledFly
elseif input.KeyCode == Enum.KeyCode.Space then
up = true

elseif input.KeyCode == Enum.KeyCode.LeftShift then
down = true
end
end) -- InputBegan

UserInputService.InputEnded:Connect(function(input)
if input.KeyCode == Enum.KeyCode.Space then
up = false
elseif input.KeyCode == Enum.KeyCode.LeftShift then
down = false
end
end) 

local function startFly()
    if flyLoop then return end

    bodyVel = Instance.new("BodyVelocity")
    bodyVel.MaxForce = Vector3.new(1e5, 1e5, 1e5)
    bodyVel.Velocity = Vector3.zero
    bodyVel.Parent = hrp

    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
    bodyGyro.CFrame = hrp.CFrame
    bodyGyro.P = 1e4
    bodyGyro.Parent = hrp

    hum.PlatformStand = true

    flyLoop = RunService.RenderStepped:Connect(function()
        local moveVector = Vector3.zero
        local cam = workspace.CurrentCamera

        -- camera-aligned planar axes
        local forward = Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z)
        if forward.Magnitude > 0 then forward = forward.Unit end
        local right = Vector3.new(cam.CFrame.RightVector.X, 0, cam.CFrame.RightVector.Z)
        if right.Magnitude > 0 then right = right.Unit end

        -- PC keys
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveVector += forward end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveVector -= forward end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveVector -= right end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveVector += right end
        if up then moveVector += Vector3.new(0, 1, 0) end
        if down then moveVector += Vector3.new(0, -1, 0) end

        -- Mobile: use Humanoid.MoveDirection (driven by thumbstick)
        if UserInputService.TouchEnabled then
            local dir = hum.MoveDirection
            if dir.Magnitude > 0 then
                -- horizontal contribution from joystick
                moveVector += Vector3.new(dir.X, 0, dir.Z)
            end
        end

        -- (Optional) keep your simple mobile flags if you set them elsewhere
        if mobileForward then moveVector += forward end
        if mobileBack   then moveVector -= forward end
        if mobileLeft   then moveVector -= right   end
        if mobileRight  then moveVector += right   end
        if mobileUp     then moveVector += Vector3.new(0, 1, 0) end
        if mobileDown   then moveVector += Vector3.new(0,-1, 0) end

        -- Apply velocity
        if moveVector.Magnitude > 0 then
            bodyVel.Velocity = moveVector.Unit * flySpeed
        else
            bodyVel.Velocity = Vector3.zero
        end

        bodyGyro.CFrame = CFrame.new(hrp.Position, hrp.Position + cam.CFrame.LookVector)
    end)
end

local function stopFly()
    if flyLoop then flyLoop:Disconnect() flyLoop = nil end
    if bodyVel then bodyVel:Destroy() bodyVel = nil end
    if bodyGyro then bodyGyro:Destroy() bodyGyro = nil end
    hum.PlatformStand = false
end

-- Connect toggle

local FlyToggle = PlayerTab:Toggle({
    Title = "Fly",
    Desc = "Enable flying",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state)
        enabledFly = state
        hum.PlatformStand = state
        if state then
            startFly()
upBtn.Visible = true
downBtn.Visible = true
        else
            stopFly()
upBtn.Visible = false
downBtn.Visible = false
        end
    end
})

local function GetPlayerNames()
local names = {}
for _, player in ipairs(Players:GetPlayers()) do
if player ~= plr then

table.insert(names, player.Name)
end
end
return names
end

local TeleportOptionsDropdown = TeleportTab:Dropdown({
    Title = "Teleport Options",
    Values = { "Instant Teleport", "Tween Teleport" },
    Value = "Instant Teleport",
    Callback = function(option) 
        tpOption = option
    end
})

local TeleportDropdown = TeleportTab:Dropdown({
    Title = "Teleport to Players",
    Values = GetPlayerNames(),
    Value = "Players",
    Callback = function(option) 
        local target = Players:FindFirstChild(option)

 local targetCharacter = target.Character or target.CharacterAdded:Wait()
local targethrp = targetCharacter:WaitForChild("HumanoidRootPart")
if targetCharacter and targethrp then
if tpOption == "Instant Teleport" then
hrp.CFrame = targethrp.CFrame + Vector3.new(0, 5, 0)

elseif tpOption == "Tween Teleport" then
local tween = ts:Create(hrp, TweenInfo.new(1, Enum.EasingStyle.Linear), { CFrame = targethrp.CFrame })

tween:Play()
tween.Completed:Wait()

end

end
    end
})

local function RefreshDropdown()
TeleportDropdown:Refresh(GetPlayerNames())
end

Players.PlayerAdded:Connect(function()
RefreshDropdown()
end)
Players.PlayerRemoving:Connect(function()
RefreshDropdown()
end)

task.spawn(function()
while true do
RefreshDropdown()
task.wait(5)
end
end)

-- Function to add highlight to a player
local function Removehighlight(player)
    if player == plr then return end
    local playerchar = player.Character or player.CharacterAdded:Wait()
    local ESP_Highlight = playerchar:FindFirstChild("ESP_Highlight")
if ESP_Highlight then
ESP_Highlight:Destroy()
end
end

local function highlightPlayer(player)
    if player == plr then return end
    local playerchar = player.Character or player.CharacterAdded:Wait()

    -- Avoid creating multiple highlights
    if playerchar:FindFirstChild("ESP_Highlight") then return end

    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP_Highlight"
    highlight.Adornee = playerchar
    highlight.FillColor = Color3.fromRGB(0, 255, 0) -- Box color
    highlight.FillTransparency = 0.5 -- Adjust transparency
    highlight.OutlineColor = Color3.fromRGB(0, 255, 0)
    highlight.OutlineTransparency = 0
    highlight.Parent = playerchar
end

Players.PlayerAdded:Connect(function(p)
if espEnabled then
highlightPlayer(p)
end
end)

Players.PlayerRemoving:Connect(Removehighlight)

local ESPToggle = PlayerTab:Toggle({
    Title = "ESP Players",
    Desc = "See Players through wall",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state) 
        espEnabled = state

        if espEnabled == true then
            WindUI:Notify({
                Title = "ESP is currently Active!",
                Content = "ESP is active!, you can see players thru walls",
                Duration = 3,
                Icon = "bird",
            })

            for _, player in ipairs(Players:GetPlayers()) do
                highlightPlayer(player)
            end  -- close for loop here

        elseif espEnabled == false then
            for _, player in ipairs(Players:GetPlayers()) do
                Removehighlight(player)
            end  -- close for loop here
        end  -- close if
    end  -- close function
})  -- close table

task.spawn(function()
    while true do
        if espEnabled then
            for _, player in ipairs(Players:GetPlayers()) do
                highlightPlayer(player)
            end
        end
        task.wait(3) -- always yield, even when espEnabled == false
    end
end)

------

local btn1 = ScriptsTab:Button({
    Title = "Infinite Yield",
    Desc = "Executes Infinite Yield",
    Locked = false,
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/DarkNetworks/Infinite-Yield/main/latest.lua'))()

WindUI:Notify({
    Title = "Infinite Yield Executed!",
    Content = "Infinite Yield has successfully executed!",
    Duration = 3, -- 3 seconds
    Icon = "bird",
})

    end
})

local btn2 = ScriptsTab:Button({
    Title = "99 Nights in the forest (Key)",
    Desc = "Executes H4x Script",
    Locked = false,
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/H4xScripts/Loader/refs/heads/main/loader.lua", true))()

WindUI:Notify({
    Title = "H4x Scrip Executed!",
    Content = "H4x Script has successfully executed!",
    Duration = 3, -- 3 seconds
    Icon = "bird",
})

    end
})

local btn3 = ScriptsTab:Button({
    Title = "Counter Blox",
    Desc = "Executes Counter Blox Script",
    Locked = false,
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/ege90002/IconicAdmin/main/CounterBlox"))()

WindUI:Notify({
    Title = "Iconic Admin Executed!",
    Content = "Iconic Admin has successfully executed!",
    Duration = 3, -- 3 seconds
    Icon = "bird",
})

    end
})

local btn4 = ScriptsTab:Button({
    Title = "Piggy",
    Desc = "Executes Piggy Script",
    Locked = false,
    Callback = function()
        -- execute the Piggy script
        loadstring(game:HttpGet("https://rawscripts.net/raw/Piggy-open-source-15390"))()

        -- send notification
        WindUI:Notify({
            Title = "Piggy Executed!",
            Content = "Piggy script has successfully executed!",
            Duration = 3, -- 3 seconds
            Icon = "bird",
        })
    end
})

---------

local function fullBright()
    if fbActive then
        Lighting.Ambient = Color3.new(1, 1, 1)
        Lighting.Brightness = 2
        Lighting.FogEnd = 100000 -- removes fog
        Lighting.GlobalShadows = false
    else
        -- optional: reset to default if you want
        Lighting.Ambient = Color3.fromRGB(127, 127, 127)
        Lighting.Brightness = 1
        Lighting.GlobalShadows = true
    end
end

local function SetupCharacter(newChar)
    char = newChar
    hum = char:WaitForChild("Humanoid")
    hrp = char:WaitForChild("HumanoidRootPart")

    -- Apply saved stats
    hum.WalkSpeed = savedWalkSpeed
    if hum.UseJumpPower then
        hum.JumpPower = savedJumpPower
    else
        hum.JumpHeight = savedJumpHeight
    end

    -- Reconnect infinite jump
    UserInputService.JumpRequest:Connect(function()
        if InfJumpActive then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end)

    -- Reapply noclip if active
    if ActiveNoclip then
        task.spawn(function()
            while ActiveNoclip and char.Parent do
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
                task.wait(0.1)
            end
        end)
    end

    -- Reapply fly if active
    if enabledFly then
        startFly()
    end

    -- Reapply ESP if active
    if espEnabled then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= plr then
                highlightPlayer(player)
            end
        end
    end
end

-- Initial setup
SetupCharacter(plr.Character or plr.CharacterAdded:Wait())

-- Listen for respawns
plr.CharacterAdded:Connect(SetupCharacter)

local function ServerHop()
    local servers = {}
    local req = game:HttpGet("https://games.roblox.com/v1/games/"..placeId.."/servers/Public?sortOrder=Asc&limit=100")
    local data = HttpService:JSONDecode(req)

    for _, server in ipairs(data.data) do
        if server.playing < server.maxPlayers and server.id ~= game.JobId then
            table.insert(servers, server.id)
        end
    end

    if #servers > 0 then
        TeleportService:TeleportToPlaceInstance(placeId, servers[math.random(1, #servers)], plr)
    else
        warn("No available servers found.")
    end
end

local Rejoinbtn = SettingsTab:Button({
    Title = "Rejoin",
    Description = "Rejoin this exact server",
    Callback = function()
        TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, plr)
    end
})

local Serverhopbtn = SettingsTab:Button({
    Title = "Server Hop",
    Description = "Join random server.",
    Callback = function()
        ServerHop()
    end
})

local SaveToggle = SettingsTab:Toggle({
    Title = "Save Changes",
    Desc = "Save and load the changes when you die",
    Icon = "check",
    Type = "Checkbox",
    Default = true,
    Callback = function(state) 
saveChanges = state
        if saveChanges then
SetupCharacter()
end
    end
})

local FlySpeed = SettingsTab:Slider({
    Title = "Fly Speed",
    
    -- To make float number supported, 
    -- make the Step a float number.
    -- example: Step = 0.1
    Step = 1,
    
    Value = {
        Min = 50,
        Max = 500,
        Default = 50,
    },
    Callback = function(value)
        flySpeed = value
    end
})

local AntiAFKToggle = SettingsTab:Toggle({
    Title = "Anti-AFK",
    Desc = "Won't kick you  when your afk",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state) 
        ActiveAntiAfk = state
    end
})

plr.Idled:Connect(function()
if ActiveAntiAfk then
    vu:Button2Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
    task.wait(1)
    vu:Button2Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
end
end)

Lighting.Changed:Connect(fullBright)

local FB = SettingsTab:Toggle({
    Title = "Full Brightness",
    Desc = "Makes the surroundings brighter",
    Icon = "check",
    Type = "Checkbox",
    Default = false,
    Callback = function(state) 
        fbActive = state
if fbActive then
fullBright()
end
    end
})
