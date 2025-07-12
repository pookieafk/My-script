-- Kill Aura GUI by ChatGPT (for your own game)
-- Place in a LocalScript (e.g., StarterPlayerScripts)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()

--== SETTINGS ==--
local killAuraMode = "map" -- "map" or "radius"
local killRadius = 30
local auraEnabled = false

--== GUI CREATION ==--
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "KillAuraGUI"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 80)
frame.Position = UDim2.new(0.4, 0, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0.4, 0)
title.BackgroundTransparency = 1
title.Text = "☠ Kill Aura GUI ☠"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(0.9, 0, 0.4, 0)
button.Position = UDim2.new(0.05, 0, 0.5, 0)
button.Text = "Activate Kill Aura"
button.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.Font = Enum.Font.Gotham
button.TextSize = 16

--== KILL FUNCTION ==--
function killNPC(npc)
	if npc and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
		local hum = npc.Humanoid
		if hum.Health > 0 then
			hum.Health = 0
			for _, child in pairs(npc:GetChildren()) do
				if child:IsA("Script") and child.Name:lower():find("regen") then
					child:Destroy()
				end
			end
		end
	end
end

--== AURA LOOP ==--
RunService.RenderStepped:Connect(function()
	if not auraEnabled then return end
	Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	for _, npc in pairs(workspace:GetDescendants()) do
		if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
			if npc ~= Character then
				local shouldKill = false
				if killAuraMode == "map" then
					shouldKill = true
				elseif killAuraMode == "radius" then
					local dist = (Character.HumanoidRootPart.Position - npc.HumanoidRootPart.Position).Magnitude
					if dist <= killRadius then
						shouldKill = true
					end
				end
				if shouldKill then
					killNPC(npc)
				end
			end
		end
	end
end)

--== BUTTON TOGGLE ==--
button.MouseButton1Click:Connect(function()
	auraEnabled = not auraEnabled
	if auraEnabled then
		button.Text = "Kill Aura: ON"
		button.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
	else
		button.Text = "Kill Aura: OFF"
		button.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
	end
end)
