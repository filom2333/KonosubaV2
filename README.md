-- Konosuba GUI Script - Main
-- This script provides various in-game GUI enhancements.

local HG = {}

-- Core Services, References, and Configuration
HG.Services = {}
HG.PlayerRefs = {}
HG.Clipboard = {} -- For clipboard operations
HG.Config = {
    DEFAULT_SETTINGS = {
        playerESPEnabled = false,
        espLinesEnabled = false, showHealthBar = true,
        skeletonESPEnabled = false,
        espMaxDistance = 400,
        espRadarEnabled = false, espRadarSize = 120, espRadarRange = 150,
        aimbotEnabled = false,
        showFOV = false, showCrosshair = false, targetHead = false, showOffScreenArrows = false,
        sensitivity = 0.9,
        fovSize = 150, -- fovCrosshairOffsetY was removed, this is the new default
        aimbotActivationKey = Enum.KeyCode.LeftShift,
        aimbotWallCheckEnabled = true,
        aimbotShowTargetLine = false,
        aimbotPredictionEnabled = false,
        aimbotPredictionStrength = 0.1,
        espOutlineColor = Color3.new(1, 1, 1),
        espLineColor = Color3.fromRGB(255, 255, 255),
        currentTheme = "Dark",
        blurSize = 70,
        backgroundBlurEnabled = true,
        fovCircleColor = Color3.fromRGB(255, 0, 0),
        fovCircleThickness = 1.0,
        loadingBlurSize = 15,
        loadingBackgroundTransparency = 0.1,
        devFreecamEnabled = false,
        devBrightSkyEnabled = false
    },
    settings = {}, -- Live settings, will be initialized from DEFAULT_SETTINGS
    CONSTANTS = {
        -- ESP & Visuals
        SKELETON_HEAD_MAX_DISTANCE = 100,
        espLineThickness = 1,
        espHealthBarHeight = 4,
        espHealthBarOffsetY = 3,
        espHealthBarColorFG = Color3.fromRGB(0, 220, 50),
        ESP_BOX_THICKNESS = 1.5,
        ESP_BOX_CORNER_RADIUS = UDim.new(0, 3),
        ESP_NAME_BACKGROUND_TRANSPARENCY = 0.75,
        ESP_CHECK_INTERVAL = 0.5, -- How often to run the full ESP check
        -- Radar
        espRadarDotSize = 6,
        espRadarEnemyDotColor = Color3.fromRGB(255, 0, 0),
        espRadarDotOutlineColor = Color3.fromRGB(255, 255, 255),
        espRadarDotOutlineThickness = 1,
        espRadarCenterMarkerColor = Color3.fromRGB(255, 255, 255),
        espRadarCenterMarkerSize = 8,
        espRadarCenterMarkerThickness = 1.5,
        espRadarFrameBorderThickness = 1.5,
        RADAR_BACKGROUND_TRANSPARENCY = 0.75,
        -- Skeleton
        SKELETON_LINE_THICKNESS = 1.5,
        SKELETON_HEAD_SIZE = 12, -- Default pixel size
        SKELETON_HEAD_STROKE_THICKNESS = 1.5,
        SKELETON_ZINDEX = 10,
        SKELETON_HEAD_FILL_TRANSPARENCY = 0.85,
        MIN_SKELETON_HEAD_PIXEL_SIZE = 6,
        MAX_SKELETON_HEAD_PIXEL_SIZE = 24,
        SKELETON_HEAD_PROPORTION = 0.7, -- Proportion of character head height
        -- GUI & Styling
        BLUR_EFFECT_NAME = "KONOSUBA_GUI_Blur_v17.4", -- Keep this consistent for the blur effect
        LOADING_DURATION = 2, -- Seconds for the loading bar
        MODERN_UI_ELEMENT_CORNER_RADIUS = UDim.new(0, 8),
        MODERN_UI_PILL_CORNER_RADIUS = UDim.new(0.5, 0), -- For perfectly round ends on progress bars etc.
        BUTTON_CORNER_RADIUS = UDim.new(0, 6),
        MINIMIZE_CIRCLE_SIZE = 50,
        MINIMIZE_CIRCLE_PADDING = 15,
        MINIMIZE_CIRCLE_DEFAULT_POS = UDim2.new(1, -50 - 15, 0, 15),
        -- FOV & Aimbot Visuals
        FOV_ARROW_SIZE = 24,
        FOV_ARROW_LINE_THICKNESS = 2,
        FOV_ARROW_ZINDEX = 98,
        AIMBOT_TARGET_LINE_THICKNESS = 1.5,
        AIMBOT_TARGET_LINE_ZINDEX = 99,
        -- Developer Mode
        FREECAM_MOVE_SPEED = 50,
        FREECAM_LOOK_SPEED = 0.0075,
    },
    THEME_SETTINGS = {
        Dark = {
            MainBG = Color3.fromRGB(16, 16, 20), TitleBG = Color3.fromRGB(20, 20, 25), SidebarBG = Color3.fromRGB(18, 18, 22),
            ContentAreaBG = Color3.fromRGB(16, 16, 20), ContainerBG = Color3.fromRGB(24, 24, 29),
            PrimaryText = Color3.fromRGB(230, 230, 235), SecondaryText = Color3.fromRGB(155, 155, 165),
            ButtonText = Color3.fromRGB(255, 255, 255), HeaderText = Color3.fromRGB(210, 210, 215),
            AccentColor = Color3.fromRGB(120, 100, 255), AccentColorText = Color3.fromRGB(255, 255, 255),
            ButtonBG_Inactive = Color3.fromRGB(38, 38, 43), ButtonBG_Active = Color3.fromRGB(120, 100, 255),
            ButtonBG_Minimize = Color3.fromRGB(50, 50, 60), ButtonBG_Minimize_Active = Color3.fromRGB(70, 70, 80),
            ButtonBG_Close = Color3.fromRGB(210, 60, 70),
            SliderTrack = Color3.fromRGB(40, 40, 45), SliderHandle = Color3.fromRGB(230, 230, 235),
            SliderHandleBorder = Color3.fromRGB(30, 30, 35), SliderFilledTrack = Color3.fromRGB(120, 100, 255),
            SidebarButtonBG_Inactive = Color3.fromRGB(18, 18, 22), SidebarButtonBG_Active = Color3.fromRGB(30, 30, 35),
            SidebarButtonText_Inactive = Color3.fromRGB(155, 155, 165), SidebarButtonText_Active = Color3.fromRGB(230, 230, 235),
            SidebarActiveIndicator = Color3.fromRGB(120, 100, 255),
            OutlineColor = Color3.fromRGB(35, 35, 40), HeaderBorder = Color3.fromRGB(30, 30, 35),
            ColorButtonBorder_Inactive = Color3.fromRGB(50, 50, 55), ColorButtonBorder_Active = Color3.fromRGB(120, 100, 255),
            AuthorLinkColorHex = "9080FF", HoverColorOffset = 12,
            LoadingText = Color3.fromRGB(220, 220, 225), LoadingBG = Color3.fromRGB(12, 12, 15),
            ESPNameBG = Color3.fromRGB(12, 12, 15), HealthBarBGColor = Color3.fromRGB(60, 20, 20),
            MinimizeCircleBG = Color3.fromRGB(35, 35, 40), MinimizeCircleText = Color3.fromRGB(120, 100, 255),
            FOVArrowColor = Color3.fromRGB(220, 220, 225)
        },
        Light = {
            MainBG = Color3.fromRGB(245, 245, 250), TitleBG = Color3.fromRGB(235, 235, 240), SidebarBG = Color3.fromRGB(240, 240, 245),
            ContentAreaBG = Color3.fromRGB(245, 245, 250), ContainerBG = Color3.fromRGB(255, 255, 255),
            PrimaryText = Color3.fromRGB(20, 20, 25), SecondaryText = Color3.fromRGB(100, 100, 110),
            ButtonText = Color3.fromRGB(255, 255, 255), HeaderText = Color3.fromRGB(40, 40, 45),
            AccentColor = Color3.fromRGB(80, 70, 220), AccentColorText = Color3.fromRGB(255, 255, 255),
            ButtonBG_Inactive = Color3.fromRGB(220, 220, 225), ButtonBG_Active = Color3.fromRGB(80, 70, 220),
            ButtonBG_Minimize = Color3.fromRGB(180, 180, 190), ButtonBG_Minimize_Active = Color3.fromRGB(160, 160, 170),
            ButtonBG_Close = Color3.fromRGB(220, 60, 70),
            SliderTrack = Color3.fromRGB(210, 210, 215), SliderHandle = Color3.fromRGB(20, 20, 25),
            SliderHandleBorder = Color3.fromRGB(255, 255, 255), SliderFilledTrack = Color3.fromRGB(80, 70, 220),
            SidebarButtonBG_Inactive = Color3.fromRGB(240, 240, 245), SidebarButtonBG_Active = Color3.fromRGB(225, 225, 230),
            SidebarButtonText_Inactive = Color3.fromRGB(100, 100, 110), SidebarButtonText_Active = Color3.fromRGB(20, 20, 25),
            SidebarActiveIndicator = Color3.fromRGB(80, 70, 220),
            OutlineColor = Color3.fromRGB(200, 200, 205), HeaderBorder = Color3.fromRGB(215, 215, 220),
            ColorButtonBorder_Inactive = Color3.fromRGB(180, 180, 185), ColorButtonBorder_Active = Color3.fromRGB(80, 70, 220),
            AuthorLinkColorHex = "5046DC", HoverColorOffset = -15,
            LoadingText = Color3.fromRGB(50, 50, 55), LoadingBG = Color3.fromRGB(230, 230, 235),
            ESPNameBG = Color3.fromRGB(255, 255, 255), HealthBarBGColor = Color3.fromRGB(255, 180, 180),
            MinimizeCircleBG = Color3.fromRGB(220, 220, 225), MinimizeCircleText = Color3.fromRGB(80, 70, 220),
            FOVArrowColor = Color3.fromRGB(50, 50, 55)
        }
    },
    TWEEN_INFOS = { -- Standard tween info objects
        TWEEN_HOVER = TweenInfo.new(0.15, Enum.EasingStyle.Sine, Enum.EasingDirection.Out),
        TWEEN_FAST = TweenInfo.new(0.25, Enum.EasingStyle.Sine, Enum.EasingDirection.Out),
        TWEEN_MEDIUM = TweenInfo.new(0.35, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut),
        TWEEN_SLOW = TweenInfo.new(0.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut),
        TWEEN_LOADING_FADE = TweenInfo.new(0.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut),
        TWEEN_PROGRESS = TweenInfo.new(2, Enum.EasingStyle.Linear),
        TWEEN_GUI_OPEN = TweenInfo.new(0.4, Enum.EasingStyle.Cubic, Enum.EasingDirection.Out),
        TWEEN_MINIMIZE = TweenInfo.new(0.3, Enum.EasingStyle.Cubic, Enum.EasingDirection.Out),
    }
}

-- Initialize live settings from defaults
for key, value in pairs(HG.Config.DEFAULT_SETTINGS) do
    HG.Config.settings[key] = value
end
-- Update specific tween/constant values that depend on other config values
HG.Config.TWEEN_INFOS.TWEEN_PROGRESS = TweenInfo.new(HG.Config.CONSTANTS.LOADING_DURATION, Enum.EasingStyle.Linear)
HG.Config.CONSTANTS.MINIMIZE_CIRCLE_DEFAULT_POS = UDim2.new(
    1, -HG.Config.CONSTANTS.MINIMIZE_CIRCLE_SIZE - HG.Config.CONSTANTS.MINIMIZE_CIRCLE_PADDING,
    0, HG.Config.CONSTANTS.MINIMIZE_CIRCLE_PADDING
)


-- Script State & GUI Element References
HG.State = {
    -- Feature State
    espElements = {}, -- Stores ESP-related GUI elements for each player
    radarDots = {},   -- Stores radar dot GUI elements
    fovArrows = {},   -- Stores FOV arrow GUI elements
    aimbotConnection = nil, -- Connection for the aimbot loop
    currentAimbotTarget = nil,
    backgroundBlurEffect = nil,
    lastFullESPCheck = 0, -- Timestamp of the last full ESP update
    activeTweens = {}, -- Tracks active tweens to manage them

    -- GUI Interaction State
    storedSize = nil, storedPosition = nil, -- For minimize/restore
    isLoading = true, -- Is the GUI currently in its loading phase?
    isMouseUnlocked = false, -- Is the mouse cursor free or locked?
    activeSectionButton = nil, -- Which sidebar button is currently active
    isMinimized = false, -- Is the GUI minimized?
    currentVisibleSectionFrame = nil, -- Which main content section is visible
    draggingSlider = nil, -- Info of the slider currently being dragged
    sliders = {}, -- Stores all slider control info

    -- Minimize Circle Dragging State
    isDraggingCircle = false,
    dragStartPos = nil, -- Mouse position when drag started
    startCirclePos = nil, -- Circle position when drag started

    -- MainFrame Dragging State
    draggingMainFrame = false,
    mainFrameDragStartMouse = Vector2.zero,
    mainFrameDragStartPos = UDim2.new(),

    -- Developer Mode State
    freecamActive = false,
    originalCameraType = nil,
    originalCameraCFrame = nil,
    originalLightingAndSkySettings = {}, -- To restore after dev sky changes
    freecamRenderStepConnection = nil,
    localPlayerOriginalAnchoredState = false,

    -- GUI Element References (populated by HG.UI_InitializeActualElements)
    GUI_REFERENCES = {}
}

-- Core Utility Functions
HG.Utils = {}

-- GUI Factory Functions (for creating common UI elements)
HG.GUIFactory = {}

