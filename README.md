-- ARTIC COMMANDER HUB | STABLE FLY + SPEED + JUMP + NOCLIP + ESP + FEEDBACK

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TeleportService = game:GetService("TeleportService")
local Camera = workspace.CurrentCamera
local player = Players.LocalPlayer

-- CHARACTER
local char = player.Character or player.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")
local root = char:WaitForChild("HumanoidRootPart")
local animate = char:WaitForChild("Animate")

player.CharacterAdded:Connect(function(c)
	char = c
	hum = c:WaitForChild("Humanoid")
	root = c:WaitForChild("HumanoidRootPart")
	animate = c:WaitForChild("Animate")
end)

-- ================= GUI =================
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.ResetOnSpawn = false

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0,360,0,260)
main.Position = UDim2.new(0.5,-180,0.5,-130)
main.BackgroundColor3 = Color3.fromRGB(240,240,240)
main.Active = true
main.Draggable = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0,22)

local top = Instance.new("Frame", main)
top.Size = UDim2.new(1,0,0,44)
top.BackgroundColor3 = Color3.fromRGB(90,140,255)
Instance.new("UICorner", top).CornerRadius = UDim.new(0,22)

local title = Instance.new("TextLabel", top)
title.Size = UDim2.new(1,-60,1,0)
title.Position = UDim2.new(0,14,0,0)
title.BackgroundTransparency = 1
title.Text = "❄ ARTIC COMMANDER"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.new(1,1,1)
title.TextXAlignment = Enum.TextXAlignment.Left

local minimize = Instance.new("TextButton", top)
minimize.Size = UDim2.new(0,34,0,34)
minimize.Position = UDim2.new(1,-40,0.5,-17)
minimize.Text = "-"
minimize.Font = Enum.Font.GothamBold
minimize.TextSize = 22
minimize.BackgroundColor3 = Color3.fromRGB(255,120,120)
minimize.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", minimize).CornerRadius = UDim.new(0,16)

local mini = Instance.new("ImageButton", gui)
mini.Size = UDim2.new(0,56,0,56)
mini.Position = UDim2.new(1,-72,0.5,-28)
mini.BackgroundColor3 = Color3.fromRGB(90,140,255)
mini.Image = "rbxassetid://84639845026314"
mini.Visible = false
mini.Active = true
mini.Draggable = true
Instance.new("UICorner", mini).CornerRadius = UDim.new(0,18)

local cmdBox = Instance.new("TextBox", main)
cmdBox.Size = UDim2.new(0.9,0,0,44)
cmdBox.Position = UDim2.new(0.05,0,0.3,0)
cmdBox.PlaceholderText = "Digite o comando..."
cmdBox.Font = Enum.Font.Gotham
cmdBox.TextSize = 16
cmdBox.BackgroundColor3 = Color3.fromRGB(220,220,220)
Instance.new("UICorner", cmdBox).CornerRadius = UDim.new(0,14)

local cmdsLabel = Instance.new("TextLabel", main)
cmdsLabel.Size = UDim2.new(0.9,0,0,110)
cmdsLabel.Position = UDim2.new(0.05,0,0.55,0)
cmdsLabel.BackgroundTransparency = 1
cmdsLabel.TextWrapped = true
cmdsLabel.Font = Enum.Font.Gotham
cmdsLabel.TextSize = 14
cmdsLabel.TextColor3 = Color3.fromRGB(40,40,40)
cmdsLabel.Text =
"Comandos:\n" ..
"fly / unfly\n" ..
"speed 20-500\n" ..
"jump 50-200\n" ..
"noclip / clip\n" ..
"esp / unesp\n" ..
"rejoin"

-- ================= FEEDBACK =================
local function notify(text, color)
	local label = Instance.new("TextLabel", gui)
	label.Size = UDim2.new(0,320,0,40)
	label.Position = UDim2.new(0.5,-160,0.15,0)
	label.BackgroundColor3 = color or Color3.fromRGB(90,140,255)
	label.TextColor3 = Color3.new(1,1,1)
	label.Text = text
	label.Font = Enum.Font.GothamBold
	label.TextSize = 16
	label.ZIndex = 50
	Instance.new("UICorner", label).CornerRadius = UDim.new(0,14)

	task.delay(2, function()
		label:Destroy()
	end)
end

-- ================= FLY =================
local flying = false
local flySpeed = 70
local bv, bg, flyConn

local function stopFly()
	flying = false
	if animate then animate.Disabled = false end
	if flyConn then flyConn:Disconnect() end
	if bv then bv:Destroy() end
	if bg then bg:Destroy() end
	hum.PlatformStand = false
end

local function startFly()
	if flying then return end
	stopFly()
	flying = true
	if animate then animate.Disabled = true end
	hum.PlatformStand = true

	bv = Instance.new("BodyVelocity", root)
	bv.MaxForce = Vector3.new(1e9,1e9,1e9)

	bg = Instance.new("BodyGyro", root)
	bg.MaxTorque = Vector3.new(1e9,1e9,1e9)
	bg.P = 9000

	flyConn = RunService.RenderStepped:Connect(function()
		local move = hum.MoveDirection
		local camCF = Camera.CFrame
		bg.CFrame = CFrame.new(root.Position, root.Position + camCF.LookVector)
		bv.Velocity = camCF.LookVector * move.Magnitude * flySpeed
	end)
