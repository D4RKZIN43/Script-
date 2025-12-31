--==============================
-- HG UI + PVP SELECT PLAYER ESP (COM AVATAR)
--==============================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

--==============================
-- GUI BASE
--==============================
local gui = Instance.new("ScreenGui")
gui.Name = "HG_UI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

--==============================
-- BOTÃO HG (MAIS PARA CIMA)
--==============================
local launcher = Instance.new("TextButton", gui)
launcher.Size = UDim2.new(0,60,0,60)
launcher.Position = UDim2.new(0,15,0.25,0) -- MAIS ALTO
launcher.Text = "HG"
launcher.Font = Enum.Font.GothamBlack
launcher.TextSize = 22
launcher.TextColor3 = Color3.fromRGB(255,215,0)
launcher.BackgroundColor3 = Color3.fromRGB(15,15,15)
launcher.AutoButtonColor = false
Instance.new("UICorner", launcher).CornerRadius = UDim.new(0,14)

local launcherStroke = Instance.new("UIStroke", launcher)
launcherStroke.Thickness = 3
launcherStroke.Color = Color3.fromRGB(255,170,0)

task.spawn(function()
	while true do
		for i=0.3,1,0.05 do launcherStroke.Transparency=i task.wait(0.03) end
		for i=1,0.3,-0.05 do launcherStroke.Transparency=i task.wait(0.03) end
	end
end)

--==============================
-- MAIN UI
--==============================
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0,420,0,260)
main.Position = UDim2.new(0.5,-210,0.5,-130)
main.Visible = false
main.BackgroundColor3 = Color3.fromRGB(10,10,10)
main.BorderSizePixel = 0
Instance.new("UICorner", main).CornerRadius = UDim.new(0,16)

local border = Instance.new("UIStroke", main)
border.Color = Color3.fromRGB(255,170,0)
border.Thickness = 2

--==============================
-- TOP BAR
--==============================
local top = Instance.new("Frame", main)
top.Size = UDim2.new(1,0,0,35)
top.BackgroundTransparency = 1

local toggle = Instance.new("TextButton", top)
toggle.Size = UDim2.new(0,30,0,22)
toggle.Position = UDim2.new(0,8,0,6)
toggle.Text = "<"
toggle.Font = Enum.Font.GothamBold
toggle.TextSize = 16
toggle.TextColor3 = Color3.fromRGB(255,170,0)
toggle.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", toggle)

local close = Instance.new("TextButton", top)
close.Size = UDim2.new(0,40,0,22)
close.Position = UDim2.new(1,-45,0,6)
close.Text = "〕〔"
close.Font = Enum.Font.GothamBold
close.TextSize = 14
close.TextColor3 = Color3.fromRGB(255,170,0)
close.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", close)

close.MouseButton1Click:Connect(function()
	main.Visible = false
end)

--==============================
-- SIDEBAR
--==============================
local side = Instance.new("Frame", main)
side.Size = UDim2.new(0,120,1,-40)
side.Position = UDim2.new(0,0,0,35)
side.BackgroundColor3 = Color3.fromRGB(15,15,15)
Instance.new("UICorner", side).CornerRadius = UDim.new(0,12)

local function tab(name,y)
	local b = Instance.new("TextButton", side)
	b.Size = UDim2.new(1,-10,0,35)
	b.Position = UDim2.new(0,5,0,y)
	b.Text = name
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.TextColor3 = Color3.fromRGB(255,170,0)
	b.BackgroundColor3 = Color3.fromRGB(20,20,20)
	Instance.new("UICorner", b)
	return b
end

local spamTab = tab("SPAM",10)
local pvpTab  = tab("PVP",55)
local auxTab  = tab("AUX / BOOST",100)

--==============================
-- CONTENT
--==============================
local content = Instance.new("ScrollingFrame", main)
content.Position = UDim2.new(0,125,0,40)
content.Size = UDim2.new(1,-130,1,-45)
content.CanvasSize = UDim2.new(0,0,0,600)
content.ScrollBarThickness = 6
content.BackgroundColor3 = Color3.fromRGB(12,12,12)
content.BorderSizePixel = 0
Instance.new("UICorner", content)

--==============================
-- TOGGLE SIDEBAR
--==============================
local open = true
toggle.MouseButton1Click:Connect(function()
	open = not open
	if open then
		toggle.Text = "<"
		side.Visible = true
		content.Position = UDim2.new(0,125,0,40)
		content.Size = UDim2.new(1,-130,1,-45)
	else
		toggle.Text = ">"
		side.Visible = false
		content.Position = UDim2.new(0,10,0,40)
		content.Size = UDim2.new(1,-20,1,-45)
	end
end)

launcher.MouseButton1Click:Connect(function()
	main.Visible = true
end)

--==============================
-- LIMPAR CONTENT
--==============================
local function clearContent()
	for _,v in pairs(content:GetChildren()) do
		if not v:IsA("UICorner") then
			v:Destroy()
		end
	end
end

--==============================
-- ESP CONTROL (1 PLAYER)
--==============================
local selectedPlayer = nil
local espObjects = {}
local espConn = nil