--[[ Main Execution Block ]]--
local success, errorMessage = pcall(function()

    -- Initialize Services
    HG.Services.Players = game:GetService("Players")
    HG.Services.UserInputService = game:GetService("UserInputService")
    HG.Services.RunService = game:GetService("RunService")
    HG.Services.Workspace = game:GetService("Workspace")
    HG.Services.StarterGui = game:GetService("StarterGui")
    HG.Services.TweenService = game:GetService("TweenService")
    HG.Services.HttpService = game:GetService("HttpService")
    HG.Services.Lighting = game:GetService("Lighting")

    -- Player and Camera References
    HG.PlayerRefs.LocalPlayer = HG.Services.Players.LocalPlayer
    HG.PlayerRefs.Camera = HG.Services.Workspace.CurrentCamera

    -- Clipboard functions (with fallbacks)
    HG.Clipboard.setclipboard = setclipboard or function(text) warn("setclipboard() is not available in this environment.") end
    HG.Clipboard.getclipboard = getclipboard or function() warn("getclipboard() is not available in this environment."); return nil end

    -- Store original camera state for potential restoration
    HG.State.originalCameraType = HG.PlayerRefs.Camera.CameraType
    HG.State.originalCameraCFrame = HG.PlayerRefs.Camera.CFrame

    -- Utility Functions
    function HG.Utils.playTween(object, tweenInfo, goal, tweenName)
        tweenName = tweenName or "DefaultTween" -- Give a default name if none provided
        if not object or not object.Parent then return nil end -- Can't tween a non-existent object

        -- Cancel existing tween on the same object with the same name
        if HG.State.activeTweens[object] and HG.State.activeTweens[object][tweenName] then
            HG.State.activeTweens[object][tweenName]:Cancel()
            HG.State.activeTweens[object][tweenName] = nil
        end

        local tween = HG.Services.TweenService:Create(object, tweenInfo, goal)
        tween:Play()

        -- Store the new tween
        if not HG.State.activeTweens[object] then
            HG.State.activeTweens[object] = {}
        end
        HG.State.activeTweens[object][tweenName] = tween

        -- Cleanup when tween completes
        local completedConn
        completedConn = tween.Completed:Connect(function()
            if HG.State.activeTweens[object] and HG.State.activeTweens[object][tweenName] == tween then
                HG.State.activeTweens[object][tweenName] = nil -- Remove from active tweens
            end
            if completedConn then
                completedConn:Disconnect() -- Disconnect this Completed signal
            end
        end)
        return tween
    end

    function HG.Utils.updateBlurEffectState()
        local BlurEffect = HG.State.backgroundBlurEffect
        local Settings = HG.Config.settings
        local State = HG.State
        local TweenInfos = HG.Config.TWEEN_INFOS

        if BlurEffect and BlurEffect.Parent then
            if State.isLoading then
                BlurEffect.Enabled = true
                BlurEffect.Size = Settings.loadingBlurSize
            else
                local targetSize = (Settings.backgroundBlurEnabled and not State.isMinimized) and Settings.blurSize or 0
                local targetEnabled = targetSize > 0
                HG.Utils.playTween(BlurEffect, TweenInfos.TWEEN_MEDIUM, { Size = targetSize }, "BlurChange")
                if BlurEffect.Enabled ~= targetEnabled then -- Only change enabled state if necessary
                    BlurEffect.Enabled = targetEnabled
                end
            end
        end
    end

    function HG.Utils.getHoverColor(originalColor)
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        local offset = theme.HoverColorOffset / 255 -- Convert 0-255 offset to 0-1 range
        return Color3.new(
            math.clamp(originalColor.R + offset, 0, 1),
            math.clamp(originalColor.G + offset, 0, 1),
            math.clamp(originalColor.B + offset, 0, 1)
        )
    end

    function HG.Utils.applyHoverEffect(button)
        if not button or HG.State.isLoading then return end
        local originalColor = button:GetAttribute("OriginalColor") or button.BackgroundColor3
        button:SetAttribute("OriginalColor", originalColor) -- Ensure it's set
        local hoverColor = HG.Utils.getHoverColor(originalColor)
        HG.Utils.playTween(button, HG.Config.TWEEN_INFOS.TWEEN_HOVER, { BackgroundColor3 = hoverColor }, "HoverEffect")
    end

    function HG.Utils.removeHoverEffect(button)
        if not button or HG.State.isLoading then return end
        local originalColor = button:GetAttribute("OriginalColor") or button.BackgroundColor3
        HG.Utils.playTween(button, HG.Config.TWEEN_INFOS.TWEEN_HOVER, { BackgroundColor3 = originalColor }, "HoverEffect")
    end

    function HG.Utils.setupHover(button)
        if not button then return end
        button:SetAttribute("OriginalColor", button.BackgroundColor3) -- Store initial color

        button.MouseEnter:Connect(function() HG.Utils.applyHoverEffect(button) end)
        button.MouseLeave:Connect(function() HG.Utils.removeHoverEffect(button) end)

        -- If BackgroundColor3 is changed by something other than hover, update OriginalColor
        button:GetPropertyChangedSignal("BackgroundColor3"):Connect(function()
            if not (HG.State.activeTweens[button] and HG.State.activeTweens[button]["HoverEffect"]) then
                button:SetAttribute("OriginalColor", button.BackgroundColor3)
            end
        end)
    end

    -- GUI Factory Functions
    function HG.GUIFactory.CreateSectionHeader(parent, layoutOrder, text)
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        
        local headerFrame = Instance.new("Frame")
        headerFrame.Name = text .. "Header"
        headerFrame.Size = UDim2.new(1, -20, 0, 35) -- Full width minus padding, fixed height
        headerFrame.BackgroundColor3 = theme.TitleBG
        headerFrame.BorderSizePixel = 0
        headerFrame.LayoutOrder = layoutOrder
        headerFrame.ClipsDescendants = true
        headerFrame.Parent = parent

        local corner = Instance.new("UICorner", headerFrame)
        corner.CornerRadius = UDim.new(0, 6)

        local headerLabel = Instance.new("TextLabel")
        headerLabel.Name = "HeaderText"
        headerLabel.Size = UDim2.new(1, -20, 1, 0) -- Inner padding for text
        headerLabel.Position = UDim2.new(0, 10, 0, 0)
        headerLabel.BackgroundTransparency = 1
        headerLabel.Font = Enum.Font.SourceSansSemibold
        headerLabel.Text = text
        headerLabel.TextColor3 = theme.HeaderText
        headerLabel.TextSize = 16
        headerLabel.TextXAlignment = Enum.TextXAlignment.Left
        headerLabel.Parent = headerFrame
        
        return headerFrame, headerLabel
    end

    function HG.GUIFactory.CreateToggleRow(parent, layoutOrder, labelText, buttonVarName)
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        
        local rowFrame = Instance.new("Frame")
        rowFrame.Name = labelText .. "Row"
        rowFrame.Size = UDim2.new(1, -20, 0, 40)
        rowFrame.BackgroundTransparency = 1
        rowFrame.LayoutOrder = layoutOrder
        rowFrame.Parent = parent

        local label = Instance.new("TextLabel")
        label.Name = "Label"
        label.Size = UDim2.new(0.65, -10, 1, 0) -- 65% width minus some padding
        label.Position = UDim2.new(0, 10, 0, 0)
        label.BackgroundTransparency = 1
        label.Text = labelText .. ":"
        label.TextColor3 = theme.SecondaryText
        label.Font = Enum.Font.SourceSans
        label.TextSize = 14
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = rowFrame

        local toggleButton = Instance.new("TextButton")
        toggleButton.Name = buttonVarName
        toggleButton.Size = UDim2.new(0.30, -10, 0, 28) -- 30% width minus padding
        toggleButton.Position = UDim2.new(0.70, 0, 0.5, -14) -- Positioned to the right, vertically centered
        toggleButton.BackgroundColor3 = theme.ButtonBG_Inactive
        toggleButton.Text = "Off"
        toggleButton.TextColor3 = theme.PrimaryText
        toggleButton.Font = Enum.Font.SourceSansSemibold
        toggleButton.TextSize = 13
        toggleButton.Parent = rowFrame

        local btnCorner = Instance.new("UICorner", toggleButton)
        btnCorner.CornerRadius = HG.Config.CONSTANTS.BUTTON_CORNER_RADIUS
        
        return rowFrame, toggleButton
    end

    function HG.GUIFactory.CreateSliderRow(parent, layoutOrder, labelText, sliderVarName, valueVarName, minVal, maxVal, defaultVal, formatString)
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        
        local sliderFrame = Instance.new("Frame")
        sliderFrame.Name = labelText .. "SliderFrame"
        sliderFrame.Size = UDim2.new(1, -20, 0, 55)
        sliderFrame.BackgroundTransparency = 1
        sliderFrame.LayoutOrder = layoutOrder
        sliderFrame.Parent = parent

        local label = Instance.new("TextLabel")
        label.Name = "Label"
        label.Size = UDim2.new(1, -65, 0, 20) -- Full width minus space for value label
        label.Position = UDim2.new(0, 10, 0, 5)
        label.BackgroundTransparency = 1
        label.Text = labelText .. ":"
        label.TextColor3 = theme.SecondaryText
        label.Font = Enum.Font.SourceSans
        label.TextSize = 13
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = sliderFrame

        local valueLabel = Instance.new("TextLabel")
        valueLabel.Name = valueVarName
        valueLabel.Size = UDim2.new(0, 55, 0, 20) -- Fixed width for value
        valueLabel.Position = UDim2.new(1, -65, 0, 5) -- Positioned at the right end
        valueLabel.BackgroundTransparency = 1
        valueLabel.Text = string.format(formatString or "%s", defaultVal)
        valueLabel.TextColor3 = theme.PrimaryText
        valueLabel.Font = Enum.Font.SourceSansSemibold
        valueLabel.TextSize = 13
        valueLabel.TextXAlignment = Enum.TextXAlignment.Right
        valueLabel.Parent = sliderFrame

        local track = Instance.new("Frame")
        track.Name = "Track"
        track.Size = UDim2.new(1, -20, 0, 10) -- Full width minus padding, fixed height for track
        track.Position = UDim2.new(0, 10, 0, 30) -- Positioned below label/value
        track.BackgroundColor3 = theme.SliderTrack
        track.BorderSizePixel = 0
        track.Parent = sliderFrame
        local trackCorner = Instance.new("UICorner", track)
        trackCorner.CornerRadius = HG.Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS

        local filledTrack = Instance.new("Frame")
        filledTrack.Name = "FilledTrack"
        filledTrack.Size = UDim2.new(0, 0, 1, 0) -- Starts with zero width
        filledTrack.BackgroundColor3 = theme.SliderFilledTrack
        filledTrack.BorderSizePixel = 0
        filledTrack.ZIndex = track.ZIndex + 1
        filledTrack.Parent = track
        local filledTrackCorner = Instance.new("UICorner", filledTrack)
        filledTrackCorner.CornerRadius = HG.Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS

        local handle = Instance.new("TextButton") -- TextButton for input, but looks like a frame
        handle.Name = sliderVarName
        handle.Size = UDim2.fromOffset(20, 20) -- Square handle
        handle.Position = UDim2.new(0, 0, 0.5, 0) -- Centered vertically on track, starts at left
        handle.AnchorPoint = Vector2.new(0.5, 0.5)
        handle.BackgroundColor3 = theme.SliderHandle
        handle.BorderSizePixel = 0
        handle.Text = "" -- No text
        handle.ZIndex = track.ZIndex + 2 -- Above track and filled track
        handle.Parent = track
        -- Consider adding a UICorner for the handle if you want it round/custom shaped

        return sliderFrame, handle, valueLabel, track, filledTrack
    end

    function HG.GUIFactory.CreateColorButton(parent, order, color, name)
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        
        local btn = Instance.new("TextButton")
        btn.Name = name .. "ColorButton"
        btn.Size = UDim2.new(0, 24, 0, 24) -- Small square button
        btn.BackgroundColor3 = color
        btn.BorderSizePixel = 0
        btn.Text = ""
        btn.LayoutOrder = order
        btn.Parent = parent

        local btnCorner = Instance.new("UICorner", btn)
        btnCorner.CornerRadius = HG.Config.CONSTANTS.BUTTON_CORNER_RADIUS

        local stroke = Instance.new("UIStroke", btn)
        stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        stroke.Color = theme.ColorButtonBorder_Inactive
        stroke.Thickness = 1.5
        
        return btn
    end

    function HG.GUIFactory.CreateFOVColorButton(parent, order, color, name) -- Similar to CreateColorButton, could be merged if identical
        local theme = HG.Config.THEME_SETTINGS[HG.Config.settings.currentTheme] or HG.Config.THEME_SETTINGS.Dark
        
        local btn = Instance.new("TextButton")
        btn.Name = name .. "FOVColorButton"
        btn.Size = UDim2.new(0, 24, 0, 24)
        btn.BackgroundColor3 = color
        btn.BorderSizePixel = 0
        btn.Text = ""
        btn.LayoutOrder = order
        btn.Parent = parent

        local btnCorner = Instance.new("UICorner", btn)
        btnCorner.CornerRadius = HG.Config.CONSTANTS.BUTTON_CORNER_RADIUS
        
        local stroke = Instance.new("UIStroke", btn)
        stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        stroke.Color = theme.ColorButtonBorder_Inactive -- Default to inactive border
        stroke.Thickness = 1.5
        
        return btn
    end

    -- Module for UI Element Instantiation
    -- This is where all the GUI parts are actually created and put together.
    HG.UI_InitializeActualElements = function()
        local GUI = HG.State.GUI_REFERENCES
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local Config = HG.Config
        local Factory = HG.GUIFactory
        local currentThemeSettings = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark

        -- ScreenGui & Initial Overlay Elements
        -- Make sure we have a clean slate for our ScreenGui
        local oldScreenGuiName = Config.CONSTANTS.BLUR_EFFECT_NAME:gsub("_Blur_", "_GUI_")
        GUI.ScreenGui = PlayerRefs.LocalPlayer:FindFirstChild("PlayerGui"):FindFirstChild(oldScreenGuiName)
        if GUI.ScreenGui then GUI.ScreenGui:Destroy() end -- Destroy existing if any

        GUI.ScreenGui = Instance.new("ScreenGui")
        GUI.ScreenGui.Name = oldScreenGuiName
        GUI.ScreenGui.Parent = PlayerRefs.LocalPlayer:WaitForChild("PlayerGui", 10) -- Wait for PlayerGui to be available
        GUI.ScreenGui.ResetOnSpawn = false -- Important: Keep GUI on respawn
        GUI.ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        GUI.ScreenGui.DisplayOrder = 9999999 -- Try to render on top
        GUI.ScreenGui.IgnoreGuiInset = true -- Cover the entire screen, ignore top bar inset

        GUI.KonosubaWatermarkLabel = Instance.new("TextLabel")
        GUI.KonosubaWatermarkLabel.Name = "KonosubaWatermark"
        GUI.KonosubaWatermarkLabel.Parent = GUI.ScreenGui
        GUI.KonosubaWatermarkLabel.AnchorPoint = Vector2.new(0, 0)
        GUI.KonosubaWatermarkLabel.Position = UDim2.new(0, 10, 0, 10) -- Top-left corner
        GUI.KonosubaWatermarkLabel.Size = UDim2.new(0, 0, 0, 0) -- Let it size automatically
        GUI.KonosubaWatermarkLabel.AutomaticSize = Enum.AutomaticSize.XY
        GUI.KonosubaWatermarkLabel.BackgroundTransparency = 1
        GUI.KonosubaWatermarkLabel.Font = Enum.Font.SourceSansBold
        GUI.KonosubaWatermarkLabel.Text = "Konosuba"
        GUI.KonosubaWatermarkLabel.TextColor3 = Color3.new(1,1,1) -- Default white, theme might override
        GUI.KonosubaWatermarkLabel.TextTransparency = 0.7
        GUI.KonosubaWatermarkLabel.TextStrokeColor3 = Color3.fromRGB(0,0,0)
        GUI.KonosubaWatermarkLabel.TextStrokeTransparency = 0.7
        GUI.KonosubaWatermarkLabel.TextSize = 22
        GUI.KonosubaWatermarkLabel.ZIndex = 2 -- Above most things, but below main GUI window potentially

        GUI.FreecamWatermarkLabel = Instance.new("TextLabel")
        GUI.FreecamWatermarkLabel.Name = "FreecamWatermark"
        GUI.FreecamWatermarkLabel.Parent = GUI.ScreenGui
        GUI.FreecamWatermarkLabel.AnchorPoint = Vector2.new(0.5, 0) -- Centered horizontally, at the top
        GUI.FreecamWatermarkLabel.Position = UDim2.new(0.5, 0, 0, 10)
        GUI.FreecamWatermarkLabel.Size = UDim2.new(0, 400, 0, 20) -- Fixed size
        GUI.FreecamWatermarkLabel.BackgroundTransparency = 1
        GUI.FreecamWatermarkLabel.Font = Enum.Font.SourceSansSemibold
        GUI.FreecamWatermarkLabel.Text = "Freecam Active | Press 'F2' to unlock mouse & interact with GUI"
        GUI.FreecamWatermarkLabel.TextColor3 = Color3.new(1,1,1)
        GUI.FreecamWatermarkLabel.TextStrokeColor3 = Color3.new(0,0,0)
        GUI.FreecamWatermarkLabel.TextStrokeTransparency = 0.4
        GUI.FreecamWatermarkLabel.TextSize = 16
        GUI.FreecamWatermarkLabel.ZIndex = 10000 -- Very high ZIndex
        GUI.FreecamWatermarkLabel.Visible = false -- Only visible when freecam is active

        -- Loading screen elements
        GUI.LoadingBackgroundOverlay = Instance.new("Frame")
        GUI.LoadingBackgroundOverlay.Name = "LoadingBackgroundOverlay"
        GUI.LoadingBackgroundOverlay.Parent = GUI.ScreenGui
        GUI.LoadingBackgroundOverlay.BackgroundColor3 = currentThemeSettings.LoadingBG
        GUI.LoadingBackgroundOverlay.BackgroundTransparency = Config.settings.loadingBackgroundTransparency
        GUI.LoadingBackgroundOverlay.BorderSizePixel = 0
        GUI.LoadingBackgroundOverlay.Size = UDim2.new(1,0,1,0) -- Full screen
        GUI.LoadingBackgroundOverlay.Position = UDim2.new(0,0,0,0)
        GUI.LoadingBackgroundOverlay.ZIndex = 999 -- Below loading text/bar

        GUI.LoadingFrame = Instance.new("Frame")
        GUI.LoadingFrame.Name = "LoadingFrame"
        GUI.LoadingFrame.Parent = GUI.ScreenGui
        GUI.LoadingFrame.BackgroundTransparency = 1 -- Transparent, just a container
        GUI.LoadingFrame.BorderSizePixel = 0
        GUI.LoadingFrame.Size = UDim2.new(1,0,1,0)
        GUI.LoadingFrame.Position = UDim2.new(0,0,0,0)
        GUI.LoadingFrame.ZIndex = 1000 -- Above overlay

        local LoadingText = Instance.new("TextLabel")
        LoadingText.Name = "LoadingText"
        LoadingText.Parent = GUI.LoadingFrame
        LoadingText.AnchorPoint = Vector2.new(0.5,0.5)
        LoadingText.Position = UDim2.new(0.5,0,0.45,0) -- Slightly above center
        LoadingText.Size = UDim2.new(0,250,0,30)
        LoadingText.BackgroundTransparency = 1
        LoadingText.Font = Enum.Font.SourceSansSemibold
        LoadingText.Text = "Loading Konosuba GUI..."
        LoadingText.TextColor3 = currentThemeSettings.LoadingText
        LoadingText.TextSize = 18
        LoadingText.TextXAlignment = Enum.TextXAlignment.Center
        LoadingText.TextStrokeColor3 = Color3.new(0,0,0)
        LoadingText.TextStrokeTransparency = 0.5

        local LoadingProgressBarBG = Instance.new("Frame")
        LoadingProgressBarBG.Name = "LoadingProgressBarBG"
        LoadingProgressBarBG.Parent = GUI.LoadingFrame
        LoadingProgressBarBG.AnchorPoint = Vector2.new(0.5,0.5)
        LoadingProgressBarBG.Position = UDim2.new(0.5,0,0.55,0) -- Below text
        LoadingProgressBarBG.Size = UDim2.new(0,280,0,10)
        LoadingProgressBarBG.BackgroundColor3 = currentThemeSettings.SliderTrack -- Use slider track color for consistency
        LoadingProgressBarBG.BorderSizePixel = 0
        Instance.new("UICorner", LoadingProgressBarBG).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS

        local LoadingProgressBarFG = Instance.new("Frame")
        LoadingProgressBarFG.Name = "LoadingProgressBarFG"
        LoadingProgressBarFG.Parent = LoadingProgressBarBG -- Child of the background bar
        LoadingProgressBarFG.Size = UDim2.new(0,0,1,0) -- Starts at 0 width
        LoadingProgressBarFG.BackgroundColor3 = currentThemeSettings.AccentColor
        LoadingProgressBarFG.BorderSizePixel = 0
        Instance.new("UICorner", LoadingProgressBarFG).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS

        local LoadingAuthorLabel = Instance.new("TextLabel")
        LoadingAuthorLabel.Name = "LoadingAuthorLabel"
        LoadingAuthorLabel.Parent = GUI.LoadingFrame
        LoadingAuthorLabel.AnchorPoint = Vector2.new(0.5,0.5)
        LoadingAuthorLabel.Position = UDim2.new(0.5,0,0.58,15) -- Below progress bar
        LoadingAuthorLabel.Size = UDim2.new(0,200,0,20)
        LoadingAuthorLabel.BackgroundTransparency = 1
        LoadingAuthorLabel.Font = Enum.Font.SourceSans
        LoadingAuthorLabel.Text = "@CrownComp" -- Author credit
        LoadingAuthorLabel.TextColor3 = currentThemeSettings.AccentColor
        LoadingAuthorLabel.TextSize = 14
        LoadingAuthorLabel.TextXAlignment = Enum.TextXAlignment.Center
        LoadingAuthorLabel.TextStrokeColor3 = Color3.new(0,0,0)
        LoadingAuthorLabel.TextStrokeTransparency = 0.6
        
        -- Setup background blur effect
        HG.State.backgroundBlurEffect = Services.Lighting:FindFirstChild(Config.CONSTANTS.BLUR_EFFECT_NAME)
        if not HG.State.backgroundBlurEffect then
            HG.State.backgroundBlurEffect = Instance.new("BlurEffect")
            HG.State.backgroundBlurEffect.Name = Config.CONSTANTS.BLUR_EFFECT_NAME
            HG.State.backgroundBlurEffect.Parent = Services.Lighting
        end
        HG.State.backgroundBlurEffect.Size = Config.settings.loadingBlurSize
        HG.State.backgroundBlurEffect.Enabled = true -- Enabled during loading

        -- Main Window Structure
        local mainFrameWidth, mainFrameHeight = 520, 580
        local sidebarWidth, titleBarHeight = 120, 40
        
        GUI.MainFrame = Instance.new("Frame")
        GUI.MainFrame.Name = "KonosubaMainFrame"
        GUI.MainFrame.Size = UDim2.new(0, mainFrameWidth, 0, mainFrameHeight)
        GUI.MainFrame.Position = UDim2.new(0.5, -mainFrameWidth / 2, 0.5, -mainFrameHeight / 2) -- Centered
        GUI.MainFrame.BackgroundColor3 = currentThemeSettings.MainBG
        GUI.MainFrame.BackgroundTransparency = 1 -- Initially transparent, fades in
        GUI.MainFrame.BorderSizePixel = 0
        GUI.MainFrame.ClipsDescendants = true
        GUI.MainFrame.Parent = GUI.ScreenGui
        GUI.MainFrame.Active = false -- Not active for input itself, children are
        GUI.MainFrame.Draggable = false -- Title bar will handle dragging
        local mainFrameCorner = Instance.new("UICorner", GUI.MainFrame)
        mainFrameCorner.CornerRadius = UDim.new(0, 25) -- Nice rounded corners
        
        GUI.TitleFrame = Instance.new("Frame")
        GUI.TitleFrame.Name = "TitleFrame"
        GUI.TitleFrame.Size = UDim2.new(1, 0, 0, titleBarHeight) -- Full width, fixed height
        GUI.TitleFrame.Position = UDim2.new(0, 0, 0, 0) -- At the top of MainFrame
        GUI.TitleFrame.BackgroundColor3 = currentThemeSettings.TitleBG
        GUI.TitleFrame.BackgroundTransparency = 0 -- Solid by default (unless MainFrame is transparent)
        GUI.TitleFrame.BorderSizePixel = 0
        GUI.TitleFrame.Active = true -- Allows dragging
        GUI.TitleFrame.Draggable = false -- Will be handled by script
        GUI.TitleFrame.Parent = GUI.MainFrame

        GUI.Title = Instance.new("TextLabel")
        GUI.Title.Name = "GUITitle" -- Changed from "Title" to avoid conflict if there was a global "Title"
        GUI.Title.Size = UDim2.new(1, -(titleBarHeight * 2 + 20), 1, 0) -- Width accommodates buttons
        GUI.Title.Position = UDim2.new(0, 15, 0, 0) -- Padded from left
        GUI.Title.BackgroundTransparency = 1
        GUI.Title.Text = "Konosuba GUI"
        GUI.Title.TextColor3 = currentThemeSettings.PrimaryText
        GUI.Title.Font = Enum.Font.SourceSansSemibold
        GUI.Title.TextSize = 18
        GUI.Title.TextXAlignment = Enum.TextXAlignment.Left
        GUI.Title.Parent = GUI.TitleFrame

        local buttonSize = titleBarHeight - 12 -- Size for minimize/close buttons
        GUI.ToggleButton = Instance.new("TextButton")
        GUI.ToggleButton.Name = "MinimizeButton"
        GUI.ToggleButton.Size = UDim2.fromOffset(buttonSize, buttonSize)
        GUI.ToggleButton.Position = UDim2.new(1, -(buttonSize * 2 + 18), 0.5, -buttonSize / 2) -- Right side, before close
        GUI.ToggleButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Minimize
        GUI.ToggleButton.Text = "-" -- Minimize symbol
        GUI.ToggleButton.TextColor3 = currentThemeSettings.ButtonText
        GUI.ToggleButton.Font = Enum.Font.SourceSansBold
        GUI.ToggleButton.TextSize = 22
        GUI.ToggleButton.Parent = GUI.TitleFrame
        Instance.new("UICorner", GUI.ToggleButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS

        GUI.CloseButton = Instance.new("TextButton")
        GUI.CloseButton.Name = "CloseButton"
        GUI.CloseButton.Size = UDim2.fromOffset(buttonSize, buttonSize)
        GUI.CloseButton.Position = UDim2.new(1, -(buttonSize + 10), 0.5, -buttonSize / 2) -- Far right
        GUI.CloseButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Close
        GUI.CloseButton.Text = "X" -- Close symbol
        GUI.CloseButton.TextColor3 = currentThemeSettings.ButtonText
        GUI.CloseButton.Font = Enum.Font.SourceSansBold
        GUI.CloseButton.TextSize = 18
        GUI.CloseButton.Parent = GUI.TitleFrame
        Instance.new("UICorner", GUI.CloseButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS
        
        GUI.SidebarFrame = Instance.new("Frame")
        GUI.SidebarFrame.Name = "Sidebar"
        GUI.SidebarFrame.Size = UDim2.new(0, sidebarWidth, 1, -titleBarHeight) -- Fixed width, full height minus title
        GUI.SidebarFrame.Position = UDim2.new(0, 0, 0, titleBarHeight) -- Below title bar
        GUI.SidebarFrame.BackgroundColor3 = currentThemeSettings.SidebarBG
        GUI.SidebarFrame.BackgroundTransparency = 0
        GUI.SidebarFrame.BorderSizePixel = 0
        GUI.SidebarFrame.Parent = GUI.MainFrame

        local SidebarPadding = Instance.new("UIPadding")
        SidebarPadding.PaddingTop = UDim.new(0, 15)
        SidebarPadding.PaddingBottom = UDim.new(0, 15)
        SidebarPadding.PaddingLeft = UDim.new(0, 10)
        SidebarPadding.PaddingRight = UDim.new(0, 10)
        SidebarPadding.Parent = GUI.SidebarFrame

        local SidebarLayout = Instance.new("UIListLayout")
        SidebarLayout.Padding = UDim.new(0, 10) -- Spacing between sidebar buttons
        SidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder
        SidebarLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
        SidebarLayout.Parent = GUI.SidebarFrame

        -- Helper function for creating sidebar buttons
        local function CreateSidebarButton(text, order)
            local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
            local btn = Instance.new("TextButton")
            btn.Name = text .. "SectionButton"
            btn.Size = UDim2.new(1, 0, 0, 40) -- Full width of sidebar (respecting padding), fixed height
            btn.BackgroundColor3 = theme.SidebarButtonBG_Inactive
            btn.TextColor3 = theme.SidebarButtonText_Inactive
            btn.Text = text
            btn.Font = Enum.Font.SourceSansSemibold
            btn.TextSize = 15
            btn.LayoutOrder = order
            btn.Parent = GUI.SidebarFrame
            Instance.new("UICorner", btn).CornerRadius = Config.CONSTANTS.MODERN_UI_ELEMENT_CORNER_RADIUS

            local activeIndicator = Instance.new("Frame", btn) -- Visual cue for active button
            activeIndicator.Name = "ActiveIndicator"
            activeIndicator.Size = UDim2.new(0, 4, 0.6, 0) -- Thin bar on the left
            activeIndicator.Position = UDim2.new(0, 5, 0.2, 0) -- Padded from left, vertically centered
            activeIndicator.BackgroundColor3 = theme.SidebarActiveIndicator
            activeIndicator.BorderSizePixel = 0
            Instance.new("UICorner", activeIndicator).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS
            activeIndicator.Visible = false -- Only visible when button is active
            return btn
        end
        GUI.ESPSectionButton = CreateSidebarButton("ESP", 1)
        GUI.AimSectionButton = CreateSidebarButton("Aim", 2)
        GUI.ConfigSectionButton = CreateSidebarButton("Config", 3)
        GUI.DevSectionButton = CreateSidebarButton("Dev", 4)
        
        GUI.MainContentAreaFrame = Instance.new("Frame")
        GUI.MainContentAreaFrame.Name = "MainContentArea"
        GUI.MainContentAreaFrame.Size = UDim2.new(1, -sidebarWidth, 1, -titleBarHeight) -- Fills remaining space
        GUI.MainContentAreaFrame.Position = UDim2.new(0, sidebarWidth, 0, titleBarHeight) -- To the right of sidebar, below title
        GUI.MainContentAreaFrame.BackgroundColor3 = currentThemeSettings.ContentAreaBG
        GUI.MainContentAreaFrame.BackgroundTransparency = 0
        GUI.MainContentAreaFrame.BorderSizePixel = 0
        GUI.MainContentAreaFrame.ClipsDescendants = true -- Important for scrolling frames
        GUI.MainContentAreaFrame.Parent = GUI.MainFrame

        local ContentPadding = Instance.new("UIPadding") -- Padding for the content area itself
        ContentPadding.PaddingTop = UDim.new(0, 10)
        ContentPadding.PaddingBottom = UDim.new(0, 10)
        ContentPadding.PaddingLeft = UDim.new(0, 10)
        ContentPadding.PaddingRight = UDim.new(0, 10)
        ContentPadding.Parent = GUI.MainContentAreaFrame

        -- Section UI - ESP
        GUI.ESPSection = Instance.new("Frame")
        GUI.ESPSection.Name = "ESPSection"
        GUI.ESPSection.Size = UDim2.new(1,0,1,0) -- Full size of parent (MainContentAreaFrame)
        GUI.ESPSection.BackgroundTransparency = 1 -- Container frame
        GUI.ESPSection.Visible = true -- ESP is default visible section
        GUI.ESPSection.Parent = GUI.MainContentAreaFrame
        
        GUI.ESPScrollFrame = Instance.new("ScrollingFrame")
        GUI.ESPScrollFrame.Name = "ESPScrollFrameContent"
        GUI.ESPScrollFrame.Size = UDim2.new(1,0,1,0)
        GUI.ESPScrollFrame.Position = UDim2.new(0,0,0,0)
        GUI.ESPScrollFrame.BackgroundColor3 = currentThemeSettings.ContainerBG
        GUI.ESPScrollFrame.BackgroundTransparency = 1 -- Will be faded in
        GUI.ESPScrollFrame.BorderSizePixel = 0
        GUI.ESPScrollFrame.CanvasSize = UDim2.new(0,0,0,0) -- Will be set by AutomaticCanvasSize
        GUI.ESPScrollFrame.ScrollBarThickness = 8
        GUI.ESPScrollFrame.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
        GUI.ESPScrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y -- Key for scrolling
        GUI.ESPScrollFrame.Parent = GUI.ESPSection
        Instance.new("UICorner", GUI.ESPScrollFrame).CornerRadius = Config.CONSTANTS.MODERN_UI_ELEMENT_CORNER_RADIUS
        local espScrollStroke = Instance.new("UIStroke", GUI.ESPScrollFrame)
        espScrollStroke.Color = currentThemeSettings.OutlineColor
        
        local ESPListLayout = Instance.new("UIListLayout")
        ESPListLayout.Padding = UDim.new(0,10) -- Spacing between elements in ESP section
        ESPListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ESPListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
        ESPListLayout.Parent = GUI.ESPScrollFrame
        
        local ESPInternalPadding = Instance.new("UIPadding") -- Padding inside the scroll frame
        ESPInternalPadding.PaddingTop = UDim.new(0,10)
        ESPInternalPadding.PaddingBottom = UDim.new(0,10)
        ESPInternalPadding.PaddingLeft = UDim.new(0,10)
        ESPInternalPadding.PaddingRight = UDim.new(0,10)
        ESPInternalPadding.Parent = GUI.ESPScrollFrame
        
        Factory.CreateSectionHeader(GUI.ESPScrollFrame, 1, "Player Visuals")
        local _, temp_PlayerESPToggle = Factory.CreateToggleRow(GUI.ESPScrollFrame, 2, "Player ESP", "PlayerESPToggle")
        GUI.PlayerESPToggle = temp_PlayerESPToggle
        local _, temp_ESPLinesToggle = Factory.CreateToggleRow(GUI.ESPScrollFrame, 3, "ESP Lines", "ESPLinesToggle")
        GUI.ESPLinesToggle = temp_ESPLinesToggle
        local _, temp_ShowHealthToggle = Factory.CreateToggleRow(GUI.ESPScrollFrame, 4, "Show Health", "ShowHealthToggle")
        GUI.ShowHealthToggle = temp_ShowHealthToggle
        local _, temp_SkeletonESPToggle = Factory.CreateToggleRow(GUI.ESPScrollFrame, 5, "Skeleton ESP", "SkeletonESPToggle")
        GUI.SkeletonESPToggle = temp_SkeletonESPToggle
        
        local ESPColorFrame = Instance.new("Frame")
        ESPColorFrame.Name = "ESPColorSelector"
        ESPColorFrame.Size = UDim2.new(1, -20, 0, 45)
        ESPColorFrame.BackgroundTransparency = 1
        ESPColorFrame.LayoutOrder = 6
        ESPColorFrame.Parent = GUI.ESPScrollFrame

        local ColorLabel = Instance.new("TextLabel", ESPColorFrame)
        ColorLabel.Name = "Label"
        ColorLabel.Size = UDim2.new(1, -20, 0, 20)
        ColorLabel.Position = UDim2.new(0, 10, 0, 0)
        ColorLabel.BackgroundTransparency = 1
        ColorLabel.Text = "ESP Color (Box/Line/Skeleton):"
        ColorLabel.TextColor3 = currentThemeSettings.SecondaryText
        ColorLabel.Font = Enum.Font.SourceSans
        ColorLabel.TextSize = 13
        ColorLabel.TextXAlignment = Enum.TextXAlignment.Left

        local ColorButtonsFrame = Instance.new("Frame", ESPColorFrame)
        ColorButtonsFrame.Name = "ESPColorButtonsContainer"
        ColorButtonsFrame.Size = UDim2.new(1, -20, 0, 24)
        ColorButtonsFrame.Position = UDim2.new(0, 10, 0, 20) -- Below the label
        ColorButtonsFrame.BackgroundTransparency = 1
        
        local ColorListLayout = Instance.new("UIListLayout", ColorButtonsFrame)
        ColorListLayout.FillDirection = Enum.FillDirection.Horizontal
        ColorListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
        ColorListLayout.VerticalAlignment = Enum.VerticalAlignment.Center
        ColorListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ColorListLayout.Padding = UDim.new(0, 8) -- Spacing between color buttons
        
        GUI.RedColorButton = Factory.CreateColorButton(ColorButtonsFrame, 1, Color3.fromRGB(255,80,80), "Red")
        GUI.WhiteColorButton = Factory.CreateColorButton(ColorButtonsFrame, 2, Color3.fromRGB(240,240,240), "White")
        GUI.GreenColorButton = Factory.CreateColorButton(ColorButtonsFrame, 3, Color3.fromRGB(80,255,100), "Green")
        GUI.BlueColorButton = Factory.CreateColorButton(ColorButtonsFrame, 4, Color3.fromRGB(80,150,255), "Blue")
        GUI.YellowColorButton = Factory.CreateColorButton(ColorButtonsFrame, 5, Color3.fromRGB(255,230,80), "Yellow")
        GUI.PinkColorButton = Factory.CreateColorButton(ColorButtonsFrame, 6, Color3.fromRGB(255,130,200), "Pink")
        
        local _, espDistH, espDistV, espDistT, espDistFT = Factory.CreateSliderRow(GUI.ESPScrollFrame, 7, "ESP Max Distance", "ESPDistanceSliderHandle", "ESPDistanceSliderValue", 50, 1000, Config.settings.espMaxDistance, "%d studs")
        GUI.ESPDistanceSliderHandle = espDistH
        GUI.ESPDistanceSliderValue = espDistV
        GUI.ESPDistanceSliderTrack = espDistT
        GUI.ESPDistanceFilledTrack = espDistFT
        
        Factory.CreateSectionHeader(GUI.ESPScrollFrame, 8, "Radar Settings")
        local _, temp_ESPRadarToggle = Factory.CreateToggleRow(GUI.ESPScrollFrame, 9, "Enable Radar", "ESPRadarToggle")
        GUI.ESPRadarToggle = temp_ESPRadarToggle
        local _, radarSzH, radarSzV, radarSzT, radarSzFT = Factory.CreateSliderRow(GUI.ESPScrollFrame, 10, "Radar Size", "RadarSizeSliderHandle", "RadarSizeSliderValue", 70, 300, Config.settings.espRadarSize, "%d px")
        GUI.RadarSizeSliderHandle = radarSzH
        GUI.RadarSizeSliderValue = radarSzV
        GUI.RadarSizeSliderTrack = radarSzT
        GUI.RadarSizeFilledTrack = radarSzFT
        local _, radarRngH, radarRngV, radarRngT, radarRngFT = Factory.CreateSliderRow(GUI.ESPScrollFrame, 11, "Radar Range", "RadarRangeSliderHandle", "RadarRangeSliderValue", 50, 500, Config.settings.espRadarRange, "%d studs")
        GUI.RadarRangeSliderHandle = radarRngH
        GUI.RadarRangeSliderValue = radarRngV
        GUI.RadarRangeSliderTrack = radarRngT
        GUI.RadarRangeFilledTrack = radarRngFT

        -- Section UI - Aim
        GUI.AimSection = Instance.new("Frame")
        GUI.AimSection.Name = "AimSection"
        GUI.AimSection.Size = UDim2.new(1,0,1,0)
        GUI.AimSection.BackgroundTransparency = 1
        GUI.AimSection.Visible = false -- Hidden by default
        GUI.AimSection.Parent = GUI.MainContentAreaFrame
        
        GUI.AimScrollFrame = Instance.new("ScrollingFrame")
        GUI.AimScrollFrame.Name = "AimScrollFrameContent"
        GUI.AimScrollFrame.Size = UDim2.new(1,0,1,0)
        GUI.AimScrollFrame.Position = UDim2.new(0,0,0,0)
        GUI.AimScrollFrame.BackgroundColor3 = currentThemeSettings.ContainerBG
        GUI.AimScrollFrame.BackgroundTransparency = 1
        GUI.AimScrollFrame.BorderSizePixel = 0
        GUI.AimScrollFrame.CanvasSize = UDim2.new(0,0,0,0)
        GUI.AimScrollFrame.ScrollBarThickness = 8
        GUI.AimScrollFrame.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
        GUI.AimScrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
        GUI.AimScrollFrame.Parent = GUI.AimSection
        Instance.new("UICorner", GUI.AimScrollFrame).CornerRadius = Config.CONSTANTS.MODERN_UI_ELEMENT_CORNER_RADIUS
        local aimScrollStroke = Instance.new("UIStroke", GUI.AimScrollFrame)
        aimScrollStroke.Color = currentThemeSettings.OutlineColor
        
        local AimListLayout = Instance.new("UIListLayout", GUI.AimScrollFrame)
        AimListLayout.Padding = UDim.new(0,10)
        AimListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        AimListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
        
        local AimInternalPadding = Instance.new("UIPadding", GUI.AimScrollFrame)
        AimInternalPadding.PaddingTop = UDim.new(0,10)
        AimInternalPadding.PaddingBottom = UDim.new(0,10)
        AimInternalPadding.PaddingLeft = UDim.new(0,10)
        AimInternalPadding.PaddingRight = UDim.new(0,10)
        
        Factory.CreateSectionHeader(GUI.AimScrollFrame, 1, "Aimbot Core")
        local _, temp_AimbotToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 2, "Enable Aimbot", "AimbotToggle")
        GUI.AimbotToggle = temp_AimbotToggle
        
        local AimbotKeyFrame = Instance.new("Frame", GUI.AimScrollFrame)
        AimbotKeyFrame.Name = "AimbotKeySelector"
        AimbotKeyFrame.Size = UDim2.new(1, -20, 0, 40)
        AimbotKeyFrame.BackgroundTransparency = 1
        AimbotKeyFrame.LayoutOrder = 3
        
        local AimbotKeyLabel = Instance.new("TextLabel", AimbotKeyFrame)
        AimbotKeyLabel.Name = "Label"
        AimbotKeyLabel.Size = UDim2.new(0.5, -10, 1, 0)
        AimbotKeyLabel.Position = UDim2.new(0, 10, 0, 0)
        AimbotKeyLabel.BackgroundTransparency = 1
        AimbotKeyLabel.Text = "Aimbot Key:"
        AimbotKeyLabel.TextColor3 = currentThemeSettings.SecondaryText
        AimbotKeyLabel.Font = Enum.Font.SourceSans
        AimbotKeyLabel.TextSize = 14
        AimbotKeyLabel.TextXAlignment = Enum.TextXAlignment.Left
        
        local AimbotKeyButtonFrame = Instance.new("Frame", AimbotKeyFrame) -- Container for key selection buttons
        AimbotKeyButtonFrame.Name = "AimbotKeyButtonsContainer"
        AimbotKeyButtonFrame.Size = UDim2.new(0.5, -10, 1, 0)
        AimbotKeyButtonFrame.Position = UDim2.new(0.5, 10, 0, 0) -- Positioned to the right of label
        AimbotKeyButtonFrame.BackgroundTransparency = 1
        
        local AimbotKeyButtonLayout = Instance.new("UIListLayout", AimbotKeyButtonFrame)
        AimbotKeyButtonLayout.FillDirection = Enum.FillDirection.Horizontal
        AimbotKeyButtonLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right -- Align buttons to the right
        AimbotKeyButtonLayout.VerticalAlignment = Enum.VerticalAlignment.Center
        AimbotKeyButtonLayout.SortOrder = Enum.SortOrder.LayoutOrder
        AimbotKeyButtonLayout.Padding = UDim.new(0, 8)
        
        GUI.AimbotKeyLShiftButton = Instance.new("TextButton", AimbotKeyButtonFrame)
        GUI.AimbotKeyLShiftButton.Name = "AimbotKeyLShiftButton"
        GUI.AimbotKeyLShiftButton.Size = UDim2.new(0, 70, 0, 28)
        GUI.AimbotKeyLShiftButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Active -- Default active
        GUI.AimbotKeyLShiftButton.TextColor3 = currentThemeSettings.ButtonText
        GUI.AimbotKeyLShiftButton.Font = Enum.Font.SourceSansSemibold
        GUI.AimbotKeyLShiftButton.Text = "LShift"
        GUI.AimbotKeyLShiftButton.TextSize = 13
        GUI.AimbotKeyLShiftButton.LayoutOrder = 1
        Instance.new("UICorner", GUI.AimbotKeyLShiftButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS
        
        GUI.AimbotKeyRMBButton = Instance.new("TextButton", AimbotKeyButtonFrame)
        GUI.AimbotKeyRMBButton.Name = "AimbotKeyRMBButton"
        GUI.AimbotKeyRMBButton.Size = UDim2.new(0, 70, 0, 28)
        GUI.AimbotKeyRMBButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Inactive
        GUI.AimbotKeyRMBButton.TextColor3 = currentThemeSettings.PrimaryText
        GUI.AimbotKeyRMBButton.Font = Enum.Font.SourceSansSemibold
        GUI.AimbotKeyRMBButton.Text = "RMB"
        GUI.AimbotKeyRMBButton.TextSize = 13
        GUI.AimbotKeyRMBButton.LayoutOrder = 2
        Instance.new("UICorner", GUI.AimbotKeyRMBButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS

        local _, temp_TargetToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 4, "Target Head", "TargetToggle")
        GUI.TargetToggle = temp_TargetToggle
        local _, temp_AimbotWallCheckToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 5, "Wall Check", "AimbotWallCheckToggle")
        GUI.AimbotWallCheckToggle = temp_AimbotWallCheckToggle
        
        Factory.CreateSectionHeader(GUI.AimScrollFrame, 6, "Aimbot Visuals")
        local _, temp_FOVToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 7, "Show Aim FOV", "FOVToggle")
        GUI.FOVToggle = temp_FOVToggle
        local _, temp_CrosshairToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 8, "Show Crosshair", "CrosshairToggle")
        GUI.CrosshairToggle = temp_CrosshairToggle
        local _, temp_OffScreenArrowsToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 9, "Off-Screen Arrows", "OffScreenArrowsToggle")
        GUI.OffScreenArrowsToggle = temp_OffScreenArrowsToggle
        local _, temp_AimbotTargetLineToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 10, "Show Target Line", "AimbotTargetLineToggle")
        GUI.AimbotTargetLineToggle = temp_AimbotTargetLineToggle

        local _, fovH, fovV, fovT, fovFT = Factory.CreateSliderRow(GUI.AimScrollFrame, 11, "Aim FOV Diameter", "FOVSliderHandle", "FOVSliderValue", 50, 800, Config.settings.fovSize, "%d px")
        GUI.FOVSliderHandle = fovH; GUI.FOVSliderValue = fovV; GUI.FOVSliderTrack = fovT; GUI.FOVSliderFilledTrack = fovFT
        
        local _, fovThickH, fovThickV, fovThickT, fovThickFT = Factory.CreateSliderRow(GUI.AimScrollFrame, 12, "FOV Outline Thickness", "FOVThicknessSliderHandle", "FOVThicknessSliderValue", 1, 5, Config.settings.fovCircleThickness, "%.1f px")
        GUI.FOVThicknessSliderHandle = fovThickH; GUI.FOVThicknessSliderValue = fovThickV; GUI.FOVThicknessSliderTrack = fovThickT; GUI.FOVThicknessFilledTrack = fovThickFT
        
        local FOVColorFrame = Instance.new("Frame", GUI.AimScrollFrame)
        FOVColorFrame.Name = "FOVColorSelector"
        FOVColorFrame.Size = UDim2.new(1, -20, 0, 45)
        FOVColorFrame.BackgroundTransparency = 1
        FOVColorFrame.LayoutOrder = 13
        
        local FOVColorLabel = Instance.new("TextLabel", FOVColorFrame)
        FOVColorLabel.Name = "Label"
        FOVColorLabel.Size = UDim2.new(1, -20, 0, 20)
        FOVColorLabel.Position = UDim2.new(0, 10, 0, 0)
        FOVColorLabel.BackgroundTransparency = 1
        FOVColorLabel.Text = "FOV Circle Color:"
        FOVColorLabel.TextColor3 = currentThemeSettings.SecondaryText
        FOVColorLabel.Font = Enum.Font.SourceSans
        FOVColorLabel.TextSize = 13
        FOVColorLabel.TextXAlignment = Enum.TextXAlignment.Left
        
        local FOVColorButtonsFrame = Instance.new("Frame", FOVColorFrame)
        FOVColorButtonsFrame.Name = "FOVColorButtonsContainer"
        FOVColorButtonsFrame.Size = UDim2.new(1, -20, 0, 24)
        FOVColorButtonsFrame.Position = UDim2.new(0, 10, 0, 20)
        FOVColorButtonsFrame.BackgroundTransparency = 1
        
        local FOVColorListLayout = Instance.new("UIListLayout", FOVColorButtonsFrame)
        FOVColorListLayout.FillDirection = Enum.FillDirection.Horizontal
        FOVColorListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
        FOVColorListLayout.VerticalAlignment = Enum.VerticalAlignment.Center
        FOVColorListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        FOVColorListLayout.Padding = UDim.new(0, 8)
        
        GUI.FOVColorRedButton = Factory.CreateFOVColorButton(FOVColorButtonsFrame, 1, Color3.fromRGB(255,80,80), "Red")
        GUI.FOVColorWhiteButton = Factory.CreateFOVColorButton(FOVColorButtonsFrame, 2, Color3.fromRGB(240,240,240), "White")
        GUI.FOVColorGreenButton = Factory.CreateFOVColorButton(FOVColorButtonsFrame, 3, Color3.fromRGB(80,255,100), "Green")
        GUI.FOVColorBlueButton = Factory.CreateFOVColorButton(FOVColorButtonsFrame, 4, Color3.fromRGB(80,150,255), "Blue")
        GUI.FOVColorYellowButton = Factory.CreateFOVColorButton(FOVColorButtonsFrame, 5, Color3.fromRGB(255,230,80), "Yellow")
        -- Note: FOV/Crosshair Y Offset Slider that was previously at LayoutOrder 14 is removed.
        
        Factory.CreateSectionHeader(GUI.AimScrollFrame, 15, "Aimbot Tuning") -- LayoutOrder adjusted due to removal above
        local _, sensH, sensV, sensT, sensFT = Factory.CreateSliderRow(GUI.AimScrollFrame, 16, "Aimbot Sensitivity", "SensSliderHandle", "SensSliderValue", 0.01, 1, Config.settings.sensitivity, "%.2f")
        GUI.SensSliderHandle = sensH; GUI.SensSliderValue = sensV; GUI.SensSliderTrack = sensT; GUI.SensFilledTrack = sensFT
        
        Factory.CreateSectionHeader(GUI.AimScrollFrame, 17, "Aimbot Prediction")
        local _, temp_AimbotPredictionToggle = Factory.CreateToggleRow(GUI.AimScrollFrame, 18, "Enable Prediction", "AimbotPredictionToggle")
        GUI.AimbotPredictionToggle = temp_AimbotPredictionToggle
        local _, predStrH, predStrV, predStrT, predStrFT = Factory.CreateSliderRow(GUI.AimScrollFrame, 19, "Prediction Strength", "AimbotPredictionStrengthSliderHandle", "AimbotPredictionStrengthSliderValue", 0.00, 0.5, Config.settings.aimbotPredictionStrength, "%.2f")
        GUI.AimbotPredictionStrengthSliderHandle = predStrH; GUI.AimbotPredictionStrengthSliderValue = predStrV; GUI.AimbotPredictionStrengthSliderTrack = predStrT; GUI.AimbotPredictionStrengthFilledTrack = predStrFT

        -- Section UI - Config
        GUI.ConfigSection = Instance.new("Frame")
        GUI.ConfigSection.Name = "ConfigSection"
        GUI.ConfigSection.Size = UDim2.new(1,0,1,0)
        GUI.ConfigSection.BackgroundTransparency = 1
        GUI.ConfigSection.Visible = false -- Hidden by default
        GUI.ConfigSection.Parent = GUI.MainContentAreaFrame

        GUI.ConfigContent = Instance.new("ScrollingFrame")
        GUI.ConfigContent.Name = "ConfigContentScroll"
        GUI.ConfigContent.Size = UDim2.new(1,0,1,0)
        GUI.ConfigContent.Position = UDim2.new(0,0,0,0)
        GUI.ConfigContent.BackgroundColor3 = currentThemeSettings.ContainerBG
        GUI.ConfigContent.BackgroundTransparency = 1
        GUI.ConfigContent.BorderSizePixel = 0
        GUI.ConfigContent.CanvasSize = UDim2.new(0,0,0,0)
        GUI.ConfigContent.ScrollBarThickness = 8
        GUI.ConfigContent.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
        GUI.ConfigContent.AutomaticCanvasSize = Enum.AutomaticSize.Y
        GUI.ConfigContent.Parent = GUI.ConfigSection
        Instance.new("UICorner", GUI.ConfigContent).CornerRadius = Config.CONSTANTS.MODERN_UI_ELEMENT_CORNER_RADIUS
        local configScrollStroke = Instance.new("UIStroke", GUI.ConfigContent)
        configScrollStroke.Color = currentThemeSettings.OutlineColor

        local ConfigListLayout = Instance.new("UIListLayout", GUI.ConfigContent)
        ConfigListLayout.Padding = UDim.new(0,10)
        ConfigListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ConfigListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
        ConfigListLayout.VerticalAlignment = Enum.VerticalAlignment.Top -- Align items to top

        local ConfigPadding = Instance.new("UIPadding", GUI.ConfigContent)
        ConfigPadding.PaddingTop = UDim.new(0,10)
        ConfigPadding.PaddingBottom = UDim.new(0,10)
        ConfigPadding.PaddingLeft = UDim.new(0,10)
        ConfigPadding.PaddingRight = UDim.new(0,10)

        Factory.CreateSectionHeader(GUI.ConfigContent, 1, "General Settings")

        GUI.AuthorLabel = Instance.new("TextLabel", GUI.ConfigContent)
        GUI.AuthorLabel.Name = "AuthorLabel"
        GUI.AuthorLabel.Size = UDim2.new(1, -20, 0, 25)
        GUI.AuthorLabel.BackgroundTransparency = 1
        GUI.AuthorLabel.RichText = true -- For link color
        GUI.AuthorLabel.Text = "Author: <font color=\"#" .. currentThemeSettings.AuthorLinkColorHex .. "\">@CrownComp</font> (Telegram)"
        GUI.AuthorLabel.TextColor3 = currentThemeSettings.SecondaryText
        GUI.AuthorLabel.Font = Enum.Font.SourceSans
        GUI.AuthorLabel.TextSize = 14
        GUI.AuthorLabel.TextXAlignment = Enum.TextXAlignment.Left
        GUI.AuthorLabel.LayoutOrder = 2
        GUI.AuthorLabel.Position = UDim2.new(0, 10, 0, 0) -- Add a bit of padding for this specific label

        local ThemeFrame = Instance.new("Frame", GUI.ConfigContent)
        ThemeFrame.Name = "ThemeSelectorFrame"
        ThemeFrame.Size = UDim2.new(1, -20, 0, 40)
        ThemeFrame.BackgroundTransparency = 1
        ThemeFrame.LayoutOrder = 3
        
        GUI.ThemeLabel = Instance.new("TextLabel", ThemeFrame)
        GUI.ThemeLabel.Name = "ThemeLabel"
        GUI.ThemeLabel.Size = UDim2.new(0.5, -10, 1, 0)
        GUI.ThemeLabel.Position = UDim2.new(0, 10, 0, 0)
        GUI.ThemeLabel.BackgroundTransparency = 1
        GUI.ThemeLabel.Text = "Interface Theme:"
        GUI.ThemeLabel.TextColor3 = currentThemeSettings.SecondaryText
        GUI.ThemeLabel.Font = Enum.Font.SourceSans
        GUI.ThemeLabel.TextSize = 14
        GUI.ThemeLabel.TextXAlignment = Enum.TextXAlignment.Left
        
        local ThemeButtonFrame = Instance.new("Frame", ThemeFrame)
        ThemeButtonFrame.Name = "ThemeButtonsContainer"
        ThemeButtonFrame.Size = UDim2.new(0.5, -10, 1, 0)
        ThemeButtonFrame.Position = UDim2.new(0.5, 10, 0, 0)
        ThemeButtonFrame.BackgroundTransparency = 1
        
        local ThemeButtonLayout = Instance.new("UIListLayout", ThemeButtonFrame)
        ThemeButtonLayout.FillDirection = Enum.FillDirection.Horizontal
        ThemeButtonLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
        ThemeButtonLayout.VerticalAlignment = Enum.VerticalAlignment.Center
        ThemeButtonLayout.SortOrder = Enum.SortOrder.LayoutOrder
        ThemeButtonLayout.Padding = UDim.new(0, 8)
        
        GUI.DarkThemeButton = Instance.new("TextButton", ThemeButtonFrame)
        GUI.DarkThemeButton.Name = "DarkThemeButton"
        GUI.DarkThemeButton.Size = UDim2.new(0, 70, 0, 28)
        GUI.DarkThemeButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Active -- Default theme is Dark
        GUI.DarkThemeButton.TextColor3 = currentThemeSettings.ButtonText
        GUI.DarkThemeButton.Font = Enum.Font.SourceSansSemibold
        GUI.DarkThemeButton.Text = "Dark"
        GUI.DarkThemeButton.TextSize = 13
        GUI.DarkThemeButton.LayoutOrder = 1
        Instance.new("UICorner", GUI.DarkThemeButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS
        
        GUI.LightThemeButton = Instance.new("TextButton", ThemeButtonFrame)
        GUI.LightThemeButton.Name = "LightThemeButton"
        GUI.LightThemeButton.Size = UDim2.new(0, 70, 0, 28)
        GUI.LightThemeButton.BackgroundColor3 = currentThemeSettings.ButtonBG_Inactive
        GUI.LightThemeButton.TextColor3 = currentThemeSettings.PrimaryText
        GUI.LightThemeButton.Font = Enum.Font.SourceSansSemibold
        GUI.LightThemeButton.Text = "Light"
        GUI.LightThemeButton.TextSize = 13
        GUI.LightThemeButton.LayoutOrder = 2
        Instance.new("UICorner", GUI.LightThemeButton).CornerRadius = Config.CONSTANTS.BUTTON_CORNER_RADIUS

        local _, temp_BlurToggle = Factory.CreateToggleRow(GUI.ConfigContent, 4, "Background Blur", "BlurToggle")
        GUI.BlurToggle = temp_BlurToggle

        GUI.MouseUnlockLabel = Instance.new("TextLabel", GUI.ConfigContent)
        GUI.MouseUnlockLabel.Name = "MouseUnlockLabel"
        GUI.MouseUnlockLabel.Size = UDim2.new(1, -20, 0, 45) -- Allow for two lines
        GUI.MouseUnlockLabel.TextWrapped = true
        GUI.MouseUnlockLabel.BackgroundTransparency = 1
        GUI.MouseUnlockLabel.RichText = false
        GUI.MouseUnlockLabel.Text = "Press 'F2' to unlock/lock mouse cursor for GUI interaction in camera-locked modes."
        GUI.MouseUnlockLabel.TextColor3 = currentThemeSettings.SecondaryText
        GUI.MouseUnlockLabel.Font = Enum.Font.SourceSans
        GUI.MouseUnlockLabel.TextSize = 13
        GUI.MouseUnlockLabel.TextXAlignment = Enum.TextXAlignment.Left
        GUI.MouseUnlockLabel.TextYAlignment = Enum.TextYAlignment.Top
        GUI.MouseUnlockLabel.LayoutOrder = 5
        GUI.MouseUnlockLabel.Position = UDim2.new(0, 10, 0, 0)

        GUI.F1HintLabel = Instance.new("TextLabel", GUI.ConfigContent)
        GUI.F1HintLabel.Name = "F1HintLabel"
        GUI.F1HintLabel.Size = UDim2.new(1,-20,0,25)
        GUI.F1HintLabel.TextWrapped = true
        GUI.F1HintLabel.BackgroundTransparency = 1
        GUI.F1HintLabel.RichText = false
        GUI.F1HintLabel.Text = "Press 'F1' to minimize/restore the GUI."
        GUI.F1HintLabel.TextColor3 = currentThemeSettings.SecondaryText
        GUI.F1HintLabel.Font = Enum.Font.SourceSans
        GUI.F1HintLabel.TextSize = 13
        GUI.F1HintLabel.TextXAlignment = Enum.TextXAlignment.Left
        GUI.F1HintLabel.TextYAlignment = Enum.TextYAlignment.Top
        GUI.F1HintLabel.LayoutOrder = 6
        GUI.F1HintLabel.Position = UDim2.new(0,10,0,0)

        -- Section UI - Dev
        GUI.DevSection = Instance.new("Frame")
        GUI.DevSection.Name = "DevSection"
        GUI.DevSection.Size = UDim2.new(1,0,1,0)
        GUI.DevSection.BackgroundTransparency = 1
        GUI.DevSection.Visible = false -- Hidden by default
        GUI.DevSection.Parent = GUI.MainContentAreaFrame

        GUI.DevScrollFrame = Instance.new("ScrollingFrame")
        GUI.DevScrollFrame.Name = "DevScrollFrameContent"
        GUI.DevScrollFrame.Size = UDim2.new(1,0,1,0)
        GUI.DevScrollFrame.Position = UDim2.new(0,0,0,0)
        GUI.DevScrollFrame.BackgroundColor3 = currentThemeSettings.ContainerBG
        GUI.DevScrollFrame.BackgroundTransparency = 1
        GUI.DevScrollFrame.BorderSizePixel = 0
        GUI.DevScrollFrame.CanvasSize = UDim2.new(0,0,0,0)
        GUI.DevScrollFrame.ScrollBarThickness = 8
        GUI.DevScrollFrame.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
        GUI.DevScrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
        GUI.DevScrollFrame.Parent = GUI.DevSection
        Instance.new("UICorner", GUI.DevScrollFrame).CornerRadius = Config.CONSTANTS.MODERN_UI_ELEMENT_CORNER_RADIUS
        local devScrollStroke = Instance.new("UIStroke", GUI.DevScrollFrame)
        devScrollStroke.Color = currentThemeSettings.OutlineColor

        local DevListLayout = Instance.new("UIListLayout", GUI.DevScrollFrame)
        DevListLayout.Padding = UDim.new(0,10)
        DevListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        DevListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

        local DevInternalPadding = Instance.new("UIPadding", GUI.DevScrollFrame)
        DevInternalPadding.PaddingTop = UDim.new(0,10)
        DevInternalPadding.PaddingBottom = UDim.new(0,10)
        DevInternalPadding.PaddingLeft = UDim.new(0,10)
        DevInternalPadding.PaddingRight = UDim.new(0,10)
        
        Factory.CreateSectionHeader(GUI.DevScrollFrame, 1, "Developer Tools")
        local _, temp_DevFreecamToggle = Factory.CreateToggleRow(GUI.DevScrollFrame, 2, "Enable Freecam", "DevFreecamToggle")
        GUI.DevFreecamToggle = temp_DevFreecamToggle
        local _, temp_DevBrightSkyToggle = Factory.CreateToggleRow(GUI.DevScrollFrame, 3, "Bright Clear Sky", "DevBrightSkyToggle")
        GUI.DevBrightSkyToggle = temp_DevBrightSkyToggle

        -- Overlay Drawing Elements (FOV Circle, Crosshair, Radar, etc.)
        GUI.FOVCircleFrame = Instance.new("Frame")
        GUI.FOVCircleFrame.Name = "AimFOVCircleFrame"
        GUI.FOVCircleFrame.AnchorPoint = Vector2.new(0.5, 0.5)
        GUI.FOVCircleFrame.Position = UDim2.new(0.5, 0, 0.5, 0) -- Centered on screen
        GUI.FOVCircleFrame.Size = UDim2.fromOffset(Config.settings.fovSize, Config.settings.fovSize)
        GUI.FOVCircleFrame.BackgroundTransparency = 1 -- It's just an outline
        GUI.FOVCircleFrame.Visible = Config.settings.showFOV
        GUI.FOVCircleFrame.ZIndex = 100 -- High ZIndex
        GUI.FOVCircleFrame.Parent = GUI.ScreenGui
        Instance.new("UICorner", GUI.FOVCircleFrame).CornerRadius = UDim.new(1,0) -- Makes it a circle
        
        GUI.FOVStroke = Instance.new("UIStroke")
        GUI.FOVStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        GUI.FOVStroke.Color = Config.settings.fovCircleColor
        GUI.FOVStroke.Thickness = Config.settings.fovCircleThickness
        GUI.FOVStroke.Transparency = 0 -- Solid stroke
        GUI.FOVStroke.LineJoinMode = Enum.LineJoinMode.Round
        GUI.FOVStroke.Parent = GUI.FOVCircleFrame
        
        -- Crosshair elements
        local chSize, chThick, chColor = 12, 1.5, Color3.fromRGB(255,255,255) -- Crosshair dimensions/color
        GUI.CrosshairFrameH = Instance.new("Frame")
        GUI.CrosshairFrameH.Name = "CrosshairH"
        GUI.CrosshairFrameH.AnchorPoint = Vector2.new(0.5,0.5)
        GUI.CrosshairFrameH.Position = UDim2.new(0.5,0,0.5,0) -- Centered
        GUI.CrosshairFrameH.Size = UDim2.fromOffset(chSize, chThick) -- Horizontal line
        GUI.CrosshairFrameH.BackgroundColor3 = chColor
        GUI.CrosshairFrameH.BorderSizePixel = 0
        GUI.CrosshairFrameH.ZIndex = 101
        GUI.CrosshairFrameH.Visible = Config.settings.showCrosshair
        GUI.CrosshairFrameH.Parent = GUI.ScreenGui
        Instance.new("UICorner", GUI.CrosshairFrameH).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS
        
        GUI.CrosshairFrameV = Instance.new("Frame")
        GUI.CrosshairFrameV.Name = "CrosshairV"
        GUI.CrosshairFrameV.AnchorPoint = Vector2.new(0.5,0.5)
        GUI.CrosshairFrameV.Position = UDim2.new(0.5,0,0.5,0) -- Centered
        GUI.CrosshairFrameV.Size = UDim2.fromOffset(chThick, chSize) -- Vertical line
        GUI.CrosshairFrameV.BackgroundColor3 = chColor
        GUI.CrosshairFrameV.BorderSizePixel = 0
        GUI.CrosshairFrameV.ZIndex = 101
        GUI.CrosshairFrameV.Visible = Config.settings.showCrosshair
        GUI.CrosshairFrameV.Parent = GUI.ScreenGui
        Instance.new("UICorner", GUI.CrosshairFrameV).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS
        
        -- Radar Frame
        GUI.RadarFrame = Instance.new("Frame")
        GUI.RadarFrame.Name = "ESPRadarFrame"
        GUI.RadarFrame.AnchorPoint = Vector2.new(0,0) -- Top-left anchor
        GUI.RadarFrame.Position = UDim2.new(0, 15, 0, titleBarHeight + 15) -- Below title bar, padded
        GUI.RadarFrame.Size = UDim2.fromOffset(Config.settings.espRadarSize, Config.settings.espRadarSize)
        GUI.RadarFrame.BackgroundColor3 = currentThemeSettings.ContainerBG
        GUI.RadarFrame.BackgroundTransparency = Config.CONSTANTS.RADAR_BACKGROUND_TRANSPARENCY
        GUI.RadarFrame.BorderSizePixel = 0
        GUI.RadarFrame.ClipsDescendants = true -- Dots should not go outside
        GUI.RadarFrame.ZIndex = 50
        GUI.RadarFrame.Visible = Config.settings.espRadarEnabled and Config.settings.playerESPEnabled
        GUI.RadarFrame.Parent = GUI.ScreenGui
        Instance.new("UICorner", GUI.RadarFrame).CornerRadius = UDim.new(1,0) -- Circular radar

        GUI.RadarFrameStroke = Instance.new("UIStroke")
        GUI.RadarFrameStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        GUI.RadarFrameStroke.Color = currentThemeSettings.OutlineColor
        GUI.RadarFrameStroke.Thickness = Config.CONSTANTS.espRadarFrameBorderThickness
        GUI.RadarFrameStroke.Transparency = 0
        GUI.RadarFrameStroke.LineJoinMode = Enum.LineJoinMode.Round
        GUI.RadarFrameStroke.Parent = GUI.RadarFrame

        -- Radar Center Marker
        GUI.RadarCenterMarkerH = Instance.new("Frame")
        GUI.RadarCenterMarkerH.Name = "RadarCenterH"
        GUI.RadarCenterMarkerH.AnchorPoint = Vector2.new(0.5,0.5)
        GUI.RadarCenterMarkerH.Position = UDim2.new(0.5,0,0.5,0) -- Center of radar
        GUI.RadarCenterMarkerH.Size = UDim2.fromOffset(Config.CONSTANTS.espRadarCenterMarkerSize, Config.CONSTANTS.espRadarCenterMarkerThickness)
        GUI.RadarCenterMarkerH.BackgroundColor3 = Config.CONSTANTS.espRadarCenterMarkerColor
        GUI.RadarCenterMarkerH.BorderSizePixel = 0
        GUI.RadarCenterMarkerH.ZIndex = GUI.RadarFrame.ZIndex + 2 -- Above dots
        GUI.RadarCenterMarkerH.Parent = GUI.RadarFrame
        
        GUI.RadarCenterMarkerV = Instance.new("Frame")
        GUI.RadarCenterMarkerV.Name = "RadarCenterV"
        GUI.RadarCenterMarkerV.AnchorPoint = Vector2.new(0.5,0.5)
        GUI.RadarCenterMarkerV.Position = UDim2.new(0.5,0,0.5,0)
        GUI.RadarCenterMarkerV.Size = UDim2.fromOffset(Config.CONSTANTS.espRadarCenterMarkerThickness, Config.CONSTANTS.espRadarCenterMarkerSize)
        GUI.RadarCenterMarkerV.BackgroundColor3 = Config.CONSTANTS.espRadarCenterMarkerColor
        GUI.RadarCenterMarkerV.BorderSizePixel = 0
        GUI.RadarCenterMarkerV.ZIndex = GUI.RadarFrame.ZIndex + 2
        GUI.RadarCenterMarkerV.Parent = GUI.RadarFrame

        -- Aimbot Target Line
        GUI.AimbotTargetLineFrame = Instance.new("Frame")
        GUI.AimbotTargetLineFrame.Name = "AimbotTargetLine"
        GUI.AimbotTargetLineFrame.AnchorPoint = Vector2.new(0.5, 0.5)
        GUI.AimbotTargetLineFrame.BackgroundColor3 = Config.settings.fovCircleColor -- Use FOV color for this
        GUI.AimbotTargetLineFrame.BorderSizePixel = 0
        GUI.AimbotTargetLineFrame.ZIndex = Config.CONSTANTS.AIMBOT_TARGET_LINE_ZINDEX
        GUI.AimbotTargetLineFrame.Visible = false -- Only when aiming and enabled
        GUI.AimbotTargetLineFrame.Parent = GUI.ScreenGui

        -- Minimize Circle Button (appears when GUI is minimized)
        GUI.MinimizeCircleButton = Instance.new("TextButton")
        GUI.MinimizeCircleButton.Name = "MinimizeCircleButton"
        GUI.MinimizeCircleButton.Size = UDim2.fromOffset(Config.CONSTANTS.MINIMIZE_CIRCLE_SIZE, Config.CONSTANTS.MINIMIZE_CIRCLE_SIZE)
        GUI.MinimizeCircleButton.Position = Config.CONSTANTS.MINIMIZE_CIRCLE_DEFAULT_POS
        GUI.MinimizeCircleButton.BackgroundColor3 = currentThemeSettings.MinimizeCircleBG
        GUI.MinimizeCircleButton.TextColor3 = currentThemeSettings.MinimizeCircleText
        GUI.MinimizeCircleButton.Text = "CG" -- Short for "Konosuba GUI" or similar
        GUI.MinimizeCircleButton.Font = Enum.Font.SourceSansBold
        GUI.MinimizeCircleButton.TextSize = 18
        GUI.MinimizeCircleButton.BorderSizePixel = 0
        GUI.MinimizeCircleButton.Visible = false -- Only visible when minimized
        GUI.MinimizeCircleButton.ZIndex = 10000 -- Should be on top of everything
        GUI.MinimizeCircleButton.Parent = GUI.ScreenGui
        GUI.MinimizeCircleButton.Active = true -- Clickable
        GUI.MinimizeCircleButton.Draggable = true -- Can be dragged
        Instance.new("UICorner", GUI.MinimizeCircleButton).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS
        Instance.new("UIStroke", GUI.MinimizeCircleButton).Color = currentThemeSettings.OutlineColor

        -- Store references to loading elements for easy access during theme changes/cleanup
        HG.State.GUI_REFERENCES._LoadingText = LoadingText
        HG.State.GUI_REFERENCES._LoadingProgressBarBG = LoadingProgressBarBG
        HG.State.GUI_REFERENCES._LoadingAuthorLabel = LoadingAuthorLabel
        HG.State.GUI_REFERENCES._LoadingProgressBarFG = LoadingProgressBarFG
    end

    -- Manager Modules (empty tables to be populated with functions)
    HG.UIManager = {}
    HG.SliderManager = {}
    HG.MouseManager = {}
    HG.DevModeManager = {}
    HG.ESPManager = {}
    HG.AimbotManager = {}
    HG.FOVManager = {}

    -- UI Manager Functions
    function HG.UIManager.applyTheme(themeName)
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local State = HG.State

        Config.settings.currentTheme = themeName
        local theme = Config.THEME_SETTINGS[themeName] or Config.THEME_SETTINGS.Dark -- Fallback to Dark theme
        
        -- Update loading screen elements if they still exist (though unlikely after init)
        if GUI.LoadingBackgroundOverlay then GUI.LoadingBackgroundOverlay.BackgroundColor3 = theme.LoadingBG end
        if GUI._LoadingText then GUI._LoadingText.TextColor3 = theme.LoadingText end
        if GUI._LoadingProgressBarBG then GUI._LoadingProgressBarBG.BackgroundColor3 = theme.SliderTrack end
        if GUI._LoadingProgressBarFG then GUI._LoadingProgressBarFG.BackgroundColor3 = theme.AccentColor end
        if GUI._LoadingAuthorLabel then GUI._LoadingAuthorLabel.TextColor3 = theme.AccentColor end
        
        -- Main window frames
        if GUI.MainFrame then
            GUI.MainFrame.BackgroundColor3 = theme.MainBG
            if not State.isMinimized and not State.isLoading then
                GUI.MainFrame.BackgroundTransparency = 0
            elseif not State.isMinimized then -- Minimized but not loading
                GUI.MainFrame.BackgroundTransparency = 1
            end
        end
        if GUI.TitleFrame then
            GUI.TitleFrame.BackgroundColor3 = theme.TitleBG
            if not State.isMinimized and not State.isLoading then GUI.TitleFrame.BackgroundTransparency = 0 end
        end
        if GUI.SidebarFrame then
            GUI.SidebarFrame.BackgroundColor3 = theme.SidebarBG
            if not State.isMinimized and not State.isLoading then GUI.SidebarFrame.BackgroundTransparency = 0 end
        end
        if GUI.MainContentAreaFrame then
            GUI.MainContentAreaFrame.BackgroundColor3 = theme.ContentAreaBG
            if not State.isMinimized and not State.isLoading then GUI.MainContentAreaFrame.BackgroundTransparency = 0 end
        end

        -- Helper to style container frames (scroll frames)
        local function styleContainer(containerFrame)
            if containerFrame then
                -- containerFrame.BackgroundColor3 = theme.ContainerBG -- Already transparent in design
                local stroke = containerFrame:FindFirstChildOfClass("UIStroke")
                if stroke then stroke.Color = theme.OutlineColor end
            end
        end
        styleContainer(GUI.ESPScrollFrame)
        styleContainer(GUI.AimScrollFrame)
        styleContainer(GUI.ConfigContent)
        styleContainer(GUI.DevScrollFrame)
        
        -- Text elements
        if GUI.Title then GUI.Title.TextColor3 = theme.PrimaryText end
        if GUI.AuthorLabel then
            GUI.AuthorLabel.TextColor3 = theme.SecondaryText
            GUI.AuthorLabel.Text = string.format("Author: <font color=\"#%s\">@CrownComp</font> (Telegram)", theme.AuthorLinkColorHex)
        end
        if GUI.ThemeLabel then GUI.ThemeLabel.TextColor3 = theme.SecondaryText end
        if GUI.MouseUnlockLabel then GUI.MouseUnlockLabel.TextColor3 = theme.SecondaryText end
        if GUI.F1HintLabel then GUI.F1HintLabel.TextColor3 = theme.SecondaryText end
        
        -- Recursively update static colors for descendants (labels, specific frames)
        local function updateDescendantStaticColors(parent)
            if not parent then return end
            for _, child in ipairs(parent:GetDescendants()) do
                if child:IsA("TextLabel") then
                    if child.Name == "Label" or child.Name == "ThemeLabel" or child.Name == "AuthorLabel" or child.Name == "MouseUnlockLabel" or child.Name == "F1HintLabel" then
                        child.TextColor3 = theme.SecondaryText
                    elseif child.Name:match("SliderValue$") then -- e.g., FOVSliderValue
                        child.TextColor3 = theme.PrimaryText
                    elseif child.Name == "HeaderText" then
                        child.TextColor3 = theme.HeaderText
                        if child.Parent and child.Parent:IsA("Frame") then child.Parent.BackgroundColor3 = theme.TitleBG end
                    elseif child.Name == "ESPName2D" and child.Parent and child.Parent.Name == "ESPNameBG" then
                        child.TextColor3 = Color3.fromRGB(255,255,255) -- ESP Name is always white text
                    end
                elseif child:IsA("TextButton") then
                    if child.Name:match("ColorButton$") then -- For ESP/FOV color pickers
                        local stroke = child:FindFirstChildOfClass("UIStroke")
                        if stroke then
                            local isActive = child:GetAttribute("IsActiveColor") or false
                            stroke.Color = isActive and theme.ColorButtonBorder_Active or theme.ColorButtonBorder_Inactive
                        end
                    end
                elseif child.Name == "ESPNameBG" then
                    child.BackgroundColor3 = theme.ESPNameBG
                elseif child.Name:match("ESPHealthBarBG_") then -- Suffix matches player name
                    child.BackgroundColor3 = theme.HealthBarBGColor
                elseif child.Name:match("ESPHealthBarFG_") then
                    child.BackgroundColor3 = Config.CONSTANTS.espHealthBarColorFG -- Foreground is always green
                elseif child.Name == "FOVArrowContainer" then -- For off-screen target arrows
                    for _, line in ipairs(child:GetChildren()) do
                        if line:IsA("Frame") and line.Name:match("^Line") then
                            line.BackgroundColor3 = theme.FOVArrowColor
                        end
                    end
                end
            end
        end
        updateDescendantStaticColors(GUI.ESPScrollFrame)
        updateDescendantStaticColors(GUI.AimScrollFrame)
        updateDescendantStaticColors(GUI.ConfigContent)
        updateDescendantStaticColors(GUI.DevScrollFrame)
        updateDescendantStaticColors(GUI.ScreenGui) -- For things like ESP elements directly on ScreenGui

        -- Buttons with special theming
        if GUI.ToggleButton then
            GUI.ToggleButton.TextColor3 = theme.ButtonText
            GUI.ToggleButton.BackgroundColor3 = theme.ButtonBG_Minimize
            GUI.ToggleButton:SetAttribute("OriginalColor", theme.ButtonBG_Minimize)
        end
        if GUI.CloseButton then
            GUI.CloseButton.TextColor3 = theme.ButtonText
            GUI.CloseButton.BackgroundColor3 = theme.ButtonBG_Close
            GUI.CloseButton:SetAttribute("OriginalColor", theme.ButtonBG_Close)
        end
        if GUI.MinimizeCircleButton then
            GUI.MinimizeCircleButton.BackgroundColor3 = theme.MinimizeCircleBG
            GUI.MinimizeCircleButton.TextColor3 = theme.MinimizeCircleText
            local stroke = GUI.MinimizeCircleButton:FindFirstChildOfClass("UIStroke")
            if stroke then stroke.Color = theme.OutlineColor end
            GUI.MinimizeCircleButton:SetAttribute("OriginalColor", GUI.MinimizeCircleButton.BackgroundColor3)
        end
        
        -- Update slider colors
        local function updateSliderColors(sliderInfoToUpdate)
            if sliderInfoToUpdate and sliderInfoToUpdate.track and sliderInfoToUpdate.handle and sliderInfoToUpdate.filledTrack then
                sliderInfoToUpdate.track.BackgroundColor3 = theme.SliderTrack
                sliderInfoToUpdate.filledTrack.BackgroundColor3 = theme.SliderFilledTrack
                sliderInfoToUpdate.handle.BackgroundColor3 = theme.SliderHandle
            end
        end
        updateSliderColors(State.sensSliderInfo)
        updateSliderColors(State.fovSliderInfo)
        updateSliderColors(State.radarSizeSliderInfo)
        updateSliderColors(State.radarRangeSliderInfo)
        updateSliderColors(State.fovThicknessSliderInfo)
        updateSliderColors(State.espDistanceSliderInfo)
        updateSliderColors(State.aimbotPredictionStrengthSliderInfo)
        
        -- Other themed elements
        if GUI.RadarFrame then
            GUI.RadarFrame.BackgroundColor3 = theme.ContainerBG
            GUI.RadarFrame.BackgroundTransparency = Config.CONSTANTS.RADAR_BACKGROUND_TRANSPARENCY
            if GUI.RadarFrameStroke then GUI.RadarFrameStroke.Color = theme.OutlineColor end
        end
        if GUI.KonosubaWatermarkLabel then GUI.KonosubaWatermarkLabel.TextColor3 = theme.AccentColor end -- Watermark uses accent color
        if GUI.AimbotTargetLineFrame then GUI.AimbotTargetLineFrame.BackgroundColor3 = Config.settings.fovCircleColor end -- Uses FOV color
        
        -- Re-apply settings to ensure all button states/texts are correct for the new theme
        HG.UIManager.applySettingsToGUI()
    end

    function HG.UIManager.updateButtonState(button, enabled)
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES

        if not button then return end
        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
        local targetColor
        local targetTextColor = theme.PrimaryText -- Default text color for inactive state
        local targetText = button.Text -- Keep current text unless specified otherwise

        -- Determine button appearance based on its type and state (enabled/active)
        if button == GUI.PlayerESPToggle or button == GUI.ESPLinesToggle or button == GUI.ShowHealthToggle or
           button == GUI.SkeletonESPToggle or button == GUI.ESPRadarToggle or button == GUI.AimbotToggle or
           button == GUI.FOVToggle or button == GUI.CrosshairToggle or button == GUI.BlurToggle or
           button == GUI.OffScreenArrowsToggle or button == GUI.DevFreecamToggle or button == GUI.DevBrightSkyToggle or
           button == GUI.AimbotWallCheckToggle or button == GUI.AimbotTargetLineToggle or button == GUI.AimbotPredictionToggle then
            targetColor = enabled and theme.AccentColor or theme.ButtonBG_Inactive
            targetText = enabled and "On" or "Off"
            if enabled then targetTextColor = theme.AccentColorText end
        elseif button == GUI.TargetToggle then -- Aimbot target (Head/Body)
            targetColor = enabled and theme.AccentColor or theme.ButtonBG_Inactive
            targetText = enabled and "Head" or "Body"
            if enabled then targetTextColor = theme.AccentColorText end
        elseif button == GUI.DarkThemeButton or button == GUI.LightThemeButton then -- Theme selection
            local isActive = (button == GUI.DarkThemeButton and Config.settings.currentTheme == "Dark") or
                             (button == GUI.LightThemeButton and Config.settings.currentTheme == "Light")
            targetColor = isActive and theme.AccentColor or theme.ButtonBG_Inactive
            if isActive then targetTextColor = theme.AccentColorText else targetTextColor = theme.PrimaryText end
        elseif button == GUI.AimbotKeyLShiftButton or button == GUI.AimbotKeyRMBButton then -- Aimbot key selection
            local isActive = (button == GUI.AimbotKeyLShiftButton and Config.settings.aimbotActivationKey == Enum.KeyCode.LeftShift) or
                             (button == GUI.AimbotKeyRMBButton and Config.settings.aimbotActivationKey == Enum.UserInputType.MouseButton2)
            targetColor = isActive and theme.AccentColor or theme.ButtonBG_Inactive
            if isActive then targetTextColor = theme.AccentColorText else targetTextColor = theme.PrimaryText end
        elseif button == GUI.ESPSectionButton or button == GUI.AimSectionButton or button == GUI.ConfigSectionButton or button == GUI.DevSectionButton then -- Sidebar navigation
            local isActive = (button == HG.State.activeSectionButton)
            targetColor = isActive and theme.SidebarButtonBG_Active or theme.SidebarButtonBG_Inactive
            targetTextColor = isActive and theme.SidebarButtonText_Active or theme.SidebarButtonText_Inactive
            local indicator = button:FindFirstChild("ActiveIndicator")
            if indicator then indicator.Visible = isActive end
        else -- Fallback for any other toggle-like buttons (should ideally be covered above)
            targetColor = enabled and theme.AccentColor or theme.ButtonBG_Inactive
            targetText = enabled and "On" or "Off"
            if enabled then targetTextColor = theme.AccentColorText end
        end

        -- Specific overrides for title bar buttons
        if button == GUI.ToggleButton or button == GUI.CloseButton then
            targetTextColor = theme.ButtonText -- Always use the specific button text color for these
        end

        button.Text = targetText
        button.TextColor3 = targetTextColor
        button.BackgroundColor3 = targetColor
        button:SetAttribute("OriginalColor", targetColor) -- For hover effect
    end

    function HG.UIManager.updateESPColorVisuals(color)
        local Config = HG.Config
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES

        Config.settings.espOutlineColor = color
        Config.settings.espLineColor = color -- Lines and Skeletons typically use the same color as box outline
        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
        
        -- Update existing ESP elements on screen
        for _, element in pairs(State.espElements) do
            if element then
                if element.Stroke then element.Stroke.Color = Config.settings.espOutlineColor end
                if element.Line then element.Line.BackgroundColor3 = Config.settings.espLineColor end
                if element.SkeletonElements then
                    for partName, skelPart in pairs(element.SkeletonElements) do
                        if skelPart then
                            if partName == "Head" then -- Head might have fill + stroke
                                local stroke = skelPart:FindFirstChild("HeadStroke")
                                if stroke then stroke.Color = Config.settings.espOutlineColor end
                                skelPart.BackgroundColor3 = color -- Fill color for head
                            else -- Other skeleton parts are lines
                                skelPart.BackgroundColor3 = Config.settings.espOutlineColor
                            end
                        end
                    end
                end
            end
        end
        -- Update color picker buttons
        local espColorButtons = {GUI.RedColorButton, GUI.WhiteColorButton, GUI.GreenColorButton, GUI.BlueColorButton, GUI.YellowColorButton, GUI.PinkColorButton}
        for _, btn in ipairs(espColorButtons) do
            if btn then
                local stroke = btn:FindFirstChildOfClass("UIStroke")
                if stroke then
                    -- Check if this button's color is the currently active ESP color
                    local isActive = (btn.BackgroundColor3.R == color.R and btn.BackgroundColor3.G == color.G and btn.BackgroundColor3.B == color.B)
                    stroke.Color = isActive and theme.ColorButtonBorder_Active or theme.ColorButtonBorder_Inactive
                    stroke.Thickness = isActive and 2.5 or 1.5 -- Thicker border for active color
                    btn:SetAttribute("IsActiveColor", isActive)
                end
            end
        end
    end
    
    function HG.UIManager.updateFOVColorVisuals(color)
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES

        Config.settings.fovCircleColor = color
        if GUI.FOVStroke then GUI.FOVStroke.Color = Config.settings.fovCircleColor end
        if GUI.AimbotTargetLineFrame then GUI.AimbotTargetLineFrame.BackgroundColor3 = Config.settings.fovCircleColor end -- Target line uses FOV color
        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
        
        -- Update FOV color picker buttons
        local fovColorButtons = {GUI.FOVColorRedButton, GUI.FOVColorWhiteButton, GUI.FOVColorGreenButton, GUI.FOVColorBlueButton, GUI.FOVColorYellowButton}
        for _, btn in ipairs(fovColorButtons) do
            if btn then
                local stroke = btn:FindFirstChildOfClass("UIStroke")
                if stroke then
                    local isActive = (btn.BackgroundColor3.R == color.R and btn.BackgroundColor3.G == color.G and btn.BackgroundColor3.B == color.B)
                    stroke.Color = isActive and theme.ColorButtonBorder_Active or theme.ColorButtonBorder_Inactive
                    stroke.Thickness = isActive and 2.5 or 1.5
                    btn:SetAttribute("IsActiveColor", isActive)
                end
            end
        end
    end

    -- Developer Mode Manager Functions
    function HG.DevModeManager.updateFreecamOnRender(deltaTime)
        local State = HG.State
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local Config = HG.Config

        -- Only move camera if freecam is active and mouse is not unlocked (or GUI is minimized)
        if not State.freecamActive or (State.isMouseUnlocked and not State.isMinimized) then return end

        local moveVector = Vector3.new() -- Base movement direction
        -- Keyboard inputs for movement
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.W) then moveVector = moveVector + PlayerRefs.Camera.CFrame.LookVector end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.S) then moveVector = moveVector - PlayerRefs.Camera.CFrame.LookVector end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.A) then moveVector = moveVector - PlayerRefs.Camera.CFrame.RightVector end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.D) then moveVector = moveVector + PlayerRefs.Camera.CFrame.RightVector end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.E) then moveVector = moveVector + Vector3.new(0, 1, 0) end -- Up
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.Q) then moveVector = moveVector - Vector3.new(0, 1, 0) end -- Down

        if moveVector.Magnitude > 0 then moveVector = moveVector.Unit end -- Normalize for consistent speed
        PlayerRefs.Camera.CFrame = PlayerRefs.Camera.CFrame + moveVector * Config.CONSTANTS.FREECAM_MOVE_SPEED * deltaTime
    end

    function HG.DevModeManager.setFreecamState(enable)
        local Config = HG.Config
        local State = HG.State
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local GUI = HG.State.GUI_REFERENCES
        local UIManager = HG.UIManager

        if State.isLoading then return end -- Don't change state during load
        Config.settings.devFreecamEnabled = enable
        State.freecamActive = enable

        local playerChar = PlayerRefs.LocalPlayer.Character
        local hrp = playerChar and playerChar:FindFirstChild("HumanoidRootPart")

        if enable then
            -- Store original camera state
            State.originalCameraType = PlayerRefs.Camera.CameraType
            State.originalCameraCFrame = PlayerRefs.Camera.CFrame
            PlayerRefs.Camera.CameraType = Enum.CameraType.Scriptable -- Take control of camera

            if hrp then -- Anchor player to prevent movement
                State.localPlayerOriginalAnchoredState = hrp.Anchored
                hrp.Anchored = true
            end

            -- Lock mouse for camera control unless GUI is interactive
            if not State.isMouseUnlocked or State.isMinimized then
                Services.UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
                Services.UserInputService.MouseIconEnabled = false
            end

            -- Connect render step for freecam movement/look
            if not State.freecamRenderStepConnection or not State.freecamRenderStepConnection.Connected then
                 State.freecamRenderStepConnection = Services.RunService.RenderStepped:Connect(HG.DevModeManager.updateFreecamOnRender)
            end
            if GUI.FreecamWatermarkLabel then GUI.FreecamWatermarkLabel.Visible = true end
        else -- Disabling freecam
            if State.freecamRenderStepConnection and State.freecamRenderStepConnection.Connected then
                State.freecamRenderStepConnection:Disconnect()
                State.freecamRenderStepConnection = nil
            end
            
            PlayerRefs.Camera.CameraType = State.originalCameraType -- Restore camera type

            if hrp then hrp.Anchored = State.localPlayerOriginalAnchoredState end -- Unanchor player

            -- Attempt to restore camera to a reasonable state
            if PlayerRefs.Camera.CameraType == Enum.CameraType.Custom and playerChar and playerChar:FindFirstChild("Head") then
                 -- A common custom camera setup (e.g., follow character)
                 PlayerRefs.Camera.CFrame = CFrame.new(playerChar.Head.Position) * CFrame.Angles(0, math.rad(180),0) * CFrame.new(0,3,7)
                 PlayerRefs.Camera.CameraSubject = playerChar:FindFirstChildOfClass("Humanoid")
            elseif PlayerRefs.Camera.CameraType ~= Enum.CameraType.Scriptable then
                 PlayerRefs.Camera.CFrame = State.originalCameraCFrame -- Restore original CFrame if not scriptable
            end

            -- Restore mouse behavior
            if State.isMouseUnlocked then
                Services.UserInputService.MouseBehavior = Enum.MouseBehavior.Default
                Services.UserInputService.MouseIconEnabled = true
            else -- If mouse was meant to be locked (e.g. normal gameplay)
                Services.UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
                Services.UserInputService.MouseIconEnabled = false
            end
            if GUI.FreecamWatermarkLabel then GUI.FreecamWatermarkLabel.Visible = false end
        end
        if GUI.DevFreecamToggle then UIManager.updateButtonState(GUI.DevFreecamToggle, Config.settings.devFreecamEnabled) end
        Services.StarterGui:SetCore("SendNotification", {Title = "Dev: Freecam", Text = Config.settings.devFreecamEnabled and "Enabled" or "Disabled", Duration = 2})
    end

    function HG.DevModeManager.setBrightSkyState(enable)
        local Config = HG.Config
        local State = HG.State
        local Services = HG.Services
        local GUI = HG.State.GUI_REFERENCES
        local UIManager = HG.UIManager

        if State.isLoading then return end
        Config.settings.devBrightSkyEnabled = enable
        local sky = Services.Lighting:FindFirstChildOfClass("Sky")
        local clouds = Services.Workspace.Terrain:FindFirstChildOfClass("Clouds") or Services.Lighting:FindFirstChildOfClass("Clouds") -- Clouds can be in Terrain or Lighting

        if enable then
            -- Store original lighting and sky settings
            State.originalLightingAndSkySettings = {
                Brightness = Services.Lighting.Brightness, Ambient = Services.Lighting.Ambient,
                OutdoorAmbient = Services.Lighting.OutdoorAmbient, ColorShift_Top = Services.Lighting.ColorShift_Top,
                ColorShift_Bottom = Services.Lighting.ColorShift_Bottom, GlobalShadows = Services.Lighting.GlobalShadows,
                FogEnd = Services.Lighting.FogEnd, FogStart = Services.Lighting.FogStart, FogColor = Services.Lighting.FogColor,
                Sky = {}, Clouds = {}
            }
            if sky then
                State.originalLightingAndSkySettings.Sky.SkyboxBk = sky.SkyboxBk; State.originalLightingAndSkySettings.Sky.SkyboxDn = sky.SkyboxDn
                State.originalLightingAndSkySettings.Sky.SkyboxFt = sky.SkyboxFt; State.originalLightingAndSkySettings.Sky.SkyboxLf = sky.SkyboxLf
                State.originalLightingAndSkySettings.Sky.SkyboxRt = sky.SkyboxRt; State.originalLightingAndSkySettings.Sky.SkyboxUp = sky.SkyboxUp
                State.originalLightingAndSkySettings.Sky.CelestialBodiesShown = sky.CelestialBodiesShown; State.originalLightingAndSkySettings.Sky.StarCount = sky.StarCount
            else
                State.originalLightingAndSkySettings.Sky.createdSky = true -- Mark that we might create one
            end
            if clouds then
                State.originalLightingAndSkySettings.Clouds.Cover = clouds.Cover; State.originalLightingAndSkySettings.Clouds.Density = clouds.Density
            end

            -- Apply bright sky settings
            Services.Lighting.Brightness = 2.5
            Services.Lighting.Ambient = Color3.fromRGB(180, 180, 190)
            Services.Lighting.OutdoorAmbient = Color3.fromRGB(190, 195, 200)
            Services.Lighting.ColorShift_Top = Color3.fromRGB(135, 206, 250) -- Sky blue
            Services.Lighting.ColorShift_Bottom = Color3.fromRGB(190, 220, 255) -- Lighter horizon blue
            Services.Lighting.GlobalShadows = true
            Services.Lighting.FogEnd = 200000 -- Effectively no fog
            Services.Lighting.FogStart = 180000
            Services.Lighting.FogColor = Color3.fromRGB(200, 210, 225)

            if not sky and State.originalLightingAndSkySettings.Sky.createdSky then -- Create a temporary sky if none existed
                sky = Instance.new("Sky")
                sky.Name = "KONOSUBA_TempBrightSky" -- So we know it's ours
                sky.Parent = Services.Lighting
            end
            if sky then -- Clear skybox textures, disable stars for a plain bright sky
                sky.SkyboxBk = "" ; sky.SkyboxDn = "" ; sky.SkyboxFt = ""
                sky.SkyboxLf = "" ; sky.SkyboxRt = "" ; sky.SkyboxUp = ""
                sky.CelestialBodiesShown = true; sky.StarCount = 0
            end
            if clouds then clouds.Cover = 0; clouds.Density = 0 end -- Remove clouds
        else -- Disabling bright sky, restore original settings
            if State.originalLightingAndSkySettings.Brightness ~= nil then Services.Lighting.Brightness = State.originalLightingAndSkySettings.Brightness end
            if State.originalLightingAndSkySettings.Ambient ~= nil then Services.Lighting.Ambient = State.originalLightingAndSkySettings.Ambient end
            if State.originalLightingAndSkySettings.OutdoorAmbient ~= nil then Services.Lighting.OutdoorAmbient = State.originalLightingAndSkySettings.OutdoorAmbient end
            if State.originalLightingAndSkySettings.ColorShift_Top ~= nil then Services.Lighting.ColorShift_Top = State.originalLightingAndSkySettings.ColorShift_Top end
            if State.originalLightingAndSkySettings.ColorShift_Bottom ~= nil then Services.Lighting.ColorShift_Bottom = State.originalLightingAndSkySettings.ColorShift_Bottom end
            if State.originalLightingAndSkySettings.GlobalShadows ~= nil then Services.Lighting.GlobalShadows = State.originalLightingAndSkySettings.GlobalShadows end
            if State.originalLightingAndSkySettings.FogEnd ~= nil then Services.Lighting.FogEnd = State.originalLightingAndSkySettings.FogEnd end
            if State.originalLightingAndSkySettings.FogStart ~= nil then Services.Lighting.FogStart = State.originalLightingAndSkySettings.FogStart end
            if State.originalLightingAndSkySettings.FogColor ~= nil then Services.Lighting.FogColor = State.originalLightingAndSkySettings.FogColor end

            if sky then
                if State.originalLightingAndSkySettings.Sky and State.originalLightingAndSkySettings.Sky.createdSky and sky.Name == "KONOSUBA_TempBrightSky" then
                    sky:Destroy() -- Destroy the temporary sky we created
                elseif State.originalLightingAndSkySettings.Sky and State.originalLightingAndSkySettings.Sky.SkyboxBk ~= nil then
                    -- Restore original sky properties
                    sky.SkyboxBk = State.originalLightingAndSkySettings.Sky.SkyboxBk; sky.SkyboxDn = State.originalLightingAndSkySettings.Sky.SkyboxDn
                    sky.SkyboxFt = State.originalLightingAndSkySettings.Sky.SkyboxFt; sky.SkyboxLf = State.originalLightingAndSkySettings.Sky.SkyboxLf
                    sky.SkyboxRt = State.originalLightingAndSkySettings.Sky.SkyboxRt; sky.SkyboxUp = State.originalLightingAndSkySettings.Sky.SkyboxUp
                    sky.CelestialBodiesShown = State.originalLightingAndSkySettings.Sky.CelestialBodiesShown; sky.StarCount = State.originalLightingAndSkySettings.Sky.StarCount
                end
            end
            if clouds and State.originalLightingAndSkySettings.Clouds then
                if State.originalLightingAndSkySettings.Clouds.Cover ~= nil then clouds.Cover = State.originalLightingAndSkySettings.Clouds.Cover end
                if State.originalLightingAndSkySettings.Clouds.Density ~= nil then clouds.Density = State.originalLightingAndSkySettings.Clouds.Density end
            end
            State.originalLightingAndSkySettings = {} -- Clear stored settings
        end
        if GUI.DevBrightSkyToggle then UIManager.updateButtonState(GUI.DevBrightSkyToggle, Config.settings.devBrightSkyEnabled) end
        Services.StarterGui:SetCore("SendNotification", {Title = "Dev: Bright Sky", Text = Config.settings.devBrightSkyEnabled and "Enabled" or "Disabled", Duration = 2})
    end

    -- ESP Manager Functions
    function HG.ESPManager.createSkeletonLine(nameSuffix)
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local line = Instance.new("Frame")
        line.Name = "ESPSkeletonLine_" .. nameSuffix
        line.AnchorPoint = Vector2.new(0.5, 0.5) -- For rotation
        line.BackgroundColor3 = Config.settings.espOutlineColor
        line.BorderSizePixel = 0
        line.ZIndex = Config.CONSTANTS.SKELETON_ZINDEX
        line.Visible = false
        line.Parent = GUI.ScreenGui
        return line
    end

    function HG.ESPManager.createSkeletonHead(nameSuffix)
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local headCircle = Instance.new("Frame")
        headCircle.Name = "ESPSkeletonHead_" .. nameSuffix
        headCircle.AnchorPoint = Vector2.new(0.5, 0.5)
        headCircle.Size = UDim2.fromOffset(Config.CONSTANTS.SKELETON_HEAD_SIZE, Config.CONSTANTS.SKELETON_HEAD_SIZE)
        headCircle.BackgroundColor3 = Config.settings.espOutlineColor -- Fill color
        headCircle.BackgroundTransparency = Config.CONSTANTS.SKELETON_HEAD_FILL_TRANSPARENCY
        headCircle.BorderSizePixel = 0
        headCircle.ZIndex = Config.CONSTANTS.SKELETON_ZINDEX + 1 -- Slightly above lines
        headCircle.Visible = false
        headCircle.Parent = GUI.ScreenGui

        local headCorner = Instance.new("UICorner") -- Make it circular
        headCorner.CornerRadius = UDim.new(1, 0)
        headCorner.Parent = headCircle

        local headStroke = Instance.new("UIStroke") -- Outline for the head circle
        headStroke.Name = "HeadStroke"
        headStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        headStroke.Color = Config.settings.espOutlineColor
        headStroke.Thickness = Config.CONSTANTS.SKELETON_HEAD_STROKE_THICKNESS
        headStroke.LineJoinMode = Enum.LineJoinMode.Round
        headStroke.Transparency = 0
        headStroke.Parent = headCircle
        return headCircle
    end

    function HG.ESPManager.create2DBox(player)
        local State = HG.State
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local ESPManager = HG.ESPManager

        if State.espElements[player] and State.espElements[player].Frame then return end -- Already exists
        local playerName = player.Name
        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark

        local frame = Instance.new("Frame") -- The main bounding box
        frame.Name = "ESP2DBox_" .. playerName
        frame.BackgroundTransparency = 1 -- Box is just an outline
        frame.BorderSizePixel = 0
        frame.ZIndex = 5
        frame.Visible = false
        frame.ClipsDescendants = false -- Health bar might be outside
        frame.Parent = GUI.ScreenGui
        Instance.new("UICorner", frame).CornerRadius = Config.CONSTANTS.ESP_BOX_CORNER_RADIUS
        local frameStroke = Instance.new("UIStroke", frame) -- The outline itself
        frameStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        frameStroke.Color = Config.settings.espOutlineColor
        frameStroke.Thickness = Config.CONSTANTS.ESP_BOX_THICKNESS
        frameStroke.Transparency = 0

        local nameBg = Instance.new("Frame") -- Background for player name
        nameBg.Name = "ESPNameBG"
        nameBg.Size = UDim2.new(0, 100, 0, 18) -- Default size, will adjust
        nameBg.Position = UDim2.new(0.5, -50, 0, -20) -- Above box, centered
        nameBg.BackgroundColor3 = theme.ESPNameBG
        nameBg.BackgroundTransparency = Config.CONSTANTS.ESP_NAME_BACKGROUND_TRANSPARENCY
        nameBg.BorderSizePixel = 0
        nameBg.ZIndex = 8 -- Above box stroke
        nameBg.Visible = false
        nameBg.Parent = GUI.ScreenGui -- Parented to ScreenGui for independent positioning initially
        Instance.new("UICorner", nameBg).CornerRadius = Config.CONSTANTS.ESP_BOX_CORNER_RADIUS

        local nameLabel = Instance.new("TextLabel", nameBg) -- Player name text
        nameLabel.Name = "ESPName2D_" .. playerName
        nameLabel.Size = UDim2.new(1, -8, 1, -2) -- Padded inside nameBg
        nameLabel.Position = UDim2.new(0, 4, 0, 1)
        nameLabel.BackgroundTransparency = 1
        nameLabel.Text = playerName
        nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- White text for name
        nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        nameLabel.TextStrokeTransparency = 0.2
        nameLabel.Font = Enum.Font.SourceSansSemibold
        nameLabel.TextSize = 12
        nameLabel.TextXAlignment = Enum.TextXAlignment.Center
        nameLabel.ZIndex = nameBg.ZIndex + 1

        local lineFrame = Instance.new("Frame") -- Tracer line from screen bottom to player
        lineFrame.Name = "ESPLine_" .. playerName
        lineFrame.AnchorPoint = Vector2.new(0.5, 0.5) -- For rotation
        lineFrame.BackgroundColor3 = Config.settings.espLineColor
        lineFrame.BorderSizePixel = 0
        lineFrame.ZIndex = 4 -- Below box
        lineFrame.Visible = false
        lineFrame.Parent = GUI.ScreenGui

        local healthBarFrame = Instance.new("Frame", frame) -- Health bar background, parented to box
        healthBarFrame.Name = "ESPHealthBarBG_" .. playerName
        healthBarFrame.Size = UDim2.new(1, 0, 0, Config.CONSTANTS.espHealthBarHeight)
        healthBarFrame.Position = UDim2.new(0, 0, 1, Config.CONSTANTS.espHealthBarOffsetY) -- Below box
        healthBarFrame.BackgroundColor3 = theme.HealthBarBGColor
        healthBarFrame.BorderSizePixel = 0
        healthBarFrame.ZIndex = 6 -- Above box
        healthBarFrame.Visible = false
        healthBarFrame.ClipsDescendants = true
        Instance.new("UICorner", healthBarFrame).CornerRadius = UDim.new(0,2)

        local healthBarProgress = Instance.new("Frame", healthBarFrame) -- Health bar foreground (actual health)
        healthBarProgress.Name = "ESPHealthBarFG_" .. playerName
        healthBarProgress.Size = UDim2.new(1, 0, 1, 0) -- Full health initially
        healthBarProgress.Position = UDim2.new(0, 0, 0, 0)
        healthBarProgress.BackgroundColor3 = Config.CONSTANTS.espHealthBarColorFG
        healthBarProgress.BorderSizePixel = 0
        healthBarProgress.ZIndex = healthBarFrame.ZIndex + 1
        Instance.new("UICorner", healthBarProgress).CornerRadius = UDim.new(0,2)

        -- Create skeleton elements (lines and head circle)
        local skelElements = {
            Head = ESPManager.createSkeletonHead(playerName), Neck = ESPManager.createSkeletonLine("Neck_" .. playerName),
            Torso = ESPManager.createSkeletonLine("Torso_" .. playerName),
            LUArm = ESPManager.createSkeletonLine("LUArm_" .. playerName), LLArm = ESPManager.createSkeletonLine("LLArm_" .. playerName),
            RUArm = ESPManager.createSkeletonLine("RUArm_" .. playerName), RLArm = ESPManager.createSkeletonLine("RLArm_" .. playerName),
            LULeg = ESPManager.createSkeletonLine("LULeg_" .. playerName), LLLeg = ESPManager.createSkeletonLine("LLLeg_" .. playerName),
            RULeg = ESPManager.createSkeletonLine("RULeg_" .. playerName), RLLeg = ESPManager.createSkeletonLine("RLLeg_" .. playerName)
        }
        
        -- Store all created elements for this player
        State.espElements[player] = {
            Frame = frame, NameLabel = nameLabel, NameBG = nameBg, Line = lineFrame,
            HealthBarBG = healthBarFrame, HealthBarFG = healthBarProgress,
            SkeletonElements = skelElements, Character = player.Character, Stroke = frameStroke
        }
    end

    -- Gets standard R15/R6 character parts, with fallbacks for common alternative names
    function HG.ESPManager.getCharacterParts(character)
        if not character or not character.Parent then return nil end
        local parts = {
            Head = character:FindFirstChild("Head"),
            UpperTorso = character:FindFirstChild("UpperTorso"), LowerTorso = character:FindFirstChild("LowerTorso"),
            LeftUpperArm = character:FindFirstChild("LeftUpperArm"), LeftLowerArm = character:FindFirstChild("LeftLowerArm"), LeftHand = character:FindFirstChild("LeftHand"),
            RightUpperArm = character:FindFirstChild("RightUpperArm"), RightLowerArm = character:FindFirstChild("RightLowerArm"), RightHand = character:FindFirstChild("RightHand"),
            LeftUpperLeg = character:FindFirstChild("LeftUpperLeg"), LeftLowerLeg = character:FindFirstChild("LeftLowerLeg"), LeftFoot = character:FindFirstChild("LeftFoot"),
            RightUpperLeg = character:FindFirstChild("RightUpperLeg"), RightLowerLeg = character:FindFirstChild("RightLowerLeg"), RightFoot = character:FindFirstChild("RightFoot")
        }
        -- Fallbacks for R6 or other rig types
        if not parts.UpperTorso then parts.UpperTorso = character:FindFirstChild("Torso") end
        if not parts.LowerTorso then parts.LowerTorso = character:FindFirstChild("Torso") end -- If no LowerTorso, use Torso again
        if not parts.LeftUpperArm then parts.LeftUpperArm = character:FindFirstChild("Left Arm") end
        if not parts.LeftLowerArm then parts.LeftLowerArm = character:FindFirstChild("Left Arm") end
        if not parts.LeftHand then parts.LeftHand = character:FindFirstChild("Left Arm") end
        if not parts.RightUpperArm then parts.RightUpperArm = character:FindFirstChild("Right Arm") end
        if not parts.RightLowerArm then parts.RightLowerArm = character:FindFirstChild("Right Arm") end
        if not parts.RightHand then parts.RightHand = character:FindFirstChild("Right Arm") end
        if not parts.LeftUpperLeg then parts.LeftUpperLeg = character:FindFirstChild("Left Leg") end
        if not parts.LeftLowerLeg then parts.LeftLowerLeg = character:FindFirstChild("Left Leg") end
        if not parts.LeftFoot then parts.LeftFoot = character:FindFirstChild("Left Leg") end
        if not parts.RightUpperLeg then parts.RightUpperLeg = character:FindFirstChild("Right Leg") end
        if not parts.RightLowerLeg then parts.RightLowerLeg = character:FindFirstChild("Right Leg") end
        if not parts.RightFoot then parts.RightFoot = character:FindFirstChild("Right Leg") end

        -- Essential parts for basic skeleton
        if not parts.Head or not parts.UpperTorso or not parts.LowerTorso then
            -- warn("Essential skeleton parts not found for character: ", character.Name)
            return nil
        end
        return parts
    end

    function HG.ESPManager.clearAllESP()
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES

        -- Destroy all player-specific ESP elements
        for p, e in pairs(State.espElements) do
            if e.Frame then pcall(e.Frame.Destroy, e.Frame) end
            if e.NameBG then pcall(e.NameBG.Destroy, e.NameBG) end
            if e.Line then pcall(e.Line.Destroy, e.Line) end
            if e.SkeletonElements then
                for _, skelPart in pairs(e.SkeletonElements) do
                    if skelPart then pcall(skelPart.Destroy, skelPart) end
                end
            end
        end
        State.espElements = {} -- Clear the table

        -- Fallback: Clean up any stray ESP elements directly parented to ScreenGui
        if GUI.ScreenGui and GUI.ScreenGui.Parent then
            for _, v in ipairs(GUI.ScreenGui:GetChildren()) do
                if v.Name:match("^ESP(2DBox|Name2D|NameBG|Line|HealthBarBG|HealthBarFG|SkeletonLine|SkeletonHead|FOVArrowContainer)_") then
                    pcall(v.Destroy, v)
                end
            end
        end

        -- Clear radar dots
        for p, dotInfo in pairs(State.radarDots) do
            if dotInfo and dotInfo.Dot and dotInfo.Dot.Parent then pcall(dotInfo.Dot.Destroy, dotInfo.Dot) end
        end
        State.radarDots = {}
        if GUI.RadarFrame then GUI.RadarFrame.Visible = false end -- Hide radar frame

        -- Clear FOV arrows
        for player, arrow in pairs(State.fovArrows) do
            if arrow and arrow.Parent then pcall(arrow.Destroy, arrow) end
        end
        State.fovArrows = {}
    end

    function HG.ESPManager.removePlayerESP(player)
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES
        local playerName = player.Name -- Get name before player object might become invalid

        if State.espElements[player] then
            local e = State.espElements[player]
            if e.Frame then pcall(e.Frame.Destroy, e.Frame) end
            if e.NameBG then pcall(e.NameBG.Destroy, e.NameBG) end
            if e.Line then pcall(e.Line.Destroy, e.Line) end
            if e.SkeletonElements then
                for _, skelPart in pairs(e.SkeletonElements) do
                    if skelPart then pcall(skelPart.Destroy, skelPart) end
                end
            end
            State.espElements[player] = nil
        end

        -- Fallback cleanup by name, in case elements were not in State.espElements for some reason
        if GUI.ScreenGui and GUI.ScreenGui.Parent then
            local frame2D = GUI.ScreenGui:FindFirstChild("ESP2DBox_" .. playerName)
            if frame2D then frame2D:Destroy() end
            local nameBG2D = GUI.ScreenGui:FindFirstChild("ESPNameBG_" .. playerName) -- NameBG is on ScreenGui
            if nameBG2D then nameBG2D:Destroy() end
            local line2D = GUI.ScreenGui:FindFirstChild("ESPLine_" .. playerName)
            if line2D then line2D:Destroy() end
            -- Note: HealthBar is child of ESP2DBox, so destroyed with it. Skeleton parts are on ScreenGui.
            for _, child in ipairs(GUI.ScreenGui:GetChildren()) do
                if child.Name:match("^ESPSkeleton(Line|Head)_.*_" .. playerName .. "$") then -- Match complex skeleton part names
                    pcall(child.Destroy, child)
                end
            end
        end

        if State.radarDots[player] then
            local dotInfo = State.radarDots[player]
            if dotInfo and dotInfo.Dot and dotInfo.Dot.Parent then pcall(dotInfo.Dot.Destroy, dotInfo.Dot) end
            State.radarDots[player] = nil
        end
        if State.fovArrows[player] then
            pcall(State.fovArrows[player].Destroy, State.fovArrows[player])
            State.fovArrows[player] = nil
        end
        if GUI.ScreenGui and GUI.ScreenGui.Parent then -- Cleanup orphaned FOV arrow
            local arrowOrphan = GUI.ScreenGui:FindFirstChild("FOVArrowContainer_" .. playerName)
            if arrowOrphan then arrowOrphan:Destroy() end
        end
    end

    -- Updates a skeleton line's position, size, rotation, and visibility
    function HG.ESPManager.updateSkeletonLineVisual(lineInstance, p1_2D, p2_2D, thickness, color)
        if not lineInstance or not p1_2D or not p2_2D then
            if lineInstance then lineInstance.Visible = false end
            return false -- Cannot draw
        end
        local delta = p2_2D - p1_2D -- Vector from p1 to p2
        local distance = delta.Magnitude
        if distance < 1 then -- Too short to draw
            lineInstance.Visible = false
            return false
        end
        local midpoint = p1_2D + delta / 2 -- Center of the line
        local angle = math.atan2(delta.Y, delta.X) -- Angle for rotation

        lineInstance.Size = UDim2.fromOffset(distance, thickness)
        lineInstance.Position = UDim2.fromOffset(midpoint.X, midpoint.Y)
        lineInstance.Rotation = math.deg(angle)
        lineInstance.BackgroundColor3 = color
        lineInstance.Visible = true
        return true
    end

    function HG.ESPManager.hideSkeleton(skeletonElements)
        if not skeletonElements then return end
        for _, element in pairs(skeletonElements) do
            if element then element.Visible = false end
        end
    end

    function HG.ESPManager.update2DBox(player, element)
        local Config = HG.Config
        local State = HG.State
        local PlayerRefs = HG.PlayerRefs
        local ESPManager = HG.ESPManager

        local c = element.Character -- Target character
        local localChar = PlayerRefs.LocalPlayer.Character
        local localHRP = localChar and localChar:FindFirstChild("HumanoidRootPart")

        local function hideAllComponents() -- Helper to hide all ESP parts for this player
            if element.Frame then element.Frame.Visible = false end
            if element.NameBG then element.NameBG.Visible = false end
            if element.Line then element.Line.Visible = false end
            if element.HealthBarBG then element.HealthBarBG.Visible = false end
            ESPManager.hideSkeleton(element.SkeletonElements)
        end

        if not c or not c.Parent or not localHRP then hideAllComponents(); return end -- Prereqs not met

        local hR = c:FindFirstChild("HumanoidRootPart") -- Target's HRP
        local hE = c:FindFirstChild("Head") -- Target's Head
        local h = c:FindFirstChildOfClass("Humanoid") -- Target's Humanoid

        if not hR or not hE or not h then hideAllComponents(); return end -- More prereqs

        local distance = (hR.Position - localHRP.Position).Magnitude
        if distance > Config.settings.espMaxDistance then hideAllComponents(); return end -- Too far

        -- Get 3D world positions for key points
        local headWorldPos = hE.Position + Vector3.new(0, 0.5, 0) -- Top of head-ish
        local hrpWorldPos = hR.Position -- Center of mass
        local feetWorldPos = hR.Position - Vector3.new(0, hR.Size.Y / 2 + 0.1, 0) -- Feet position

        -- Convert to 2D screen coordinates
        local headScreenPos, headOnScreen = PlayerRefs.Camera:WorldToViewportPoint(headWorldPos)
        local hrpScreenPos, hrpOnScreen = PlayerRefs.Camera:WorldToViewportPoint(hrpWorldPos)
        local feetScreenPos, feetOnScreen = PlayerRefs.Camera:WorldToViewportPoint(feetWorldPos)

        -- Check if all key points are on screen and in front of camera
        local isOnScreen = headOnScreen and hrpOnScreen and feetOnScreen and
                           headScreenPos.Z > 0 and hrpScreenPos.Z > 0 and feetScreenPos.Z > 0

        if not isOnScreen then hideAllComponents(); return end

        -- Player ESP Box, Name, Health, Line
        if Config.settings.playerESPEnabled then
            local boxHeight = math.abs(headScreenPos.Y - feetScreenPos.Y)
            local boxWidth = boxHeight * 0.45 -- Maintain aspect ratio
            boxHeight = math.clamp(boxHeight, 8, PlayerRefs.Camera.ViewportSize.Y * 0.8) -- Clamp size
            boxWidth = math.clamp(boxWidth, 4, PlayerRefs.Camera.ViewportSize.X * 0.8)
            local boxX = hrpScreenPos.X - boxWidth / 2
            local boxY = headScreenPos.Y

            element.Frame.Size = UDim2.fromOffset(boxWidth, boxHeight)
            element.Frame.Position = UDim2.fromOffset(boxX, boxY)
            element.Frame.Visible = true
            if element.Stroke then element.Stroke.Color = Config.settings.espOutlineColor end

            -- Name tag
            if element.NameBG and element.NameLabel then
                local nameWidth = element.NameLabel.TextBounds.X -- Get actual text width
                local nameBgWidth = math.max(30, nameWidth + 8) -- Ensure min width, add padding
                element.NameBG.Size = UDim2.fromOffset(nameBgWidth, 18)
                element.NameBG.Position = UDim2.fromOffset(hrpScreenPos.X - nameBgWidth / 2, boxY - 20) -- Position above box
                element.NameBG.Visible = true
            end

            -- Health bar
            if element.HealthBarBG and element.HealthBarFG then
                if Config.settings.showHealthBar then
                    local healthPercent = math.clamp(h.Health / h.MaxHealth, 0, 1)
                    element.HealthBarBG.Visible = true
                    element.HealthBarFG.Size = UDim2.new(healthPercent, 0, 1, 0)
                else
                    element.HealthBarBG.Visible = false
                end
            end

            -- Tracer line
            if element.Line then
                if Config.settings.espLinesEnabled then
                    local startPoint = Vector2.new(PlayerRefs.Camera.ViewportSize.X / 2, PlayerRefs.Camera.ViewportSize.Y - 5) -- Bottom center of screen
                    local endPoint = Vector2.new(feetScreenPos.X, feetScreenPos.Y)
                    local delta = endPoint - startPoint
                    local dist = delta.Magnitude
                    local angle = math.atan2(delta.Y, delta.X)
                    element.Line.Size = UDim2.fromOffset(dist, Config.CONSTANTS.espLineThickness)
                    element.Line.Position = UDim2.fromOffset(startPoint.X + delta.X / 2, startPoint.Y + delta.Y / 2)
                    element.Line.Rotation = math.deg(angle)
                    element.Line.BackgroundColor3 = Config.settings.espLineColor
                    element.Line.Visible = true
                else
                    element.Line.Visible = false
                end
            end
        else -- playerESPEnabled is false, hide these components
            if element.Frame then element.Frame.Visible = false end
            if element.NameBG then element.NameBG.Visible = false end
            if element.HealthBarBG then element.HealthBarBG.Visible = false end
            if element.Line then element.Line.Visible = false end
        end

        -- Skeleton ESP
        if Config.settings.skeletonESPEnabled and element.SkeletonElements then
            local parts = ESPManager.getCharacterParts(c)
            if parts then
                local points2D = {} -- Store 2D screen positions of parts
                local canDrawSkeleton = true
                -- Convert all required part positions to 2D
                for name, partInstance in pairs(parts) do
                    local isEssential = (name == "Head" or name == "UpperTorso" or name == "LowerTorso")
                    if partInstance then
                        local pos3D = partInstance.Position
                        local pos2D_vec3, onScreenPart = PlayerRefs.Camera:WorldToViewportPoint(pos3D)
                        if onScreenPart and pos2D_vec3.Z > 0 then
                            points2D[name] = Vector2.new(pos2D_vec3.X, pos2D_vec3.Y)
                        else
                            points2D[name] = nil -- Part not visible or behind camera
                            if isEssential then canDrawSkeleton = false; break end
                        end
                    else
                        points2D[name] = nil -- Part doesn't exist
                        if isEssential then canDrawSkeleton = false; break end
                    end
                end

                if canDrawSkeleton then
                    local skel = element.SkeletonElements
                    local color = Config.settings.espOutlineColor
                    local thick = Config.CONSTANTS.SKELETON_LINE_THICKNESS
                    local skeletonPartDrawn = false -- Track if any part of skeleton is drawn

                    -- Head circle
                    if skel.Head and points2D.Head and parts.Head then
                        local head3DPart = parts.Head
                        local headCenterWorld = head3DPart.Position
                        local headSizeYWorld = head3DPart.Size.Y -- Use Y size for height
                        -- Get screen projection of top and bottom of head to estimate pixel height
                        local topOfHeadWorld = headCenterWorld + Vector3.new(0, headSizeYWorld / 2, 0)
                        local bottomOfHeadWorld = headCenterWorld - Vector3.new(0, headSizeYWorld / 2, 0)
                        local topOfHeadScreen, topOnScreen = PlayerRefs.Camera:WorldToViewportPoint(topOfHeadWorld)
                        local bottomOfHeadScreen, bottomOnScreen = PlayerRefs.Camera:WorldToViewportPoint(bottomOfHeadWorld)
                        
                        local newHeadPixelSize = Config.CONSTANTS.SKELETON_HEAD_SIZE -- Default
                        if topOnScreen and bottomOnScreen then
                            local headScreenHeight = math.abs(topOfHeadScreen.Y - bottomOfHeadScreen.Y)
                            newHeadPixelSize = headScreenHeight * Config.CONSTANTS.SKELETON_HEAD_PROPORTION
                            newHeadPixelSize = math.clamp(newHeadPixelSize, Config.CONSTANTS.MIN_SKELETON_HEAD_PIXEL_SIZE, Config.CONSTANTS.MAX_SKELETON_HEAD_PIXEL_SIZE)
                        end
                        
                        skel.Head.Size = UDim2.fromOffset(newHeadPixelSize, newHeadPixelSize)
                        skel.Head.Position = UDim2.fromOffset(points2D.Head.X, points2D.Head.Y)
                        skel.Head.Visible = (distance <= Config.CONSTANTS.SKELETON_HEAD_MAX_DISTANCE) -- Only show head if close enough
                        if skel.Head.Visible then
                            skel.Head.BackgroundColor3 = color -- Fill color
                            local headStroke = skel.Head:FindFirstChild("HeadStroke")
                            if headStroke then headStroke.Color = color end -- Stroke color
                            skeletonPartDrawn = true
                        end
                    elseif skel.Head then
                        skel.Head.Visible = false
                    end

                    -- Skeleton lines (Neck, Torso, Limbs)
                    if ESPManager.updateSkeletonLineVisual(skel.Neck, points2D.Head, points2D.UpperTorso, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.Torso, points2D.UpperTorso, points2D.LowerTorso, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.LUArm, points2D.UpperTorso, points2D.LeftUpperArm, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.LLArm, points2D.LeftUpperArm, points2D.LeftLowerArm or points2D.LeftHand, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.RUArm, points2D.UpperTorso, points2D.RightUpperArm, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.RLArm, points2D.RightUpperArm, points2D.RightLowerArm or points2D.RightHand, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.LULeg, points2D.LowerTorso, points2D.LeftUpperLeg, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.LLLeg, points2D.LeftUpperLeg, points2D.LeftLowerLeg or points2D.LeftFoot, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.RULeg, points2D.LowerTorso, points2D.RightUpperLeg, thick, color) then skeletonPartDrawn = true end
                    if ESPManager.updateSkeletonLineVisual(skel.RLLeg, points2D.RightUpperLeg, points2D.RightLowerLeg or points2D.RightFoot, thick, color) then skeletonPartDrawn = true end

                    if not skeletonPartDrawn then ESPManager.hideSkeleton(skel) end -- Hide all if no parts were drawn
                else
                    ESPManager.hideSkeleton(element.SkeletonElements) -- Cannot draw skeleton if essential parts missing
                end
            else
                ESPManager.hideSkeleton(element.SkeletonElements) -- Character parts not found
            end
        elseif element.SkeletonElements then
            ESPManager.hideSkeleton(element.SkeletonElements) -- Skeleton ESP disabled, hide elements
        end
    end

    -- Periodically checks for players and updates their ESP elements
    function HG.ESPManager.checkAndUpdateESP()
        local State = HG.State
        local Config = HG.Config
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local ESPManager = HG.ESPManager
        local GUI = HG.State.GUI_REFERENCES

        if State.isLoading then return end
        
        -- If all ESP features are off, clear everything and return
        if not (Config.settings.playerESPEnabled or Config.settings.skeletonESPEnabled) then
            if next(State.espElements) or next(State.radarDots) or (GUI.RadarFrame and GUI.RadarFrame.Visible) then
                ESPManager.clearAllESP()
            end
            return
        end
        
        -- Update radar frame visibility based on settings
        if GUI.RadarFrame then
            GUI.RadarFrame.Visible = Config.settings.espRadarEnabled and Config.settings.playerESPEnabled
        end

        local playersInGame = {} -- Keep track of players currently in the game
        local currentPlayers = Services.Players:GetPlayers()
        local localChar = PlayerRefs.LocalPlayer.Character
        local localHRP = localChar and localChar:FindFirstChild("HumanoidRootPart")

        if not localHRP then -- Local player HRP missing, cannot do ESP
            if next(State.espElements) or next(State.radarDots) then ESPManager.clearAllESP() end
            return
        end

        for _, player in ipairs(currentPlayers) do
            if player ~= PlayerRefs.LocalPlayer then -- Don't ESP self
                playersInGame[player] = true
                local character = player.Character
                if character and character.Parent then
                    local humanoid = character:FindFirstChildOfClass("Humanoid")
                    local hrp = character:FindFirstChild("HumanoidRootPart")
                    if humanoid and humanoid.Health > 0 and hrp then -- Player is alive and has HRP
                        local distance = (hrp.Position - localHRP.Position).Magnitude
                        if distance <= Config.settings.espMaxDistance then -- Player is within ESP range
                            local element = State.espElements[player]
                            if not element then -- New player in range, create ESP elements
                                ESPManager.create2DBox(player)
                            elseif element.Character ~= character then -- Player respawned, recreate ESP
                                ESPManager.removePlayerESP(player)
                                ESPManager.create2DBox(player)
                            else
                                element.Character = character -- Update character reference (usually same)
                            end
                            
                            -- Create radar dot if enabled and not existing
                            if Config.settings.espRadarEnabled and Config.settings.playerESPEnabled and not State.radarDots[player] and GUI.RadarFrame then
                                local dot = Instance.new("Frame")
                                dot.Name = "RadarDot_" .. player.Name
                                dot.AnchorPoint = Vector2.new(0.5,0.5)
                                dot.Size = UDim2.fromOffset(Config.CONSTANTS.espRadarDotSize, Config.CONSTANTS.espRadarDotSize)
                                dot.BackgroundColor3 = Config.CONSTANTS.espRadarEnemyDotColor
                                dot.BorderSizePixel = 0
                                dot.ZIndex = GUI.RadarFrame.ZIndex + 1
                                dot.Visible = false -- Will be made visible by updateRadarPositions
                                dot.Parent = GUI.RadarFrame
                                Instance.new("UICorner", dot).CornerRadius = Config.CONSTANTS.MODERN_UI_PILL_CORNER_RADIUS
                                local dotStroke = Instance.new("UIStroke", dot)
                                dotStroke.Name = "DotStroke"
                                dotStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
                                dotStroke.Color = Config.CONSTANTS.espRadarDotOutlineColor
                                dotStroke.Thickness = Config.CONSTANTS.espRadarDotOutlineThickness
                                dotStroke.LineJoinMode = Enum.LineJoinMode.Round
                                State.radarDots[player] = {Dot = dot, Stroke = dotStroke}
                            end
                        else -- Player out of range, remove ESP
                            if State.espElements[player] then ESPManager.removePlayerESP(player) end
                            if State.radarDots[player] then local rd=State.radarDots[player]; if rd and rd.Dot and rd.Dot.Parent then rd.Dot:Destroy() end; State.radarDots[player]=nil end
                        end
                    else -- Player dead or invalid, remove ESP
                        if State.espElements[player] then ESPManager.removePlayerESP(player) end
                        if State.radarDots[player] then local rd=State.radarDots[player]; if rd and rd.Dot and rd.Dot.Parent then rd.Dot:Destroy() end; State.radarDots[player]=nil end
                    end
                else -- Player character invalid, remove ESP
                    if State.espElements[player] then ESPManager.removePlayerESP(player) end
                    if State.radarDots[player] then local rd=State.radarDots[player]; if rd and rd.Dot and rd.Dot.Parent then rd.Dot:Destroy() end; State.radarDots[player]=nil end
                end
            end
        end

        -- Clean up ESP elements for players who left or are no longer valid
        for player, element in pairs(State.espElements) do
            if not playersInGame[player] then ESPManager.removePlayerESP(player) end
        end
        for player, dotInfo in pairs(State.radarDots) do
            if not playersInGame[player] then
                if dotInfo and dotInfo.Dot and dotInfo.Dot.Parent then pcall(dotInfo.Dot.Destroy, dotInfo.Dot) end
                State.radarDots[player] = nil
            end
        end
        for player, arrow in pairs(State.fovArrows) do
            if not playersInGame[player] then
                if arrow and arrow.Parent then pcall(arrow.Destroy, arrow) end
                State.fovArrows[player] = nil
            end
        end
    end

    -- Updates positions of dots on the ESP radar
    function HG.ESPManager.updateRadarPositions()
        local State = HG.State
        local Config = HG.Config
        local PlayerRefs = HG.PlayerRefs
        local GUI = HG.State.GUI_REFERENCES

        if State.isLoading or not GUI.RadarFrame or not GUI.RadarFrame.Visible or
           not Config.settings.playerESPEnabled or not Config.settings.espRadarEnabled then
            -- Hide all dots if radar is not supposed to be active
            for _, dotInfo in pairs(State.radarDots) do
                if dotInfo and dotInfo.Dot and dotInfo.Dot.Visible then dotInfo.Dot.Visible = false end
            end
            return
        end

        local localCharacter = PlayerRefs.LocalPlayer.Character
        local localHRP = localCharacter and localCharacter:FindFirstChild("HumanoidRootPart")
        local cameraCFrame = PlayerRefs.Camera.CFrame

        if not localHRP then -- Cannot update radar without local player HRP
            for _, dotInfo in pairs(State.radarDots) do
                if dotInfo and dotInfo.Dot and dotInfo.Dot.Visible then dotInfo.Dot.Visible = false end
            end
            return
        end

        local localPos = localHRP.Position
        local _, cameraRotationY, _ = cameraCFrame:ToOrientation() -- Get camera's Y rotation for radar orientation

        for player, dotInfo in pairs(State.radarDots) do
            if not dotInfo or not dotInfo.Dot then continue end
            local dot = dotInfo.Dot
            local enemyElement = State.espElements[player]
            local enemyCharacter = enemyElement and enemyElement.Character
            local enemyHRP = enemyCharacter and enemyCharacter:FindFirstChild("HumanoidRootPart")
            local enemyHumanoid = enemyCharacter and enemyCharacter:FindFirstChildOfClass("Humanoid")

            if enemyHRP and enemyHumanoid and enemyHumanoid.Health > 0 then
                local enemyPos = enemyHRP.Position
                local relativePos = enemyPos - localPos -- Vector from local player to enemy
                local relativePosXZ = Vector3.new(relativePos.X, 0, relativePos.Z) -- Ignore Y-axis for radar
                local distance = relativePosXZ.Magnitude

                if distance > 0 and distance <= Config.settings.espRadarRange then
                    -- Rotate relative position to be camera-relative for radar display
                    local rotatedVector = CFrame.Angles(0, -cameraRotationY, 0) * relativePosXZ
                    -- Scale position to fit within radar bounds
                    local radarScale = (Config.settings.espRadarSize / 2) / Config.settings.espRadarRange
                    local radarX = math.clamp(rotatedVector.X * radarScale, -Config.settings.espRadarSize / 2, Config.settings.espRadarSize / 2)
                    local radarY = math.clamp(rotatedVector.Z * radarScale, -Config.settings.espRadarSize / 2, Config.settings.espRadarSize / 2) -- Z-axis becomes Y on 2D radar

                    if Vector2.new(radarX, radarY).Magnitude <= Config.settings.espRadarSize / 2 then -- Check if within circle
                        dot.Position = UDim2.new(0.5, radarX, 0.5, radarY) -- Position relative to radar center
                        dot.Visible = true
                    else
                        dot.Visible = false -- Outside circular boundary due to clamping shape
                    end
                else
                    dot.Visible = false -- Out of radar range
                end
            else
                dot.Visible = false -- Enemy invalid or dead
            end
        end
    end
    
    -- Aimbot Manager Functions
    function HG.AimbotManager.isTargetVisible(targetPlayer, targetPart)
        local Config = HG.Config
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs

        if not Config.settings.aimbotWallCheckEnabled then return true end -- Wall check disabled
        if not PlayerRefs.LocalPlayer.Character or not targetPlayer.Character or not targetPart then return false end

        local localChar = PlayerRefs.LocalPlayer.Character
        local targetChar = targetPlayer.Character
        local rayOrigin = PlayerRefs.Camera.CFrame.Position
        local rayDirection = (targetPart.Position - rayOrigin) -- Vector from camera to target part

        local raycastParams = RaycastParams.new()
        raycastParams.FilterType = Enum.RaycastFilterType.Exclude
        raycastParams.FilterDescendantsInstances = {localChar, targetChar} -- Ignore self and target
        raycastParams.IgnoreWater = true

        local raycastResult = Services.Workspace:Raycast(rayOrigin, rayDirection.Unit * rayDirection.Magnitude, raycastParams)
        return raycastResult == nil -- If nil, no obstruction, target is visible
    end

    function HG.AimbotManager.findNewAimbotTarget()
        local Config = HG.Config
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local AimbotManager = HG.AimbotManager

        local bestTarget = nil
        local minScreenDist = Config.settings.fovSize / 2 -- Max distance from FOV center
        local viewportSize = PlayerRefs.Camera.ViewportSize
        local fovScreenCenter = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)
        
        local playersToCheck = Services.Players:GetPlayers()
        for _, player in ipairs(playersToCheck) do
            if player ~= PlayerRefs.LocalPlayer then
                local character = player.Character
                if character and character.Parent and character:FindFirstChildOfClass("Humanoid") and character.Humanoid.Health > 0 then
                    local targetPartName = Config.settings.targetHead and "Head" or "HumanoidRootPart"
                    local targetPart = character:FindFirstChild(targetPartName) or character:FindFirstChild("UpperTorso") -- Fallback to UpperTorso
                    
                    if targetPart then
                        local screenPosVec3, onScreen = PlayerRefs.Camera:WorldToViewportPoint(targetPart.Position)
                        if onScreen and screenPosVec3.Z > 0 then -- On screen and in front of camera
                            if AimbotManager.isTargetVisible(player, targetPart) then
                                local screenPos = Vector2.new(screenPosVec3.X, screenPosVec3.Y)
                                local screenDist = (screenPos - fovScreenCenter).Magnitude
                                if screenDist <= minScreenDist then -- Is this target closer to FOV center?
                                    minScreenDist = screenDist
                                    bestTarget = player
                                end
                            end
                        end
                    end
                end
            end
        end
        return bestTarget
    end

    function HG.AimbotManager.aimbotLoop()
        local State = HG.State
        local Config = HG.Config
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local AimbotManager = HG.AimbotManager

        if State.isLoading then return end

        -- Check if aim key is pressed
        local isAimKeyPressed = false
        if Config.settings.aimbotActivationKey:IsA("KeyCode") then
            isAimKeyPressed = Services.UserInputService:IsKeyDown(Config.settings.aimbotActivationKey)
        elseif Config.settings.aimbotActivationKey:IsA("UserInputType") then -- MouseButton
            isAimKeyPressed = Services.UserInputService:IsMouseButtonPressed(Config.settings.aimbotActivationKey)
        end

        if Config.settings.aimbotEnabled and isAimKeyPressed then
            local targetToAim = nil
            local targetPartToAim = nil

            -- Validate current target if one exists
            if State.currentAimbotTarget then
                local isValid = false
                local currentTargetPlayer = Services.Players:GetPlayerByUserId(State.currentAimbotTarget.UserId)
                if currentTargetPlayer then
                    local character = currentTargetPlayer.Character
                    if character and character.Parent and character:FindFirstChildOfClass("Humanoid") and character.Humanoid.Health > 0 then
                        local tempTargetPartName = Config.settings.targetHead and "Head" or "HumanoidRootPart"
                        local tempTargetPart = character:FindFirstChild(tempTargetPartName) or character:FindFirstChild("UpperTorso")
                        
                        if tempTargetPart then
                            local screenPosVec3, onScreen = PlayerRefs.Camera:WorldToViewportPoint(tempTargetPart.Position)
                            if onScreen and screenPosVec3.Z > 0 then
                                if AimbotManager.isTargetVisible(currentTargetPlayer, tempTargetPart) then
                                    local viewportSize = PlayerRefs.Camera.ViewportSize
                                    local fovScreenCenter = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)
                                    local screenDist = (Vector2.new(screenPosVec3.X, screenPosVec3.Y) - fovScreenCenter).Magnitude
                                    if screenDist <= Config.settings.fovSize / 2 then -- Still within FOV
                                        isValid = true
                                        targetPartToAim = tempTargetPart
                                    end
                                end
                            end
                        end
                    end
                end
                if not isValid then State.currentAimbotTarget = nil else targetToAim = currentTargetPlayer end
            end

            -- Find new target if current one is invalid or doesn't exist
            if not State.currentAimbotTarget then
                local newTargetPlayer = AimbotManager.findNewAimbotTarget()
                if newTargetPlayer then
                    State.currentAimbotTarget = newTargetPlayer
                    targetToAim = newTargetPlayer
                    local character = newTargetPlayer.Character
                    if character then
                        local targetPartName = Config.settings.targetHead and "Head" or "HumanoidRootPart"
                        targetPartToAim = character:FindFirstChild(targetPartName) or character:FindFirstChild("UpperTorso")
                    end
                end
            end

            -- If a valid target is found, aim
            if targetToAim and targetPartToAim then
                local targetPos = targetPartToAim.Position
                -- Apply prediction if enabled
                if Config.settings.aimbotPredictionEnabled then
                    local targetCharacter = targetToAim.Character
                    local targetHRP = targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local targetVelocity = targetHRP.Velocity
                        local predictionOffset = targetVelocity * Config.settings.aimbotPredictionStrength
                        targetPos = targetPos + predictionOffset
                    end
                end
                -- Smoothly aim camera
                local cameraCFrame = PlayerRefs.Camera.CFrame
                local origin = cameraCFrame.Position
                local direction = (targetPos - origin).Unit -- Normalized direction vector
                local goalCFrame = CFrame.lookAt(origin, origin + direction)
                local lerpAlpha = math.clamp(Config.settings.sensitivity, 0.01, 1.0) -- Sensitivity as interpolation alpha
                PlayerRefs.Camera.CFrame = cameraCFrame:Lerp(goalCFrame, lerpAlpha)
            end
        else -- Aimbot not enabled or key not pressed
            if State.currentAimbotTarget then State.currentAimbotTarget = nil end -- Clear current target
        end
    end

    function HG.AimbotManager.setupAimbotConnection()
        local State = HG.State
        local Config = HG.Config
        local Services = HG.Services
        local AimbotManager = HG.AimbotManager

        if State.aimbotConnection then State.aimbotConnection:Disconnect() end -- Disconnect old connection

        if Config.settings.aimbotEnabled then
            State.aimbotConnection = Services.RunService.RenderStepped:Connect(AimbotManager.aimbotLoop)
        else
            State.aimbotConnection = nil -- Clear connection
            State.currentAimbotTarget = nil -- Clear target if aimbot is turned off
        end
    end

    -- FOV Manager Functions (handles off-screen arrows)
    function HG.FOVManager.updateOffScreenArrows()
        local Config = HG.Config
        local State = HG.State
        local PlayerRefs = HG.PlayerRefs
        local Services = HG.Services
        local GUI = HG.State.GUI_REFERENCES

        -- Check if arrows should be shown
        if not Config.settings.showOffScreenArrows or not Config.settings.showFOV or not GUI.FOVCircleFrame then
            -- Hide/destroy all existing arrows
            for player, arrow in pairs(State.fovArrows) do
                if arrow and arrow.Parent then pcall(arrow.Destroy, arrow) end
            end
            State.fovArrows = {}
            return
        end

        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
        local viewportSize = PlayerRefs.Camera.ViewportSize
        local fovCenter = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)
        local fovRadius = Config.settings.fovSize / 2
        local arrowOffset = Config.CONSTANTS.FOV_ARROW_SIZE * 0.6 -- How far outside FOV circle arrows appear
        
        for _, player in ipairs(Services.Players:GetPlayers()) do
            if player ~= PlayerRefs.LocalPlayer then
                local character = player.Character
                local hrp = character and character:FindFirstChild("HumanoidRootPart")
                local humanoid = character and character:FindFirstChildOfClass("Humanoid")

                if hrp and humanoid and humanoid.Health > 0 then
                    local screenPosVec3, onScreen = PlayerRefs.Camera:WorldToViewportPoint(hrp.Position)
                    if onScreen and screenPosVec3.Z > 0 then -- Target is on screen and in front
                        local screenPosVec2 = Vector2.new(screenPosVec3.X, screenPosVec3.Y)
                        local delta = screenPosVec2 - fovCenter -- Vector from FOV center to target
                        local dist = delta.Magnitude

                        if dist > fovRadius then -- Target is outside FOV circle, show arrow
                            local arrowContainer = State.fovArrows[player]
                            if not arrowContainer or not arrowContainer.Parent then -- Create arrow if it doesn't exist
                                arrowContainer = Instance.new("Frame")
                                arrowContainer.Name = "FOVArrowContainer_" .. player.Name
                                arrowContainer.Size = UDim2.fromOffset(Config.CONSTANTS.FOV_ARROW_SIZE, Config.CONSTANTS.FOV_ARROW_SIZE)
                                arrowContainer.BackgroundTransparency = 1
                                arrowContainer.AnchorPoint = Vector2.new(0.5, 0.5) -- For rotation
                                arrowContainer.ZIndex = Config.CONSTANTS.FOV_ARROW_ZINDEX
                                arrowContainer.Parent = GUI.ScreenGui
                                -- Arrow shape (two lines)
                                local line1 = Instance.new("Frame", arrowContainer)
                                line1.Name = "Line1"; line1.Size = UDim2.new(0, Config.CONSTANTS.FOV_ARROW_LINE_THICKNESS, 0.6, 0)
                                line1.Position = UDim2.new(0.5, -Config.CONSTANTS.FOV_ARROW_SIZE*0.2, 0.2, 0); line1.Rotation = -45
                                line1.AnchorPoint = Vector2.new(0.5, 0.5); line1.BackgroundColor3 = theme.FOVArrowColor; line1.BorderSizePixel=0
                                local line2 = Instance.new("Frame", arrowContainer)
                                line2.Name = "Line2"; line2.Size = UDim2.new(0, Config.CONSTANTS.FOV_ARROW_LINE_THICKNESS, 0.6, 0)
                                line2.Position = UDim2.new(0.5, Config.CONSTANTS.FOV_ARROW_SIZE*0.2, 0.2, 0); line2.Rotation = 45
                                line2.AnchorPoint = Vector2.new(0.5, 0.5); line2.BackgroundColor3 = theme.FOVArrowColor; line2.BorderSizePixel=0
                                State.fovArrows[player] = arrowContainer
                            end
                            -- Position and rotate arrow
                            local direction = delta.Unit
                            local angle = math.atan2(direction.Y, direction.X)
                            local rotationDegrees = math.deg(angle) - 90 -- Adjust for arrow orientation
                            arrowContainer.Position = UDim2.fromOffset(
                                fovCenter.X + direction.X * (fovRadius + arrowOffset),
                                fovCenter.Y + direction.Y * (fovRadius + arrowOffset)
                            )
                            arrowContainer.Rotation = rotationDegrees
                            arrowContainer.Visible = true
                        else -- Target is inside FOV, hide arrow
                            if State.fovArrows[player] then State.fovArrows[player].Visible = false end
                        end
                    else -- Target not on screen (or behind), hide arrow
                        if State.fovArrows[player] then State.fovArrows[player].Visible = false end
                    end
                else -- Invalid player/character, destroy arrow
                    if State.fovArrows[player] then
                        pcall(State.fovArrows[player].Destroy, State.fovArrows[player])
                        State.fovArrows[player] = nil
                    end
                end
            end
        end
    end

    -- Slider Manager Functions
    function HG.SliderManager.updateSlider(sliderInfo, mouseX)
        local Config = HG.Config
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES

        local track, handle, valueLabel = sliderInfo.track, sliderInfo.handle, sliderInfo.valueLabel
        local trackWidth = track.AbsoluteSize.X - handle.AbsoluteSize.X -- Usable width of the track
        if trackWidth <= 0 then return end -- Cannot update if track has no width yet

        local trackStart = track.AbsolutePosition.X + handle.AbsoluteSize.X / 2 -- Start of draggable area
        local relativeX = mouseX - trackStart
        local clampedX = math.clamp(relativeX, 0, trackWidth) -- Clamp mouse position to track bounds
        
        -- Update handle position (visual)
        handle.Position = UDim2.fromOffset(clampedX + handle.AbsoluteSize.X / 2, handle.Position.Y.Offset)
        
        local fraction = math.clamp(clampedX / trackWidth, 0, 1) -- Value as a fraction (0 to 1)
        local value = sliderInfo.minVal + fraction * (sliderInfo.maxVal - sliderInfo.minVal) -- Calculate actual value
        
        local varName = sliderInfo.varName
        local fmt = sliderInfo.formatString
        
        -- Update corresponding setting and value label based on slider type
        if varName == "SensSliderHandle" then
            Config.settings.sensitivity = value
            valueLabel.Text = string.format(fmt or "%.2f", Config.settings.sensitivity)
        elseif varName == "FOVSliderHandle" then
            Config.settings.fovSize = math.floor(value + 0.5) -- Round to nearest integer
            valueLabel.Text = string.format(fmt or "%d px", Config.settings.fovSize)
            if GUI.FOVCircleFrame then GUI.FOVCircleFrame.Size = UDim2.fromOffset(Config.settings.fovSize, Config.settings.fovSize) end
        elseif varName == "FOVThicknessSliderHandle" then
            Config.settings.fovCircleThickness = value
            valueLabel.Text = string.format(fmt or "%.1f px", Config.settings.fovCircleThickness)
            if GUI.FOVStroke then GUI.FOVStroke.Thickness = Config.settings.fovCircleThickness end
        elseif varName == "RadarSizeSliderHandle" then
            Config.settings.espRadarSize = math.floor(value + 0.5)
            valueLabel.Text = string.format(fmt or "%d px", Config.settings.espRadarSize)
            if GUI.RadarFrame then GUI.RadarFrame.Size = UDim2.fromOffset(Config.settings.espRadarSize, Config.settings.espRadarSize) end
        elseif varName == "RadarRangeSliderHandle" then
            Config.settings.espRadarRange = math.floor(value + 0.5)
            valueLabel.Text = string.format(fmt or "%d studs", Config.settings.espRadarRange)
        elseif varName == "ESPDistanceSliderHandle" then
            Config.settings.espMaxDistance = math.floor(value + 0.5)
            valueLabel.Text = string.format(fmt or "%d studs", Config.settings.espMaxDistance)
        elseif varName == "AimbotPredictionStrengthSliderHandle" then
            Config.settings.aimbotPredictionStrength = value
            valueLabel.Text = string.format(fmt or "%.2f", Config.settings.aimbotPredictionStrength)
        end
        
        -- Update filled track visual
        if sliderInfo.filledTrack then sliderInfo.filledTrack.Size = UDim2.new(fraction, 0, 1, 0) end
    end

    function HG.SliderManager.initializeSlider(sliderInfo, updateFromGlobalSettings)
        local Config = HG.Config
        local State = HG.State

        State.sliders[sliderInfo.handle] = sliderInfo -- Store slider info
        local currentVal = 0
        local varName = sliderInfo.varName

        -- Determine initial value (from global settings or default)
        if varName == "SensSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.sensitivity or sliderInfo.defaultVal
        elseif varName == "FOVSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.fovSize or sliderInfo.defaultVal
        elseif varName == "FOVThicknessSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.fovCircleThickness or sliderInfo.defaultVal
        elseif varName == "RadarSizeSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.espRadarSize or sliderInfo.defaultVal
        elseif varName == "RadarRangeSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.espRadarRange or sliderInfo.defaultVal
        elseif varName == "ESPDistanceSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.espMaxDistance or sliderInfo.defaultVal
        elseif varName == "AimbotPredictionStrengthSliderHandle" then currentVal = updateFromGlobalSettings and Config.settings.aimbotPredictionStrength or sliderInfo.defaultVal
        else currentVal = sliderInfo.defaultVal
        end
        
        local fraction = 0
        if (sliderInfo.maxVal - sliderInfo.minVal) ~= 0 then -- Avoid division by zero
            fraction = math.clamp((currentVal - sliderInfo.minVal) / (sliderInfo.maxVal - sliderInfo.minVal), 0, 1)
        end

        -- Defer if GUI elements are not ready (AbsoluteSize might be 0)
        if sliderInfo.track and sliderInfo.track.Parent and sliderInfo.handle and sliderInfo.handle.Parent and sliderInfo.track.AbsoluteSize.X > 0 then
            local trackWidth = sliderInfo.track.AbsoluteSize.X - sliderInfo.handle.AbsoluteSize.X
            if trackWidth > 0 then
                local initialX = math.clamp(fraction * trackWidth, 0, trackWidth)
                sliderInfo.handle.Position = UDim2.fromOffset(initialX + sliderInfo.handle.AbsoluteSize.X / 2, sliderInfo.handle.Position.Y.Offset)
                sliderInfo.valueLabel.Text = string.format(sliderInfo.formatString or "%s", currentVal)
                if sliderInfo.filledTrack then sliderInfo.filledTrack.Size = UDim2.new(fraction, 0, 1, 0) end
            else -- Track not fully rendered, defer
                task.defer(HG.SliderManager.initializeSlider, sliderInfo, updateFromGlobalSettings)
            end
        else -- Elements not ready, defer
            task.defer(HG.SliderManager.initializeSlider, sliderInfo, updateFromGlobalSettings)
        end
    end

    -- Mouse Manager Functions
    function HG.MouseManager.toggleMouseLock()
        local State = HG.State
        local Services = HG.Services
        local GUI = HG.State.GUI_REFERENCES

        if not GUI.ScreenGui or not GUI.ScreenGui.Parent then return end -- GUI not available
        State.isMouseUnlocked = not State.isMouseUnlocked
        if State.isMouseUnlocked then
            Services.UserInputService.MouseBehavior = Enum.MouseBehavior.Default
            Services.UserInputService.MouseIconEnabled = true
        else -- Locking mouse
            if State.freecamActive then -- If freecam is active, lock center for camera control
                Services.UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
                Services.UserInputService.MouseIconEnabled = false
            else -- Normal gameplay lock
                Services.UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
                Services.UserInputService.MouseIconEnabled = false
            end
        end
        Services.StarterGui:SetCore("SendNotification", {Title="Cursor", Text=State.isMouseUnlocked and "Unlocked" or "Locked", Duration=2})
    end

    -- UI Manager: Minimize/Restore and Section Switching
    function HG.UIManager.minimizeGUISmooth()
        local State = HG.State
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local Utils = HG.Utils

        if State.isLoading or State.isMinimized or not GUI.MainFrame then return end
        State.isMinimized = true
        Utils.updateBlurEffectState() -- Adjust blur for minimized state
        State.storedSize = GUI.MainFrame.Size -- Store current size/pos for restore
        State.storedPosition = GUI.MainFrame.Position

        local targetCirclePos = GUI.MinimizeCircleButton.Position
        local targetSize = UDim2.fromOffset(Config.CONSTANTS.MINIMIZE_CIRCLE_SIZE, Config.CONSTANTS.MINIMIZE_CIRCLE_SIZE)
        
        -- Fade out content frames first
        local contentFadeTweens = {}
        if GUI.TitleFrame then table.insert(contentFadeTweens, Utils.playTween(GUI.TitleFrame, Config.TWEEN_INFOS.TWEEN_FAST, { BackgroundTransparency = 1 }, "MinimizeContentTitle")) end
        if GUI.SidebarFrame then table.insert(contentFadeTweens, Utils.playTween(GUI.SidebarFrame, Config.TWEEN_INFOS.TWEEN_FAST, { BackgroundTransparency = 1 }, "MinimizeContentSidebar")) end
        if GUI.MainContentAreaFrame then table.insert(contentFadeTweens, Utils.playTween(GUI.MainContentAreaFrame, Config.TWEEN_INFOS.TWEEN_FAST, { BackgroundTransparency = 1 }, "MinimizeContentArea")) end
        
        local function onContentFaded()
            if not State.isMinimized or not GUI.MainFrame then return end -- State changed during tween
            -- Then animate MainFrame to shrink to the circle's position/size
            local mainMinimizeTween = Utils.playTween(GUI.MainFrame, Config.TWEEN_INFOS.TWEEN_MINIMIZE, { Size = targetSize, Position = targetCirclePos, BackgroundTransparency = 1 }, "MinimizeMain")
            
            local function showMinimizeCircle()
                 if State.isMinimized then -- Ensure still minimized
                    if GUI.TitleFrame then GUI.TitleFrame.Visible = false end
                    if GUI.SidebarFrame then GUI.SidebarFrame.Visible = false end
                    if GUI.MainContentAreaFrame then GUI.MainContentAreaFrame.Visible = false end
                    if GUI.MainFrame then GUI.MainFrame.Visible = false end
                    if GUI.MinimizeCircleButton then
                        GUI.MinimizeCircleButton.Position = targetCirclePos -- Ensure correct position
                        GUI.MinimizeCircleButton.Visible = true
                        Utils.playTween(GUI.MinimizeCircleButton, Config.TWEEN_INFOS.TWEEN_FAST, {BackgroundTransparency = 0}, "ShowCircle")
                    end
                end
            end

            if mainMinimizeTween then mainMinimizeTween.Completed:Connect(showMinimizeCircle)
            else -- Fallback if tween fails to create
                task.wait(Config.TWEEN_INFOS.TWEEN_MINIMIZE.Time)
                showMinimizeCircle()
            end
        end
        
        -- Wait for all content fade tweens to complete
        if #contentFadeTweens > 0 then
            local completedCount = 0
            local requiredCount = 0
            for _, tween in ipairs(contentFadeTweens) do if tween then requiredCount = requiredCount + 1 end end
            if requiredCount == 0 then onContentFaded() -- No valid tweens
            else
                for _, tween in ipairs(contentFadeTweens) do
                    if tween then
                        tween.Completed:Connect(function()
                            completedCount = completedCount + 1
                            if completedCount == requiredCount then onContentFaded() end
                        end)
                    end
                end
            end
        else
            onContentFaded() -- No content tweens needed
        end
    end

    function HG.UIManager.restoreGUISmooth()
        local State = HG.State
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local Utils = HG.Utils

        if State.isLoading or not State.isMinimized or not GUI.MainFrame then return end
        State.isMinimized = false
        Utils.updateBlurEffectState() -- Adjust blur for restored state

        local startPos = GUI.MinimizeCircleButton.Position -- Start animation from circle's current pos
        local startSize = GUI.MinimizeCircleButton.Size
        
        -- Fade out the minimize circle button
        local hideCircleTween = Utils.playTween(GUI.MinimizeCircleButton, Config.TWEEN_INFOS.TWEEN_FAST, {BackgroundTransparency = 1}, "HideCircle")
        
        local function afterCircleHidden()
            if State.isMinimized or not GUI.MainFrame then return end -- State changed during tween
            if GUI.MinimizeCircleButton then GUI.MinimizeCircleButton.Visible = false end

            -- Prepare MainFrame for restore animation
            GUI.MainFrame.Position = startPos
            GUI.MainFrame.Size = startSize
            GUI.MainFrame.BackgroundTransparency = 1 -- Start transparent
            GUI.MainFrame.Visible = true
            
            -- Make content frames visible but transparent
            if GUI.TitleFrame then GUI.TitleFrame.BackgroundTransparency = 1; GUI.TitleFrame.Visible = true end
            if GUI.SidebarFrame then GUI.SidebarFrame.BackgroundTransparency = 1; GUI.SidebarFrame.Visible = true end
            if GUI.MainContentAreaFrame then GUI.MainContentAreaFrame.BackgroundTransparency = 1; GUI.MainContentAreaFrame.Visible = true end

            -- Animate MainFrame and content frames back to original state
            Utils.playTween(GUI.MainFrame, Config.TWEEN_INFOS.TWEEN_MINIMIZE, { Size = State.storedSize, Position = State.storedPosition, BackgroundTransparency = 0 }, "RestoreMain")
            if GUI.TitleFrame then Utils.playTween(GUI.TitleFrame, Config.TWEEN_INFOS.TWEEN_MINIMIZE, { BackgroundTransparency = 0 }, "RestoreContentTitle") end
            if GUI.SidebarFrame then Utils.playTween(GUI.SidebarFrame, Config.TWEEN_INFOS.TWEEN_MINIMIZE, { BackgroundTransparency = 0 }, "RestoreContentSidebar") end
            if GUI.MainContentAreaFrame then Utils.playTween(GUI.MainContentAreaFrame, Config.TWEEN_INFOS.TWEEN_MINIMIZE, { BackgroundTransparency = 0 }, "RestoreContentArea") end
        end
        
        if hideCircleTween then hideCircleTween.Completed:Connect(afterCircleHidden)
        else afterCircleHidden() -- Fallback if tween fails
        end
    end

    function HG.UIManager.switchSection(buttonToShow)
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES
        local UIManager = HG.UIManager

        if State.isLoading then return end
        
        local targetSectionFrameParent -- The parent Frame of the ScrollingFrame
        local targetSectionScrollableContent -- The ScrollingFrame itself
        
        if buttonToShow == GUI.ESPSectionButton then
            targetSectionFrameParent = GUI.ESPSection
            targetSectionScrollableContent = GUI.ESPScrollFrame
        elseif buttonToShow == GUI.AimSectionButton then
            targetSectionFrameParent = GUI.AimSection
            targetSectionScrollableContent = GUI.AimScrollFrame
        elseif buttonToShow == GUI.ConfigSectionButton then
            targetSectionFrameParent = GUI.ConfigSection
            targetSectionScrollableContent = GUI.ConfigContent
        elseif buttonToShow == GUI.DevSectionButton then
            targetSectionFrameParent = GUI.DevSection
            targetSectionScrollableContent = GUI.DevScrollFrame
        else
            return -- Unknown button
        end
        
        if State.currentVisibleSectionFrame == targetSectionScrollableContent then return end -- Already on this section
        
        -- Hide current section
        if State.currentVisibleSectionFrame and State.currentVisibleSectionFrame.Parent then
            State.currentVisibleSectionFrame.Parent.Visible = false
            State.currentVisibleSectionFrame.BackgroundTransparency = 1 -- Make it transparent for next show
        end
        
        -- Show new section
        targetSectionFrameParent.Visible = true
        targetSectionScrollableContent.BackgroundTransparency = 0 -- Make it opaque
        
        State.currentVisibleSectionFrame = targetSectionScrollableContent
        State.activeSectionButton = buttonToShow
        UIManager.applySettingsToGUI() -- Update button states for sidebar, etc.
    end

    -- Applies all current settings from HG.Config.settings to the GUI elements
    function HG.UIManager.applySettingsToGUI()
        local Config = HG.Config
        local GUI = HG.State.GUI_REFERENCES
        local UIManager = HG.UIManager
        local SliderManager = HG.SliderManager
        local FOVManager = HG.FOVManager -- For FOV arrows update
        local State = HG.State

        -- Update toggle button states
        if GUI.PlayerESPToggle then UIManager.updateButtonState(GUI.PlayerESPToggle, Config.settings.playerESPEnabled) end
        if GUI.ESPLinesToggle then UIManager.updateButtonState(GUI.ESPLinesToggle, Config.settings.espLinesEnabled) end
        if GUI.ShowHealthToggle then UIManager.updateButtonState(GUI.ShowHealthToggle, Config.settings.showHealthBar) end
        if GUI.SkeletonESPToggle then UIManager.updateButtonState(GUI.SkeletonESPToggle, Config.settings.skeletonESPEnabled) end
        if GUI.ESPRadarToggle then UIManager.updateButtonState(GUI.ESPRadarToggle, Config.settings.espRadarEnabled) end
        if GUI.AimbotToggle then UIManager.updateButtonState(GUI.AimbotToggle, Config.settings.aimbotEnabled) end
        if GUI.FOVToggle then UIManager.updateButtonState(GUI.FOVToggle, Config.settings.showFOV) end
        if GUI.CrosshairToggle then UIManager.updateButtonState(GUI.CrosshairToggle, Config.settings.showCrosshair) end
        if GUI.BlurToggle then UIManager.updateButtonState(GUI.BlurToggle, Config.settings.backgroundBlurEnabled) end
        if GUI.TargetToggle then UIManager.updateButtonState(GUI.TargetToggle, Config.settings.targetHead) end
        if GUI.AimbotWallCheckToggle then UIManager.updateButtonState(GUI.AimbotWallCheckToggle, Config.settings.aimbotWallCheckEnabled) end
        if GUI.AimbotTargetLineToggle then UIManager.updateButtonState(GUI.AimbotTargetLineToggle, Config.settings.aimbotShowTargetLine) end
        if GUI.AimbotPredictionToggle then UIManager.updateButtonState(GUI.AimbotPredictionToggle, Config.settings.aimbotPredictionEnabled) end
        if GUI.OffScreenArrowsToggle then UIManager.updateButtonState(GUI.OffScreenArrowsToggle, Config.settings.showOffScreenArrows) end
        if GUI.DevFreecamToggle then UIManager.updateButtonState(GUI.DevFreecamToggle, Config.settings.devFreecamEnabled) end
        if GUI.DevBrightSkyToggle then UIManager.updateButtonState(GUI.DevBrightSkyToggle, Config.settings.devBrightSkyEnabled) end

        -- Update aimbot key buttons
        if GUI.AimbotKeyLShiftButton then UIManager.updateButtonState(GUI.AimbotKeyLShiftButton, Config.settings.aimbotActivationKey == Enum.KeyCode.LeftShift) end
        if GUI.AimbotKeyRMBButton then UIManager.updateButtonState(GUI.AimbotKeyRMBButton, Config.settings.aimbotActivationKey == Enum.UserInputType.MouseButton2) end

        -- Update theme buttons
        if GUI.DarkThemeButton then UIManager.updateButtonState(GUI.DarkThemeButton, Config.settings.currentTheme == "Dark") end
        if GUI.LightThemeButton then UIManager.updateButtonState(GUI.LightThemeButton, Config.settings.currentTheme == "Light") end

        -- Update sidebar section buttons
        if GUI.ESPSectionButton then UIManager.updateButtonState(GUI.ESPSectionButton, GUI.ESPSectionButton == State.activeSectionButton) end
        if GUI.AimSectionButton then UIManager.updateButtonState(GUI.AimSectionButton, GUI.AimSectionButton == State.activeSectionButton) end
        if GUI.ConfigSectionButton then UIManager.updateButtonState(GUI.ConfigSectionButton, GUI.ConfigSectionButton == State.activeSectionButton) end
        if GUI.DevSectionButton then UIManager.updateButtonState(GUI.DevSectionButton, GUI.DevSectionButton == State.activeSectionButton) end
        
        -- Initialize/Update sliders to reflect current settings
        if GUI.SensSliderHandle and State.sensSliderInfo then SliderManager.initializeSlider(State.sensSliderInfo, true) end
        if GUI.FOVSliderHandle and State.fovSliderInfo then SliderManager.initializeSlider(State.fovSliderInfo, true) end
        if GUI.RadarSizeSliderHandle and State.radarSizeSliderInfo then SliderManager.initializeSlider(State.radarSizeSliderInfo, true) end
        if GUI.RadarRangeSliderHandle and State.radarRangeSliderInfo then SliderManager.initializeSlider(State.radarRangeSliderInfo, true) end
        if GUI.FOVThicknessSliderHandle and State.fovThicknessSliderInfo then SliderManager.initializeSlider(State.fovThicknessSliderInfo, true) end
        if GUI.ESPDistanceSliderHandle and State.espDistanceSliderInfo then SliderManager.initializeSlider(State.espDistanceSliderInfo, true) end
        if GUI.AimbotPredictionStrengthSliderHandle and State.aimbotPredictionStrengthSliderInfo then SliderManager.initializeSlider(State.aimbotPredictionStrengthSliderInfo, true) end

        -- Update visuals for FOV circle, crosshair, radar
        if GUI.FOVCircleFrame then
            GUI.FOVCircleFrame.Visible = Config.settings.showFOV
            GUI.FOVCircleFrame.Size = UDim2.fromOffset(Config.settings.fovSize, Config.settings.fovSize)
            GUI.FOVCircleFrame.Position = UDim2.new(0.5, 0, 0.5, 0) -- Ensure centered
        end
        if GUI.FOVStroke then
            GUI.FOVStroke.Color = Config.settings.fovCircleColor
            GUI.FOVStroke.Thickness = Config.settings.fovCircleThickness
        end
        if GUI.CrosshairFrameH then
            GUI.CrosshairFrameH.Visible = Config.settings.showCrosshair
            GUI.CrosshairFrameH.Position = UDim2.new(0.5,0,0.5,0) -- Ensure centered
        end
        if GUI.CrosshairFrameV then
            GUI.CrosshairFrameV.Visible = Config.settings.showCrosshair
            GUI.CrosshairFrameV.Position = UDim2.new(0.5,0,0.5,0) -- Ensure centered
        end
        if GUI.RadarFrame then
            GUI.RadarFrame.Size = UDim2.fromOffset(Config.settings.espRadarSize, Config.settings.espRadarSize)
            GUI.RadarFrame.Position = UDim2.new(0, 15, 0, (GUI.TitleFrame and GUI.TitleFrame.AbsoluteSize.Y or 40) + 15) -- Reposition if title bar height changes
            GUI.RadarFrame.Visible = Config.settings.playerESPEnabled and Config.settings.espRadarEnabled
        end
        
        -- Update ESP and FOV color pickers
        UIManager.updateESPColorVisuals(Config.settings.espOutlineColor)
        UIManager.updateFOVColorVisuals(Config.settings.fovCircleColor)
        
        -- Ensure title bar buttons are correctly styled
        local theme = Config.THEME_SETTINGS[Config.settings.currentTheme] or Config.THEME_SETTINGS.Dark
        if GUI.ToggleButton then GUI.ToggleButton.Text = "-" end -- Minimize button text
        if GUI.CloseButton then
            GUI.CloseButton.BackgroundColor3 = theme.ButtonBG_Close
            GUI.CloseButton:SetAttribute("OriginalColor", theme.ButtonBG_Close)
            GUI.CloseButton.TextColor3 = theme.ButtonText
        end
    end

    -- Initialize all UI elements first
    HG.UI_InitializeActualElements()

    -- Connect Events and Finalize GUI Setup
    HG.Events_ConnectAll_And_Finalize = function()
        local Services = HG.Services
        local PlayerRefs = HG.PlayerRefs
        local Config = HG.Config
        local State = HG.State
        local GUI = HG.State.GUI_REFERENCES
        local Utils = HG.Utils
        local UIManager = HG.UIManager
        local SliderManager = HG.SliderManager
        local MouseManager = HG.MouseManager
        local DevModeManager = HG.DevModeManager
        local ESPManager = HG.ESPManager
        local AimbotManager = HG.AimbotManager
        local FOVManager = HG.FOVManager

        -- Main RenderStepped loop for continuous updates (ESP, Radar, Arrows, Target Line)
        Services.RunService.RenderStepped:Connect(function(deltaTime)
            if State.isLoading then return end
            local currentTime = tick()

            -- ESP updates (full check periodically, individual box updates every frame)
            if Config.settings.playerESPEnabled or Config.settings.skeletonESPEnabled or Config.settings.espRadarEnabled then
                if currentTime - State.lastFullESPCheck > Config.CONSTANTS.ESP_CHECK_INTERVAL then
                    pcall(ESPManager.checkAndUpdateESP) -- Full check for new/removed players
                    State.lastFullESPCheck = currentTime
                end
            elseif next(State.espElements) or next(State.radarDots) then -- If ESP disabled but elements exist, clear them
                pcall(ESPManager.clearAllESP)
                State.lastFullESPCheck = currentTime
            end

            if Config.settings.playerESPEnabled or Config.settings.skeletonESPEnabled then
                for player, element in pairs(State.espElements) do
                    if element and element.Character and element.Character.Parent then
                        pcall(ESPManager.update2DBox, player, element) -- Update individual player ESP box
                    elseif element then -- Element exists but character is invalid
                        ESPManager.removePlayerESP(player)
                    end
                end
            end
            
            if Config.settings.playerESPEnabled and Config.settings.espRadarEnabled and GUI.RadarFrame and GUI.RadarFrame.Visible then
                pcall(ESPManager.updateRadarPositions)
            end
            
            -- Off-screen FOV arrows
            if Config.settings.showOffScreenArrows then
                pcall(FOVManager.updateOffScreenArrows)
            end

            -- Aimbot Target Line
            if GUI.AimbotTargetLineFrame then
                if Config.settings.aimbotShowTargetLine and Config.settings.aimbotEnabled and State.currentAimbotTarget and PlayerRefs.LocalPlayer.Character then
                    local targetPlayer = State.currentAimbotTarget
                    local targetCharacter = targetPlayer.Character
                    if targetCharacter then
                        local partToDrawToName = Config.settings.targetHead and "Head" or "HumanoidRootPart"
                        local partToDrawTo = targetCharacter:FindFirstChild(partToDrawToName) or targetCharacter:FindFirstChild("UpperTorso")
                        
                        if partToDrawTo then
                            local targetPosWorld = partToDrawTo.Position
                            -- Apply prediction to target line if enabled
                            if Config.settings.aimbotPredictionEnabled then
                                local targetHRP = targetCharacter:FindFirstChild("HumanoidRootPart")
                                if targetHRP then
                                    local targetVelocity = targetHRP.Velocity
                                    local predictionOffset = targetVelocity * Config.settings.aimbotPredictionStrength
                                    targetPosWorld = targetPosWorld + predictionOffset
                                end
                            end
                            
                            local viewportSize = PlayerRefs.Camera.ViewportSize
                            local startPosScreen = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2) -- From center of screen
                            
                            local endPosScreenVec3, onScreen = PlayerRefs.Camera:WorldToViewportPoint(targetPosWorld)
                            if onScreen and endPosScreenVec3.Z > 0 then
                                local endPosScreen = Vector2.new(endPosScreenVec3.X, endPosScreenVec3.Y)
                                local delta = endPosScreen - startPosScreen
                                local distance = delta.Magnitude
                                if distance > 1 then -- Only draw if reasonably long
                                    local angle = math.atan2(delta.Y, delta.X)
                                    GUI.AimbotTargetLineFrame.Size = UDim2.fromOffset(distance, Config.CONSTANTS.AIMBOT_TARGET_LINE_THICKNESS)
                                    GUI.AimbotTargetLineFrame.Position = UDim2.fromOffset(startPosScreen.X + delta.X / 2, startPosScreen.Y + delta.Y / 2)
                                    GUI.AimbotTargetLineFrame.Rotation = math.deg(angle)
                                    GUI.AimbotTargetLineFrame.Visible = true
                                else
                                    GUI.AimbotTargetLineFrame.Visible = false
                                end
                            else GUI.AimbotTargetLineFrame.Visible = false
                            end
                        else GUI.AimbotTargetLineFrame.Visible = false
                        end
                    else GUI.AimbotTargetLineFrame.Visible = false
                    end
                else GUI.AimbotTargetLineFrame.Visible = false
                end
            end
        end)

        -- Player lifecycle events for ESP
        Services.Players.PlayerAdded:Connect(function(p)
            p.CharacterAdded:Connect(function(c)
                task.wait(0.1) -- Wait a bit for character to fully load
                State.lastFullESPCheck = 0 -- Trigger a full ESP check soon
            end)
        end)
        Services.Players.PlayerRemoving:Connect(function(p)
            ESPManager.removePlayerESP(p)
            if State.currentAimbotTarget == p then State.currentAimbotTarget = nil end -- Clear aimbot target if it was this player
        end)
        
        -- Input handling for dragging main frame
        GUI.TitleFrame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                if GUI.MainFrame then
                    State.draggingMainFrame = true
                    State.mainFrameDragStartMouse = input.Position -- Store initial mouse position
                    State.mainFrameDragStartPos = GUI.MainFrame.Position -- Store initial frame position
                end
            end
        end)

        -- Global InputBegan (F1, F2 keys)
        Services.UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
            if State.isLoading then return end
            -- Allow F1/F2 even if gameProcessedEvent is true, unless in freecam and it's not F1/F2
            if gameProcessedEvent and not (State.freecamActive and (input.KeyCode == Enum.KeyCode.F1 or input.KeyCode == Enum.KeyCode.F2)) then
                if input.KeyCode ~= Enum.KeyCode.F1 and input.KeyCode ~= Enum.KeyCode.F2 then
                    return
                end
            end

            if input.KeyCode == Enum.KeyCode.F2 then
                MouseManager.toggleMouseLock()
            elseif input.KeyCode == Enum.KeyCode.F1 then
                if not GUI.ScreenGui or not GUI.ScreenGui.Parent then return end
                if State.isMinimized then UIManager.restoreGUISmooth() else UIManager.minimizeGUISmooth() end
            end
        end)

        -- Global InputChanged (mouse movement for dragging, sliders, freecam look)
        Services.UserInputService.InputChanged:Connect(function(input)
            if State.isLoading then return end

            if State.isDraggingCircle and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                local delta = input.Position - State.dragStartPos
                GUI.MinimizeCircleButton.Position = UDim2.new(
                    State.startCirclePos.X.Scale, State.startCirclePos.X.Offset + delta.X,
                    State.startCirclePos.Y.Scale, State.startCirclePos.Y.Offset + delta.Y
                )
            elseif State.draggingSlider and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                SliderManager.updateSlider(State.draggingSlider, input.Position.X)
            elseif State.freecamActive and input.UserInputType == Enum.UserInputType.MouseMovement and (not State.isMouseUnlocked or State.isMinimized) then
                local delta = Services.UserInputService:GetMouseDelta()
                local currentRotation = PlayerRefs.Camera.CFrame - PlayerRefs.Camera.CFrame.Position -- Get rotational part
                local newRotation = CFrame.Angles(0, -delta.X * Config.CONSTANTS.FREECAM_LOOK_SPEED, 0) * currentRotation * CFrame.Angles(-delta.Y * Config.CONSTANTS.FREECAM_LOOK_SPEED, 0, 0)
                PlayerRefs.Camera.CFrame = CFrame.new(PlayerRefs.Camera.CFrame.Position) * newRotation
            elseif State.draggingMainFrame and GUI.MainFrame and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                local delta = input.Position - State.mainFrameDragStartMouse
                GUI.MainFrame.Position = UDim2.new(
                    State.mainFrameDragStartPos.X.Scale, State.mainFrameDragStartPos.X.Offset + delta.X,
                    State.mainFrameDragStartPos.Y.Scale, State.mainFrameDragStartPos.Y.Offset + delta.Y
                )
                -- Update radar position if main frame moves, as it's relative to title bar
                if GUI.RadarFrame and GUI.TitleFrame then
                    GUI.RadarFrame.Position = UDim2.new(0, 15, 0, GUI.TitleFrame.AbsoluteSize.Y + 15)
                end
            end
        end)

        -- Global InputEnded (stop dragging/slider interaction)
        Services.UserInputService.InputEnded:Connect(function(input)
            if State.isLoading then return end
            if State.isDraggingCircle and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
                State.isDraggingCircle = false
                State.dragStartPos = nil
                State.startCirclePos = nil
                if GUI.MainFrame then GUI.MainFrame.Active = false end -- Make main frame non-interactive after drag
                if GUI.TitleFrame then GUI.TitleFrame.Active = true end -- Re-enable title frame interaction
            elseif State.draggingSlider and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
                State.draggingSlider = nil -- Stop dragging slider
            elseif State.draggingMainFrame and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
                State.draggingMainFrame = false -- Stop dragging main frame
            end
        end)

        -- Title bar buttons
        GUI.ToggleButton.MouseButton1Click:Connect(UIManager.minimizeGUISmooth)
        GUI.MinimizeCircleButton.MouseButton1Click:Connect(UIManager.restoreGUISmooth)
        GUI.MinimizeCircleButton.InputBegan:Connect(function(input) -- For dragging minimized circle
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                State.isDraggingCircle = true
                State.dragStartPos = input.Position
                State.startCirclePos = GUI.MinimizeCircleButton.Position
                if GUI.MainFrame then GUI.MainFrame.Active = false end -- Disable main frame interaction during this drag
                if GUI.TitleFrame then GUI.TitleFrame.Active = false end
            end
        end)
        GUI.CloseButton.MouseButton1Click:Connect(function()
            if State.isLoading or not GUI.MainFrame then return end
            -- Ensure mouse is unlocked before closing
            if State.isMouseUnlocked then
                pcall(function() Services.UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter; Services.UserInputService.MouseIconEnabled = false; end)
                State.isMouseUnlocked = false
            end
            -- Disable dev features
            if Config.settings.devFreecamEnabled then DevModeManager.setFreecamState(false) end
            if Config.settings.devBrightSkyEnabled then DevModeManager.setBrightSkyState(false) end
            
            if GUI.MinimizeCircleButton and GUI.MinimizeCircleButton.Parent then GUI.MinimizeCircleButton:Destroy(); GUI.MinimizeCircleButton = nil end
            
            -- Animate close
            local closeTween = Utils.playTween(GUI.MainFrame, Config.TWEEN_INFOS.TWEEN_FAST, { BackgroundTransparency = 1 }, "CloseMainFrame")
            local function finishClose()
                -- Reset all relevant settings to defaults or off
                Config.settings.playerESPEnabled = false; Config.settings.aimbotEnabled = false; Config.settings.showFOV = false
                Config.settings.showCrosshair = false; Config.settings.espLinesEnabled = false; Config.settings.showHealthBar = false
                Config.settings.espRadarEnabled = false; Config.settings.skeletonESPEnabled = false; Config.settings.showOffScreenArrows = false
                State.currentAimbotTarget = nil
                Config.settings.devFreecamEnabled = false; Config.settings.devBrightSkyEnabled = false
                Config.settings.aimbotWallCheckEnabled = Config.DEFAULT_SETTINGS.aimbotWallCheckEnabled
                Config.settings.aimbotShowTargetLine = Config.DEFAULT_SETTINGS.aimbotShowTargetLine
                Config.settings.aimbotPredictionEnabled = Config.DEFAULT_SETTINGS.aimbotPredictionEnabled
                Config.settings.aimbotPredictionStrength = Config.DEFAULT_SETTINGS.aimbotPredictionStrength
                
                if State.aimbotConnection then State.aimbotConnection:Disconnect(); State.aimbotConnection = nil end
                ESPManager.clearAllESP()
                if State.backgroundBlurEffect and State.backgroundBlurEffect.Parent then State.backgroundBlurEffect.Enabled = false end
                
                -- Destroy GUI
                if GUI.ScreenGui and GUI.ScreenGui.Parent then GUI.ScreenGui:Destroy(); GUI.ScreenGui = nil; HG.State.GUI_REFERENCES = {} end
                Services.StarterGui:SetCore("SendNotification", {Title="KONOSUBA_GUI Closed", Text="All features have been disabled.", Duration=3})
            end
            if closeTween then closeTween.Completed:Connect(finishClose) else finishClose() end -- Fallback
        end)

        -- Toggle button connections
        GUI.PlayerESPToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.playerESPEnabled = not Config.settings.playerESPEnabled
            UIManager.updateButtonState(GUI.PlayerESPToggle, Config.settings.playerESPEnabled)
            if not Config.settings.playerESPEnabled and not Config.settings.skeletonESPEnabled then ESPManager.clearAllESP() else State.lastFullESPCheck = 0 end
            if GUI.RadarFrame then GUI.RadarFrame.Visible = Config.settings.playerESPEnabled and Config.settings.espRadarEnabled end
            Services.StarterGui:SetCore("SendNotification",{Title="Player ESP",Text=Config.settings.playerESPEnabled and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.ESPLinesToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.espLinesEnabled = not Config.settings.espLinesEnabled
            UIManager.updateButtonState(GUI.ESPLinesToggle, Config.settings.espLinesEnabled)
            Services.StarterGui:SetCore("SendNotification",{Title="ESP Lines",Text=Config.settings.espLinesEnabled and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.ShowHealthToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.showHealthBar = not Config.settings.showHealthBar
            UIManager.updateButtonState(GUI.ShowHealthToggle, Config.settings.showHealthBar)
            Services.StarterGui:SetCore("SendNotification",{Title="ESP Health Bar",Text=Config.settings.showHealthBar and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.SkeletonESPToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.skeletonESPEnabled = not Config.settings.skeletonESPEnabled
            UIManager.updateButtonState(GUI.SkeletonESPToggle, Config.settings.skeletonESPEnabled)
            if not Config.settings.skeletonESPEnabled and not Config.settings.playerESPEnabled then
                ESPManager.clearAllESP()
            else
                State.lastFullESPCheck = 0 -- Force update
                if not Config.settings.skeletonESPEnabled then -- If disabling, hide existing skeletons
                    for p, e in pairs(State.espElements) do
                        if e and e.SkeletonElements then ESPManager.hideSkeleton(e.SkeletonElements) end
                    end
                end
            end
            Services.StarterGui:SetCore("SendNotification",{Title="Skeleton ESP", Text=Config.settings.skeletonESPEnabled and "Enabled" or "Disabled", Duration=2})
        end)
        GUI.ESPRadarToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.espRadarEnabled = not Config.settings.espRadarEnabled
            UIManager.updateButtonState(GUI.ESPRadarToggle, Config.settings.espRadarEnabled)
            if GUI.RadarFrame then
                GUI.RadarFrame.Visible = Config.settings.playerESPEnabled and Config.settings.espRadarEnabled
                if not Config.settings.espRadarEnabled then -- Clear radar dots if disabling
                    for p, dotInfo in pairs(State.radarDots) do if dotInfo and dotInfo.Dot and dotInfo.Dot.Parent then pcall(dotInfo.Dot.Destroy, dotInfo.Dot) end end
                    State.radarDots = {}
                else
                    State.lastFullESPCheck = 0 -- Force update to create dots if needed
                    ESPManager.checkAndUpdateESP()
                end
            end
            Services.StarterGui:SetCore("SendNotification",{Title="ESP Radar",Text=Config.settings.espRadarEnabled and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.AimbotToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.aimbotEnabled = not Config.settings.aimbotEnabled
            UIManager.updateButtonState(GUI.AimbotToggle, Config.settings.aimbotEnabled)
            AimbotManager.setupAimbotConnection() -- Connect/disconnect RenderStepped
            local keyText = "Key"
            if Config.settings.aimbotActivationKey == Enum.KeyCode.LeftShift then keyText = "LShift"
            elseif Config.settings.aimbotActivationKey == Enum.UserInputType.MouseButton2 then keyText = "RMB" end
            Services.StarterGui:SetCore("SendNotification",{Title="Aimbot",Text=Config.settings.aimbotEnabled and("Enabled (Hold "..keyText..")")or"Disabled",Duration=2})
        end)
        GUI.TargetToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.targetHead = not Config.settings.targetHead
            UIManager.updateButtonState(GUI.TargetToggle, Config.settings.targetHead)
            Services.StarterGui:SetCore("SendNotification",{Title="Aimbot Target",Text=Config.settings.targetHead and"Targeting Head"or"Targeting Body",Duration=2})
        end)
        GUI.AimbotWallCheckToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.aimbotWallCheckEnabled = not Config.settings.aimbotWallCheckEnabled
            UIManager.updateButtonState(GUI.AimbotWallCheckToggle, Config.settings.aimbotWallCheckEnabled)
            Services.StarterGui:SetCore("SendNotification", {Title="Aimbot Wall Check", Text = Config.settings.aimbotWallCheckEnabled and "Enabled" or "Disabled", Duration = 2})
        end)
        GUI.AimbotTargetLineToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.aimbotShowTargetLine = not Config.settings.aimbotShowTargetLine
            UIManager.updateButtonState(GUI.AimbotTargetLineToggle, Config.settings.aimbotShowTargetLine)
            Services.StarterGui:SetCore("SendNotification", {Title="Aimbot Target Line", Text = Config.settings.aimbotShowTargetLine and "Visible" or "Hidden", Duration = 2})
        end)
        GUI.AimbotPredictionToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.aimbotPredictionEnabled = not Config.settings.aimbotPredictionEnabled
            UIManager.updateButtonState(GUI.AimbotPredictionToggle, Config.settings.aimbotPredictionEnabled)
            Services.StarterGui:SetCore("SendNotification", {Title="Aimbot Prediction", Text = Config.settings.aimbotPredictionEnabled and "Enabled" or "Disabled", Duration = 2})
        end)
        GUI.FOVToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.showFOV = not Config.settings.showFOV
            UIManager.updateButtonState(GUI.FOVToggle, Config.settings.showFOV)
            if GUI.FOVCircleFrame then GUI.FOVCircleFrame.Visible = Config.settings.showFOV end
            FOVManager.updateOffScreenArrows() -- Update arrows based on FOV visibility
            Services.StarterGui:SetCore("SendNotification",{Title="Aim FOV Circle",Text=Config.settings.showFOV and"Visible"or"Hidden",Duration=2})
        end)
        GUI.CrosshairToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.showCrosshair = not Config.settings.showCrosshair
            UIManager.updateButtonState(GUI.CrosshairToggle, Config.settings.showCrosshair)
            if GUI.CrosshairFrameH then GUI.CrosshairFrameH.Visible = Config.settings.showCrosshair end
            if GUI.CrosshairFrameV then GUI.CrosshairFrameV.Visible = Config.settings.showCrosshair end
            Services.StarterGui:SetCore("SendNotification",{Title="Crosshair",Text=Config.settings.showCrosshair and"Visible"or"Hidden",Duration=2})
        end)
        GUI.OffScreenArrowsToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.showOffScreenArrows = not Config.settings.showOffScreenArrows
            UIManager.updateButtonState(GUI.OffScreenArrowsToggle, Config.settings.showOffScreenArrows)
            FOVManager.updateOffScreenArrows() -- Update arrows state
            Services.StarterGui:SetCore("SendNotification",{Title="FOV Arrows",Text=Config.settings.showOffScreenArrows and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.BlurToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            Config.settings.backgroundBlurEnabled = not Config.settings.backgroundBlurEnabled
            UIManager.updateButtonState(GUI.BlurToggle, Config.settings.backgroundBlurEnabled)
            Utils.updateBlurEffectState() -- Apply blur change
            Services.StarterGui:SetCore("SendNotification",{Title="Background Blur",Text=Config.settings.backgroundBlurEnabled and"Enabled"or"Disabled",Duration=2})
        end)
        GUI.DevFreecamToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            DevModeManager.setFreecamState(not Config.settings.devFreecamEnabled)
        end)
        GUI.DevBrightSkyToggle.MouseButton1Click:Connect(function()
            if State.isLoading then return end
            DevModeManager.setBrightSkyState(not Config.settings.devBrightSkyEnabled)
        end)
        
        -- Aimbot key selection
        local function switchAimbotKey(newKey)
            if State.isLoading then return end
            if Config.settings.aimbotActivationKey ~= newKey then
                Config.settings.aimbotActivationKey = newKey
                UIManager.updateButtonState(GUI.AimbotKeyLShiftButton, true) -- Update both buttons
                UIManager.updateButtonState(GUI.AimbotKeyRMBButton, true)
                local keyName = (newKey == Enum.KeyCode.LeftShift) and "LShift" or "RMB"
                Services.StarterGui:SetCore("SendNotification",{Title="Aimbot Key",Text="Set to "..keyName,Duration=2})
            end
        end
        GUI.AimbotKeyLShiftButton.MouseButton1Click:Connect(function() switchAimbotKey(Enum.KeyCode.LeftShift) end)
        GUI.AimbotKeyRMBButton.MouseButton1Click:Connect(function() switchAimbotKey(Enum.UserInputType.MouseButton2) end)

        -- Slider input connections
        local function sliderInputBegan(input, sliderHandle)
            if State.isLoading then return end
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                local sliderInfo = State.sliders[sliderHandle]
                if sliderInfo then
                    State.draggingSlider = sliderInfo
                    SliderManager.updateSlider(State.draggingSlider, input.Position.X) -- Initial update on click
                end
            end
        end
        GUI.SensSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.SensSliderHandle) end)
        GUI.FOVSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.FOVSliderHandle) end)
        GUI.FOVThicknessSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.FOVThicknessSliderHandle) end)
        GUI.RadarSizeSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.RadarSizeSliderHandle) end)
        GUI.RadarRangeSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.RadarRangeSliderHandle) end)
        GUI.ESPDistanceSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.ESPDistanceSliderHandle) end)
        GUI.AimbotPredictionStrengthSliderHandle.InputBegan:Connect(function(i) sliderInputBegan(i, GUI.AimbotPredictionStrengthSliderHandle) end)

        -- Color picker buttons
        local function updateESPColor(newColor) if State.isLoading then return end; UIManager.updateESPColorVisuals(newColor) end
        GUI.RedColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.RedColorButton.BackgroundColor3) end)
        GUI.WhiteColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.WhiteColorButton.BackgroundColor3) end)
        GUI.GreenColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.GreenColorButton.BackgroundColor3) end)
        GUI.BlueColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.BlueColorButton.BackgroundColor3) end)
        GUI.YellowColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.YellowColorButton.BackgroundColor3) end)
        GUI.PinkColorButton.MouseButton1Click:Connect(function() updateESPColor(GUI.PinkColorButton.BackgroundColor3) end)
        
        local function updateFOVColor(newColor) if State.isLoading then return end; UIManager.updateFOVColorVisuals(newColor) end
        GUI.FOVColorRedButton.MouseButton1Click:Connect(function() updateFOVColor(GUI.FOVColorRedButton.BackgroundColor3) end)
        GUI.FOVColorWhiteButton.MouseButton1Click:Connect(function() updateFOVColor(GUI.FOVColorWhiteButton.BackgroundColor3) end)
        GUI.FOVColorGreenButton.MouseButton1Click:Connect(function() updateFOVColor(GUI.FOVColorGreenButton.BackgroundColor3) end)
        GUI.FOVColorBlueButton.MouseButton1Click:Connect(function() updateFOVColor(GUI.FOVColorBlueButton.BackgroundColor3) end)
        GUI.FOVColorYellowButton.MouseButton1Click:Connect(function() updateFOVColor(GUI.FOVColorYellowButton.BackgroundColor3) end)

        -- Theme selection buttons
        GUI.DarkThemeButton.MouseButton1Click:Connect(function() if State.isLoading then return end; if Config.settings.currentTheme ~= "Dark" then UIManager.applyTheme("Dark") end end)
        GUI.LightThemeButton.MouseButton1Click:Connect(function() if State.isLoading then return end; if Config.settings.currentTheme ~= "Light" then UIManager.applyTheme("Light") end end)

        -- Sidebar section navigation buttons
        GUI.ESPSectionButton.MouseButton1Click:Connect(function() UIManager.switchSection(GUI.ESPSectionButton) end)
        GUI.AimSectionButton.MouseButton1Click:Connect(function() UIManager.switchSection(GUI.AimSectionButton) end)
        GUI.ConfigSectionButton.MouseButton1Click:Connect(function() UIManager.switchSection(GUI.ConfigSectionButton) end)
        GUI.DevSectionButton.MouseButton1Click:Connect(function() UIManager.switchSection(GUI.DevSectionButton) end)

        -- Define slider info tables (used by SliderManager)
        State.sensSliderInfo = {handle=GUI.SensSliderHandle, track=GUI.SensSliderTrack, valueLabel=GUI.SensSliderValue, filledTrack=GUI.SensFilledTrack, varName="SensSliderHandle", minVal=0.01, maxVal=1, defaultVal=Config.DEFAULT_SETTINGS.sensitivity, formatString="%.2f"}
        State.fovSliderInfo = {handle=GUI.FOVSliderHandle, track=GUI.FOVSliderTrack, valueLabel=GUI.FOVSliderValue, filledTrack=GUI.FOVSliderFilledTrack, varName="FOVSliderHandle", minVal=50, maxVal=800, defaultVal=Config.DEFAULT_SETTINGS.fovSize, formatString="%d px"}
        State.fovThicknessSliderInfo = {handle=GUI.FOVThicknessSliderHandle, track=GUI.FOVThicknessSliderTrack, valueLabel=GUI.FOVThicknessSliderValue, filledTrack=GUI.FOVThicknessFilledTrack, varName="FOVThicknessSliderHandle", minVal=1, maxVal=5, defaultVal=Config.DEFAULT_SETTINGS.fovCircleThickness, formatString="%.1f px"}
        State.radarSizeSliderInfo = {handle=GUI.RadarSizeSliderHandle, track=GUI.RadarSizeSliderTrack, valueLabel=GUI.RadarSizeSliderValue, filledTrack=GUI.RadarSizeFilledTrack, varName="RadarSizeSliderHandle", minVal=70, maxVal=300, defaultVal=Config.DEFAULT_SETTINGS.espRadarSize, formatString="%d px"}
        State.radarRangeSliderInfo = {handle=GUI.RadarRangeSliderHandle, track=GUI.RadarRangeSliderTrack, valueLabel=GUI.RadarRangeSliderValue, filledTrack=GUI.RadarRangeFilledTrack, varName="RadarRangeSliderHandle", minVal=50, maxVal=500, defaultVal=Config.DEFAULT_SETTINGS.espRadarRange, formatString="%d studs"}
        State.espDistanceSliderInfo = {handle=GUI.ESPDistanceSliderHandle, track=GUI.ESPDistanceSliderTrack, valueLabel=GUI.ESPDistanceSliderValue, filledTrack=GUI.ESPDistanceFilledTrack, varName="ESPDistanceSliderHandle", minVal=50, maxVal=1000, defaultVal=Config.DEFAULT_SETTINGS.espMaxDistance, formatString="%d studs"}
        State.aimbotPredictionStrengthSliderInfo = {handle=GUI.AimbotPredictionStrengthSliderHandle, track=GUI.AimbotPredictionStrengthSliderTrack, valueLabel=GUI.AimbotPredictionStrengthSliderValue, filledTrack=GUI.AimbotPredictionStrengthFilledTrack, varName="AimbotPredictionStrengthSliderHandle", minVal=0.00, maxVal=0.5, defaultVal=Config.DEFAULT_SETTINGS.aimbotPredictionStrength, formatString="%.2f"}

        -- Initial GUI state setup
        State.currentVisibleSectionFrame = GUI.ESPScrollFrame -- Default to ESP section
        State.activeSectionButton = GUI.ESPSectionButton
        UIManager.applyTheme(Config.settings.currentTheme) -- Apply initial theme and settings

        -- Loading animation completion
        local progressTween = Utils.playTween(GUI._LoadingProgressBarFG, Config.TWEEN_INFOS.TWEEN_PROGRESS, { Size = UDim2.new(1, 0, 1, 0) }, "LoadingProgress")
        if progressTween then progressTween.Completed:Wait() end -- Wait for progress bar to fill

        -- Fade out loading screen elements
        local fadeTextTween = Utils.playTween(GUI._LoadingText, Config.TWEEN_INFOS.TWEEN_LOADING_FADE, { TextTransparency = 1, TextStrokeTransparency = 1 }, "LoadingFadeOutText")
        local fadeBarBgTween = Utils.playTween(GUI._LoadingProgressBarBG, Config.TWEEN_INFOS.TWEEN_LOADING_FADE, { BackgroundTransparency = 1 }, "LoadingFadeOutBarBG")
        local fadeAuthorTween = Utils.playTween(GUI._LoadingAuthorLabel, Config.TWEEN_INFOS.TWEEN_LOADING_FADE, { TextTransparency = 1, TextStrokeTransparency = 1 }, "LoadingFadeOutAuthor")
        local fadeBgTween = Utils.playTween(GUI.LoadingBackgroundOverlay, Config.TWEEN_INFOS.TWEEN_LOADING_FADE, { BackgroundTransparency = 1 }, "LoadingFadeOutOverlay")
        if fadeBgTween then fadeBgTween.Completed:Wait() end -- Wait for background overlay to fade

        -- Clean up loading elements
        if GUI.LoadingFrame then GUI.LoadingFrame:Destroy(); GUI.LoadingFrame = nil end
        if GUI.LoadingBackgroundOverlay then GUI.LoadingBackgroundOverlay:Destroy(); GUI.LoadingBackgroundOverlay = nil end

        State.isLoading = false -- Mark loading as complete
        Utils.updateBlurEffectState() -- Update blur based on current settings (post-loading)
        
        -- Fade in main GUI elements (make them opaque)
        if GUI.MainFrame then GUI.MainFrame.BackgroundTransparency = 0 end
        if GUI.TitleFrame then GUI.TitleFrame.BackgroundTransparency = 0 end
        if GUI.SidebarFrame then GUI.SidebarFrame.BackgroundTransparency = 0 end
        if GUI.MainContentAreaFrame then GUI.MainContentAreaFrame.BackgroundTransparency = 0 end
        if GUI.MainFrame then Utils.playTween(GUI.MainFrame, Config.TWEEN_INFOS.TWEEN_GUI_OPEN, { BackgroundTransparency = 0 }, "OpenMainFrameAnim") end -- For smooth open
        if State.currentVisibleSectionFrame then State.currentVisibleSectionFrame.BackgroundTransparency = 0 end -- Ensure current section is visible

        -- Setup hover effects for all interactive buttons
        local buttonsToHover = {
            GUI.ESPSectionButton, GUI.AimSectionButton, GUI.ConfigSectionButton, GUI.DevSectionButton,
            GUI.PlayerESPToggle, GUI.ESPLinesToggle, GUI.ShowHealthToggle, GUI.SkeletonESPToggle,
            GUI.ESPRadarToggle, GUI.AimbotToggle, GUI.AimbotKeyLShiftButton, GUI.AimbotKeyRMBButton,
            GUI.TargetToggle, GUI.FOVToggle, GUI.CrosshairToggle, GUI.BlurToggle,
            GUI.DarkThemeButton, GUI.LightThemeButton, GUI.ToggleButton, GUI.CloseButton,
            GUI.RedColorButton, GUI.WhiteColorButton, GUI.GreenColorButton, GUI.BlueColorButton,
            GUI.YellowColorButton, GUI.PinkColorButton, GUI.FOVColorRedButton, GUI.FOVColorWhiteButton,
            GUI.FOVColorGreenButton, GUI.FOVColorBlueButton, GUI.FOVColorYellowButton,
            GUI.MinimizeCircleButton, GUI.OffScreenArrowsToggle, GUI.AimbotTargetLineToggle,
            GUI.DevFreecamToggle, GUI.DevBrightSkyToggle, GUI.AimbotWallCheckToggle, GUI.AimbotPredictionToggle
        }
        for _, btn in ipairs(buttonsToHover) do if btn then Utils.setupHover(btn) end end

        State.lastFullESPCheck = 0 -- Ensure ESP updates immediately
        AimbotManager.setupAimbotConnection() -- Setup aimbot based on current settings
        Services.StarterGui:SetCore("SendNotification",{Title="KONOSUBA_GUI Loaded!",Text="GUI v17.4 (Mod by HG) ready to go.",Duration=4})
    end

    -- Start the event connections and final setup
    HG.Events_ConnectAll_And_Finalize()