end

-- ================= SPEED =================
local function setSpeed(v)
	v = tonumber(v)
	if v then
		hum.WalkSpeed = math.clamp(v,20,500)
		notify("⚡ Speed: "..hum.WalkSpeed)
	end
end

-- ================= JUMP =================
local function setJump(v)
	v = tonumber(v)
	if v then
		hum.JumpPower = math.clamp(v,50,200)
		notify("🦘 Jump: "..hum.JumpPower)
	end
end

-- ================= NOCLIP =================
local noclip = false
local noclipConn

local function enableNoclip()
	noclip = true
	if noclipConn then noclipConn:Disconnect() end
	noclipConn = RunService.Stepped:Connect(function()
		for _,p in pairs(char:GetDescendants()) do
			if p:IsA("BasePart") then
				p.CanCollide = false
			end
		end
	end)
	notify("👻 Noclip ATIVADO")
end

local function disableNoclip()
	noclip = false
	if noclipConn then noclipConn:Disconnect() end
	for _,p in pairs(char:GetDescendants()) do
		if p:IsA("BasePart") then
			p.CanCollide = true
		end
	end
	notify("🚫 Noclip DESATIVADO")
end

-- ================= ESP =================
local ESP_ENABLED = false
local ESP = {}

local function newDraw(t, props)
	local d = Drawing.new(t)
	for i,v in pairs(props) do d[i] = v end
	return d
end

local function createESP(p)
	if p == player then return end
	ESP[p] = {
		Box = newDraw("Square",{Thickness=2,Filled=false}),
		Name = newDraw("Text",{Size=14,Center=true,Outline=true}),
		Tracer = newDraw("Line",{Thickness=1})
	}
end

local function removeESP(p)
	if ESP[p] then
		for _,o in pairs(ESP[p]) do o:Remove() end
		ESP[p] = nil
	end
end

RunService.RenderStepped:Connect(function()
	if not ESP_ENABLED then
		for _,obj in pairs(ESP) do
			for _,o in pairs(obj) do o.Visible = false end
		end
		return
	end

	for p,obj in pairs(ESP) do
		local c = p.Character
		local hrp = c and c:FindFirstChild("HumanoidRootPart")
		local hum = c and c:FindFirstChild("Humanoid")

		if hrp and hum and hum.Health > 0 then
			local pos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
			if onScreen then
				local dist = (Camera.CFrame.Position - hrp.Position).Magnitude
				local size = Vector2.new(35,50)

				obj.Box.Size = size
				obj.Box.Position = Vector2.new(pos.X-size.X/2,pos.Y-size.Y/2)
				obj.Box.Visible = true

				obj.Name.Text = p.Name.." ["..math.floor(dist).."]"
				obj.Name.Position = Vector2.new(pos.X,pos.Y-size.Y/2-16)
				obj.Name.Visible = true

				obj.Tracer.From = Vector2.new(Camera.ViewportSize.X/2,Camera.ViewportSize.Y)
				obj.Tracer.To = Vector2.new(pos.X,pos.Y+size.Y/2)
				obj.Tracer.Visible = true
			else
				for _,o in pairs(obj) do o.Visible = false end
			end
		else
			for _,o in pairs(obj) do o.Visible = false end
		end
	end
end)

for _,p in pairs(Players:GetPlayers()) do createESP(p) end
Players.PlayerAdded:Connect(createESP)
Players.PlayerRemoving:Connect(removeESP)

-- ================= COMMANDS =================
local commands = {
	fly = function() startFly() notify("✈ Fly ON") end,
	unfly = function() stopFly() notify("🛑 Fly OFF") end,
	speed = setSpeed,
	jump = setJump,
	noclip = enableNoclip,
	clip = disableNoclip,

	esp = function()
		ESP_ENABLED = true
		notify("👁 ESP ATIVADO")
	end,

	unesp = function()
		ESP_ENABLED = false
		notify("🚫 ESP DESATIVADO")
	end,

	rejoin = function()
		notify("🔄 Reentrando...")
		TeleportService:Teleport(game.PlaceId, player)
	end
}

local function runCommand(text)
	local args = {}
	for w in text:lower():gmatch("%S+") do table.insert(args,w) end
	local cmd = args[1]
	table.remove(args,1)

	if commands[cmd] then
		commands[cmd](args[1])
	else
		notify("❌ Comando inválido")
	end
end

cmdBox.FocusLost:Connect(function(enter)
	if enter then
		runCommand(cmdBox.Text)
		cmdBox.Text = ""
	end
end)

minimize.MouseButton1Click:Connect(function()
	main.Visible = false
	mini.Visible = true
end)

mini.MouseButton1Click:Connect(function()
	mini.Visible = false
	main.Visible = true
end)