local function clearESP()
	for _,obj in pairs(espObjects) do
		if obj and obj.Parent then obj:Destroy() end
	end
	espObjects = {}
	if espConn then espConn:Disconnect() espConn=nil end
	selectedPlayer = nil
end

local function createESP(target)
	clearESP()
	selectedPlayer = target
	if not target.Character then return end

	local char = target.Character
	local hrp = char:WaitForChild("HumanoidRootPart")

	local hl = Instance.new("Highlight", char)
	hl.FillTransparency = 1
	hl.OutlineColor = Color3.fromRGB(255,170,0)
	table.insert(espObjects, hl)

	local bill = Instance.new("BillboardGui", char)
	bill.Size = UDim2.new(0,200,0,50)
	bill.StudsOffset = Vector3.new(0,4,0)
	bill.AlwaysOnTop = true
	table.insert(espObjects, bill)

	local name = Instance.new("TextLabel", bill)
	name.Size = UDim2.new(1,0,0.5,0)
	name.Text = "@"..target.Name
	name.Font = Enum.Font.GothamBold
	name.TextSize = 14
	name.TextColor3 = Color3.fromRGB(255,170,0)
	name.BackgroundTransparency = 1

	local dist = Instance.new("TextLabel", bill)
	dist.Position = UDim2.new(0,0,0.5,0)
	dist.Size = UDim2.new(1,0,0.5,0)
	dist.Font = Enum.Font.Gotham
	dist.TextSize = 13
	dist.TextColor3 = Color3.fromRGB(255,170,0)
	dist.BackgroundTransparency = 1

	espConn = RunService.RenderStepped:Connect(function()
		if selectedPlayer ~= target or not hrp.Parent then
			clearESP()
			return
		end
		dist.Text = math.floor((camera.CFrame.Position - hrp.Position).Magnitude).."m"
	end)
end

--==============================
-- PVP TAB (COM AVATAR)
--==============================
pvpTab.MouseButton1Click:Connect(function()
	clearContent()

	local select = Instance.new("TextButton", content)
	select.Size = UDim2.new(1,-20,0,40)
	select.Position = UDim2.new(0,10,0,10)
	select.Text = "SELECT / PLAYER ˅"
	select.Font = Enum.Font.GothamBold
	select.TextSize = 14
	select.TextColor3 = Color3.fromRGB(255,170,0)
	select.BackgroundColor3 = Color3.fromRGB(20,20,20)
	Instance.new("UICorner", select)

	local list = Instance.new("Frame", content)
	list.Size = UDim2.new(1,-20,0,200)
	list.Position = UDim2.new(0,10,0,60)
	list.Visible = false
	list.BackgroundColor3 = Color3.fromRGB(15,15,15)
	Instance.new("UICorner", list)
	Instance.new("UIStroke", list).Color = Color3.fromRGB(255,170,0)

	local scroll = Instance.new("ScrollingFrame", list)
	scroll.Size = UDim2.new(1,0,1,-35)
	scroll.ScrollBarThickness = 6
	scroll.BackgroundTransparency = 1

	local cancel = Instance.new("TextButton", list)
	cancel.Size = UDim2.new(1,0,0,35)
	cancel.Position = UDim2.new(0,0,1,-35)
	cancel.Text = "CANCELAR"
	cancel.Font = Enum.Font.GothamBold
	cancel.TextSize = 14
	cancel.TextColor3 = Color3.fromRGB(255,80,80)
	cancel.BackgroundTransparency = 1

	local function refresh()
		scroll:ClearAllChildren()
		local y = 0

		for _,plr in pairs(Players:GetPlayers()) do
			if plr ~= player then
				local b = Instance.new("TextButton", scroll)
				b.Size = UDim2.new(1,-10,0,40)
				b.Position = UDim2.new(0,5,0,y)
				b.Text = ""
				b.BackgroundTransparency = 1

				local avatar = Instance.new("ImageLabel", b)
				avatar.Size = UDim2.new(0,32,0,32)
				avatar.Position = UDim2.new(0,4,0,4)
				avatar.BackgroundTransparency = 1
				Instance.new("UICorner", avatar).CornerRadius = UDim.new(1,0)

				local thumb = Players:GetUserThumbnailAsync(
					plr.UserId,
					Enum.ThumbnailType.HeadShot,
					Enum.ThumbnailSize.Size100x100
				)
				avatar.Image = thumb

				local txt = Instance.new("TextLabel", b)
				txt.Position = UDim2.new(0,44,0,0)
				txt.Size = UDim2.new(1,-44,1,0)
				txt.Text = "@"..plr.Name
				txt.Font = Enum.Font.GothamBold
				txt.TextSize = 14
				txt.TextColor3 = Color3.fromRGB(255,170,0)
				txt.TextXAlignment = Enum.TextXAlignment.Left
				txt.BackgroundTransparency = 1

				b.MouseButton1Click:Connect(function()
					list.Visible = false
					createESP(plr)
				end)

				y += 45
			end
		end
		scroll.CanvasSize = UDim2.new(0,0,0,y)
	end

	select.MouseButton1Click:Connect(function()
		list.Visible = not list.Visible
		refresh()
	end)

	cancel.MouseButton1Click:Connect(function()
		list.Visible = false
	end)
end)