end) -- End of pcall


if not success then
    warn("Oh snap! Konosuba GUI had an issue: ", errorMessage)
    local eM = tostring(errorMessage)
    local lI = eM:match(":(%d+):") -- Try to extract line number
    if lI then eM = "Error on line " .. lI .. ": " .. eM:gsub("^.+:%d+:%s*", "") end -- Prettify error message

    if HG.Services and HG.Services.StarterGui then
        pcall(HG.Services.StarterGui.SetCore, HG.Services.StarterGui, "SendNotification", {Title="GUI Load Failed!",Text="Error: "..eM:sub(1,150),Duration=10})
    end
    
    -- Cleanup attempt on failure
    if HG.State and HG.State.backgroundBlurEffect and HG.State.backgroundBlurEffect.Parent then HG.State.backgroundBlurEffect.Enabled = false end
    if HG.State and HG.State.GUI_REFERENCES then
        local GUI = HG.State.GUI_REFERENCES
        -- Try to destroy all major GUI components if they were created
        if GUI.ScreenGui and GUI.ScreenGui.Parent then pcall(GUI.ScreenGui.Destroy, GUI.ScreenGui) end
        if GUI.FOVCircleFrame and GUI.FOVCircleFrame.Parent then pcall(GUI.FOVCircleFrame.Destroy, GUI.FOVCircleFrame)end
        if GUI.CrosshairFrameH and GUI.CrosshairFrameH.Parent then pcall(GUI.CrosshairFrameH.Destroy, GUI.CrosshairFrameH)end
        if GUI.CrosshairFrameV and GUI.CrosshairFrameV.Parent then pcall(GUI.CrosshairFrameV.Destroy, GUI.CrosshairFrameV)end
        if GUI.RadarFrame and GUI.RadarFrame.Parent then pcall(GUI.RadarFrame.Destroy, GUI.RadarFrame)end
        if GUI.AimbotTargetLineFrame and GUI.AimbotTargetLineFrame.Parent then pcall(GUI.AimbotTargetLineFrame.Destroy, GUI.AimbotTargetLineFrame) end
        if GUI.LoadingFrame and GUI.LoadingFrame.Parent then pcall(GUI.LoadingFrame.Destroy, GUI.LoadingFrame) end
        if GUI.LoadingBackgroundOverlay and GUI.LoadingBackgroundOverlay.Parent then pcall(GUI.LoadingBackgroundOverlay.Destroy, GUI.LoadingBackgroundOverlay) end
        if GUI.KonosubaWatermarkLabel and GUI.KonosubaWatermarkLabel.Parent then pcall(GUI.KonosubaWatermarkLabel.Destroy, GUI.KonosubaWatermarkLabel) end
        if GUI.FreecamWatermarkLabel and GUI.FreecamWatermarkLabel.Parent then pcall(GUI.FreecamWatermarkLabel.Destroy, GUI.FreecamWatermarkLabel) end
        if GUI.MinimizeCircleButton and GUI.MinimizeCircleButton.Parent then GUI.MinimizeCircleButton:Destroy() end
        if GUI.MainFrame and GUI.MainFrame.Parent then GUI.MainFrame:Destroy() end
    end
    if HG.State and HG.State.fovArrows then for player, arrow in pairs(HG.State.fovArrows) do if arrow and arrow.Parent then pcall(arrow.Destroy, arrow) end end; HG.State.fovArrows = {} end
    return -- Stop script execution
