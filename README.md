-- LocalScript (StarterPlayerScripts o CoreGui mediante entorno de desarrollo UI)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Configuración de estados globales del panel
local Settings = {
    Combat = {
        TriggerbotPC = false,
        TriggerbotSolara = false,
        Delay = 0.05, -- Retraso configurable en segundos
        TeamCheck = true
    }
}

-- 1. Creación de la ScreenGui principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CombatInterfaceUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Protección básica de parentesco para pruebas en Studio o cliente
local success, coreGui = pcall(function()
    return game:GetService("CoreGui")
end)
ScreenGui.Parent = (success and coreGui) or LocalPlayer:WaitForChild("PlayerGui")

-- 2. Contenedor Principal (MainFrame)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 480, 0, 320)
MainFrame.Position = UDim2.new(0.5, -240, 0.5, -160)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 22, 28)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 8)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(45, 50, 62)
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- 3. Barra Superior (TopBar) con función de arrastre (Draggable)
local TopBar = Instance.new("Frame")
TopBar.Name = "TopBar"
TopBar.Size = UDim2.new(1, 0, 0, 38)
TopBar.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
TopBar.BorderSizePixel = 0
TopBar.Parent = MainFrame

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 8)
TopCorner.Parent = TopBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "Title"
TitleLabel.Size = UDim2.new(1, -20, 1, 0)
TitleLabel.Position = UDim2.new(0, 14, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "SYSTEM HUD // v2.4"
TitleLabel.TextColor3 = Color3.fromRGB(235, 240, 245)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 14
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TopBar

-- Lógica para arrastrar la ventana
local dragging, dragInput, dragStart, startPos
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- 4. Barra Lateral de Categorías (Sidebar)
local Sidebar = Instance.new("Frame")
Sidebar.Name = "Sidebar"
Sidebar.Size = UDim2.new(0, 130, 1, -38)
Sidebar.Position = UDim2.new(0, 0, 0, 38)
Sidebar.BackgroundColor3 = Color3.fromRGB(23, 25, 32)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

local SidebarList = Instance.new("UIListLayout")
SidebarList.Padding = UDim.new(0, 6)
SidebarList.HorizontalAlignment = Enum.HorizontalAlignment.Center
SidebarList.Parent = Sidebar

local SidebarPadding = Instance.new("UIPadding")
SidebarPadding.PaddingTop = UDim.new(0, 10)
SidebarPadding.Parent = Sidebar

-- Botón de Categoría: Combat
local CombatTabBtn = Instance.new("TextButton")
CombatTabBtn.Name = "CombatTab"
CombatTabBtn.Size = UDim2.new(0.88, 0, 0, 34)
CombatTabBtn.BackgroundColor3 = Color3.fromRGB(56, 114, 240)
CombatTabBtn.Text = "Combat"
CombatTabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CombatTabBtn.Font = Enum.Font.GothamSemibold
CombatTabBtn.TextSize = 13
CombatTabBtn.AutoButtonColor = false
CombatTabBtn.Parent = Sidebar

local TabCorner = Instance.new("UICorner")
TabCorner.CornerRadius = UDim.new(0, 6)
TabCorner.Parent = CombatTabBtn

-- 5. Contenedor de Contenido (CombatPage)
local ContentContainer = Instance.new("Frame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Size = UDim2.new(1, -130, 1, -38)
ContentContainer.Position = UDim2.new(0, 130, 0, 38)
ContentContainer.BackgroundTransparency = 1
ContentContainer.Parent = MainFrame

local CombatPage = Instance.new("ScrollingFrame")
CombatPage.Name = "CombatPage"
CombatPage.Size = UDim2.new(1, 0, 1, 0)
CombatPage.BackgroundTransparency = 1
CombatPage.BorderSizePixel = 0
CombatPage.ScrollBarThickness = 3
CombatPage.ScrollBarImageColor3 = Color3.fromRGB(70, 78, 95)
CombatPage.Parent = ContentContainer

local PageLayout = Instance.new("UIListLayout")
PageLayout.Padding = UDim.new(0, 10)
PageLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
PageLayout.Parent = CombatPage

local PagePadding = Instance.new("UIPadding")
PagePadding.PaddingTop = UDim.new(0, 12)
PagePadding.PaddingLeft = UDim.new(0, 12)
PagePadding.PaddingRight = UDim.new(0, 12)
PagePadding.Parent = CombatPage

-- Función constructora de Toggles (Interruptores)
local function CreateToggle(parent, title, description, callback)
    local ToggleFrame = Instance.new("Frame")
    ToggleFrame.Size = UDim2.new(1, 0, 0, 54)
    ToggleFrame.BackgroundColor3 = Color3.fromRGB(28, 31, 40)
    ToggleFrame.BorderSizePixel = 0
    ToggleFrame.Parent = parent

    local FrameCorner = Instance.new("UICorner")
    FrameCorner.CornerRadius = UDim.new(0, 6)
    FrameCorner.Parent = ToggleFrame

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -70, 0, 22)
    Title.Position = UDim2.new(0, 12, 0, 6)
    Title.BackgroundTransparency = 1
    Title.Text = title
    Title.TextColor3 = Color3.fromRGB(240, 244, 250)
    Title.Font = Enum.Font.GothamSemibold
    Title.TextSize = 13
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = ToggleFrame

    local Desc = Instance.new("TextLabel")
    Desc.Size = UDim2.new(1, -70, 0, 18)
    Desc.Position = UDim2.new(0, 12, 0, 28)
    Desc.BackgroundTransparency = 1
    Desc.Text = description
    Desc.TextColor3 = Color3.fromRGB(150, 160, 175)
    Desc.Font = Enum.Font.Gotham
    Desc.TextSize = 11
    Desc.TextXAlignment = Enum.TextXAlignment.Left
    Desc.Parent = ToggleFrame

    -- Switch Visual
    local SwitchBg = Instance.new("TextButton")
    SwitchBg.Size = UDim2.new(0, 44, 0, 22)
    SwitchBg.Position = UDim2.new(1, -56, 0.5, -11)
    SwitchBg.BackgroundColor3 = Color3.fromRGB(45, 50, 62)
    SwitchBg.Text = ""
    SwitchBg.AutoButtonColor = false
    SwitchBg.Parent = ToggleFrame

    local SwitchCorner = Instance.new("UICorner")
    SwitchCorner.CornerRadius = UDim.new(1, 0)
    SwitchCorner.Parent = SwitchBg

    local Circle = Instance.new("Frame")
    Circle.Size = UDim2.new(0, 16, 0, 16)
    Circle.Position = UDim2.new(0, 3, 0.5, -8)
    Circle.BackgroundColor3 = Color3.fromRGB(210, 215, 225)
    Circle.BorderSizePixel = 0
    Circle.Parent = SwitchBg

    local CircleCorner = Instance.new("UICorner")
    CircleCorner.CornerRadius = UDim.new(1, 0)
    CircleCorner.Parent = Circle

    local enabled = false
    SwitchBg.MouseButton1Click:Connect(function()
        enabled = not enabled
        local targetColor = enabled and Color3.fromRGB(56, 114, 240) or Color3.fromRGB(45, 50, 62)
        local targetPos = enabled and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)

        TweenService:Create(SwitchBg, TweenInfo.new(0.18), {BackgroundColor3 = targetColor}):Play()
        TweenService:Create(Circle, TweenInfo.new(0.18), {Position = targetPos}):Play()

        if callback then
            callback(enabled)
        end
    end)
