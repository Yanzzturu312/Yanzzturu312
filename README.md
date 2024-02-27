 -- loadstring(game:HttpGet("https://raw.githubusercontent.com/Babyhamsta/RBLX_Scripts/main/Universal/BypassedDarkDexV3.lua", true))()
 
repeat wait() until game:IsLoaded()
    local Camera = workspace.CurrentCamera
    local Workspace = game:GetService("Workspace")
    local ReplStorage = game:GetService("ReplicatedStorage")
    local Players = game:GetService("Players")
    local COREGUI = game:GetService("CoreGui")
    local LP = game:GetService("Players").LocalPlayer
    local HumanoidRootPart = LP.Character.HumanoidRootPart
    local VirtualUser = game:GetService("VirtualUser")
    local VIM = game:GetService("VirtualInputManager")
 
    --Service
    local TweenService = game:GetService("TweenService")
    local UIS = game:GetService("UserInputService")
    local HttpService = game:GetService("HttpService")
    local RunS = game:GetService("RunService")
    local TPS = game:GetService("TeleportService")
    local RunService = game:GetService("RunService")
    local LocalPlayer = Players.LocalPlayer
    local TeleportService = game:GetService("TeleportService")

  local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/download/1.1.0/main.lua"))()
    local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
    local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
 
 
 
    local Window = Fluent:CreateWindow({
        Title = "Turu hub | v0.0.1 Beta",
        SubTitle = " ",
        TabWidth = 100,
        Size = UDim2.fromOffset(450, 350),
        Acrylic = false, 
        Theme = "LIght",
        MinimizeKey = Enum.KeyCode.LeftControl 
    })
 
    local Tabs = {
        stats         = Window:AddTab({ Title = "auto farm stats", Icon = "nil" }),
        MainTab         = Window:AddTab({ Title = "Home", Icon = "nil" }),
        VehicleTab      = Window:AddTab({ Title = "Dealership", Icon = "nil" }),
        Environment     = Window:AddTab({ Title = "misc", Icon = "nil" }),
        Settings        = Window:AddTab({ Title = "Settings", Icon = "nil" }),
    }
 
    local Options = Fluent.Options
 
        local brightloop
 
        local function DayWeather()
            game:GetService("Lighting").ClockTime = 12
        end
 
        local function NightWeather()
            game:GetService("Lighting").ClockTime = 0
        end
 
        local function StartWeatherLoop(weatherFunction)
            if brightloop then
                brightloop:Disconnect() 
            end
            brightloop = game:GetService("RunService").RenderStepped:Connect(weatherFunction)
        end
 
        local weatherStatus = Tabs.Environment:AddDropdown("Dropdown", {
            Title = "Select Weather",
            Values = {"Day", "Night"},
            Multi = false,
            Default = "",
        })
        
        weatherStatus:OnChanged(function(Value)
            if Value == "Day" then
                StartWeatherLoop(DayWeather)
            elseif Value == "Night" then
                StartWeatherLoop(NightWeather)
            else
                if brightloop then
                    brightloop:Disconnect() 
                end
            end
        end)
 
        local ShadowToggle = Tabs.Environment:AddToggle("shaow", {Title = "Shadow Options", Default = false})
 
        ShadowToggle:OnChanged(function(Value)
            game:GetService("Lighting").GlobalShadows = Value
        end)
 
        function destroyListedObjects()
            local objectsToDestroy = {
                "AmbientLightRevamp",
                "ChristmasEvent",
                "Map.TrafficLight",
                "Map.Tree",
                "Map.17",
                "Map.Chirstmas",
                "Map.Ramadhan",
                "Map.RoadLight"
            }
        
            for _, objectName in ipairs(objectsToDestroy) do
                local object
                if objectName:find("%.") then
                    local parts = {}
                    for part in objectName:gmatch("[^%.]+") do
                        table.insert(parts, part)
                    end
        
                    object = workspace
                    for _, partName in ipairs(parts) do
                        object = object:FindFirstChild(partName)
                        if not object then break end
                    end
                else
                    object = workspace:FindFirstChild(objectName)
                end
        
                if object then
                    object:Destroy()
                else
                    warn(objectName .. " not found in workspace")
                end
            end
        end
        
        Tabs.Environment:AddButton({
            Title = "Boost FPS",
            Description = "the effect will continue until you relog",
            Callback = function()
                destroyListedObjects()
            end
        })
 
 
        -- [ FARM SECTION ]
        local FarmSection = Tabs.MainTab:AddSection("Farm Config")
 
        local truck_cooldown_teleport = 48
        local cd = 0;
 
        local AmountCooldown = FarmSection:AddInput("Input", {
            Title = "Coolown Teleport",
            Default =  48,
            Placeholder = "Recommended  48",
            Numeric = true, -- Only allows numbers
            Finished = false, -- Only calls callback when you press enter
            Callback = function(Value)
                truck_cooldown_teleport = Value
            end
        })    
 
        local CountdownTP = FarmSection:AddParagraph({
            Title = "Countdown Teleport",
            Content = "Teleport in "..tostring(cd)
        })
 
        getgenv().SelectedJob = nil;
        local JobDrop = FarmSection:AddDropdown("Dropdown", {
            Title = "Select Job",
            Values = {"Office Worker", "Janji Jiwa", "Truck Driver"},
            Multi = false,
            Default = getgenv().SelectedJob,
        })
        
        JobDrop:OnChanged(function(Value)
            getgenv().SelectedJob = Value
        end)
 
        local Toggle = FarmSection:AddToggle("StartFarm", {Title = "Auto Farm", Default = false })
 
        Toggle:OnChanged(function()
            if Options.StartFarm.Value == true then
                if getgenv().SelectedJob ~= nil then
                    if StopFarm == true then
                        StopFarm = false
                    end
                    DoFarm(getgenv().SelectedJob)
                else
                    Fluent:Notify({
                        Title = "Valleys hub",
                        Content = "Please Select a Job First",
                        Duration = 5 -- Set to nil to make the notification not disappear
                    })
                end
            else
                if string.find(tostring(getgenv().SelectedJob), "Driver") then
                    cd = 0;
                end 
                StopFarm = true
            end
 
        end)
        local StatsSection = Tabs.stats:AddSection("Stats")
 
        local ClientCash = StatsSection:AddParagraph({
            Title = "Your Cash",
            Content = LP:FindFirstChild("PlayerGui").Main.Container.Hub.CashFrame.Frame.TextLabel.Text
        })
 
        local ClientEarnings =  StatsSection:AddParagraph({
            Title = "Earnings Cash",
            Content = LP:FindFirstChild("PlayerGui").PhoneUI.HolderHP.Homescreen.ProfileScreen.MainFrame.EarningFrame.Value.Text
        })
 
        local ClientTime =  StatsSection:AddParagraph({
            Title = "Farming Time",
            Content = "You're not start Auto Farming"
        })
 
        -- [ SNIPER BOX ]
        local BoxSniper = Tabs.MainTab:AddSection("Box Sniper")
 
        BoxSniper:AddButton({
            Title = "Claim Box",
            Description = "",
            Callback = function()
                local args = {
                    [1] = "Claim"
                }
                game:GetService("ReplicatedStorage").NetworkContainer.RemoteEvents.Box:FireServer(unpack(args))
            end
        })
 
        BoxSniper:AddButton({
            Title = "Gamepass Box",
            Description = "",
            Callback = function()
                local args = {
                    [1] = "Buy",
                    [2] = "Gamepass Box"
                }
                game:GetService("ReplicatedStorage").NetworkContainer.RemoteEvents.Box:FireServer(unpack(args))
            end
        })
 
        BoxSniper:AddButton({
            Title = "Limited Box",
            Description = "",
            Callback = function()
                local args = {
                    [1] = "Buy",
                    [2] = "Limited Box"
                }
                game:GetService("ReplicatedStorage").NetworkContainer.RemoteEvents.Box:FireServer(unpack(args))
            end
        })
 