end

print("Konosuba GUI (v17.4, HG Mod) loaded up and ready to party!")

-- Cleanup when local player leaves
if HG.PlayerRefs.LocalPlayer then
    HG.PlayerRefs.LocalPlayer.Destroying:Connect(function()
        -- Ensure mouse behavior is reset if it was unlocked by the GUI
        if HG.State.isMouseUnlocked and HG.Services and HG.Services.UserInputService then
            pcall(function()
                HG.Services.UserInputService.MouseBehavior = Enum.MouseBehavior.Default
                HG.Services.UserInputService.MouseIconEnabled = true
            end)
        end
       
        if HG.Config.settings.devFreecamEnabled and HG.DevModeManager then HG.DevModeManager.setFreecamState(false) end
        if HG.Config.settings.devBrightSkyEnabled and HG.DevModeManager then HG.DevModeManager.setBrightSkyState(false) end

        
        if HG.State.backgroundBlurEffect and HG.State.backgroundBlurEffect.Parent then HG.State.backgroundBlurEffect.Enabled = false end
        
        -- Destroy GUI elements that might persist
        if HG.State.GUI_REFERENCES.MinimizeCircleButton and HG.State.GUI_REFERENCES.MinimizeCircleButton.Parent then HG.State.GUI_REFERENCES.MinimizeCircleButton:Destroy() end
        if HG.State.fovArrows then
            for player, arrow in pairs(HG.State.fovArrows) do if arrow and arrow.Parent then pcall(arrow.Destroy, arrow) end end
            HG.State.fovArrows = {}
        end
        if HG.State.GUI_REFERENCES.ScreenGui and HG.State.GUI_REFERENCES.ScreenGui.Parent then
            HG.State.GUI_REFERENCES.ScreenGui:Destroy()
            HG.State.GUI_REFERENCES.ScreenGui = nil
            HG.State.GUI_REFERENCES = {} -- Clear references
        end
        print("Konosuba GUI cleaned up after player destroyed.")
    end)
end