end

-- 6. Implementación de las Opciones en la categoría Combat

-- Opción 1: Triggerbot PC
CreateToggle(
    CombatPage,
    "Triggerbot (PC)",
    "Detección estándar mediante Mouse.Target y raycast en cliente.",
    function(state)
        Settings.Combat.TriggerbotPC = state
        if state then
            print("[Combat] Triggerbot PC: ACTIVADO")
        else
            print("[Combat] Triggerbot PC: DESACTIVADO")
        end
    end
)

-- Opción 2: Triggerbot Solara
CreateToggle(
    CombatPage,
    "Triggerbot (Solara)",
    "Modo compatible con entornos externos mediante verificación de Workspace.",
    function(state)
        Settings.Combat.TriggerbotSolara = state
        if state then
            print("[Combat] Triggerbot Solara: ACTIVADO")
        else
            print("[Combat] Triggerbot Solara: DESACTIVADO")
        end
    end
)

-- Opción 3: Team Check (Filtro de equipo)
CreateToggle(
    CombatPage,
    "Team Check",
    "Ignora objetivos que pertenezcan al mismo equipo.",
    function(state)
        Settings.Combat.TeamCheck = state
    end
)

-- 7. Lógica de evaluación de objetivo (Validación de estado y Raycast)
local function IsValidTarget(targetPart)
    if not targetPart or not targetPart.Parent then return false end
    local character = targetPart:FindFirstAncestorOfClass("Model")
    if not character then return false end

    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid or humanoid.Health <= 0 then return false end

    local targetPlayer = Players:GetPlayerFromCharacter(character)
    if targetPlayer and targetPlayer ~= LocalPlayer then
        if Settings.Combat.TeamCheck and targetPlayer.Team == LocalPlayer.Team then
            return false
        end
        return true
    end
    return false
end

-- Bucle de monitoreo de estado en RenderStepped
local lastAction = 0
RunService.RenderStepped:Connect(function()
    if not (Settings.Combat.TriggerbotPC or Settings.Combat.TriggerbotSolara) then
        return
    end

    if tick() - lastAction < Settings.Combat.Delay then
        return
    end

    -- Detección por modo PC (Mouse.Target directo)
    if Settings.Combat.TriggerbotPC then
        local target = Mouse.Target
        if IsValidTarget(target) then
            lastAction = tick()
            -- Disparador de evento interno de interfaz/herramienta equipada
            local tool = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
            if tool then
                tool:Activate()
            end
        end
    end

    -- Detección por modo Solara (Raycast desde la cámara hacia el centro/cursor)
    if Settings.Combat.TriggerbotSolara then
        local camera = workspace.CurrentCamera
        if camera then
            local mouseLocation = UserInputService:GetMouseLocation()
            local unitRay = camera:ViewportPointToRay(mouseLocation.X, mouseLocation.Y)
            
            local rayParams = RaycastParams.new()
            rayParams.FilterDescendantsInstances = {LocalPlayer.Character}
            rayParams.FilterType = Enum.RaycastFilterType.Exclude

            local result = workspace:Raycast(unitRay.Origin, unitRay.Direction * 500, rayParams)
            if result and IsValidTarget(result.Instance) then
                lastAction = tick()
                local tool = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
                if tool then
                    tool:Activate()
                end
            end
        end
    end
end)

-- Tecla rápida para ocultar/mostrar la interfaz (RightShift)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        MainFrame.Visible = not MainFrame.Visible
    end
end)
