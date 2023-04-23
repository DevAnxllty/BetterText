# BetterText
*Better Text is a simple way to make text notifications*

First we need to make the handler, insert this local script into StarterPlayer > StarterPlayerScripts
```
--[[
	______      _   _              _____         _   
	| ___ \    | | | |            |_   _|       | |  
	| |_/ / ___| |_| |_ ___ _ __    | | _____  _| |_ 
	| ___ \/ _ \ __| __/ _ \ '__|   | |/ _ \ \/ / __|
	| |_/ /  __/ |_| ||  __/ |      | |  __/>  <| |_ 
	\____/ \___|\__|\__\___|_|      \_/\___/_/\_\\__|
	
	@DevAnxllty 4/22/23
]]

--Services
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local SoundService = game:GetService("SoundService")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")

--Player
local player = Players.LocalPlayer

local UI = player.PlayerGui:WaitForChild("BetterText")

--Remotes
local notifRemote = ReplicatedStorage.BetterTextRemote

--Sounds
local errorSound = SoundService.error
local successSound = SoundService.error2

--Colors
local successColor = Color3.new(0.101961, 1, 0.745098)
local errorColor = Color3.new(1, 0.0431373, 0.266667)

--Types
local types = {
	["Success"] = {successColor, successSound},
	["Error"] = {errorColor, errorSound}
}
--["Name"] = {Color, Sound(optional)}


--Notification Event
notifRemote.OnClientEvent:Connect(function(text, textType)
	if not UI then
		warn("BetterText: UI Not Found")
	else
		local function createText(text, textType)
			local template = UI.Template
			local newText = template:Clone()
			newText.Parent = UI.Main
			newText.Text = text
			if types[textType] then
				local foundType = types[textType]
				newText.TextColor3 = foundType[1]

				--Sound
				if foundType[2] then
					foundType[2]:Play()
				end

				--Tweening
				local textIn = TweenService:Create(newText, TweenInfo.new(0.25, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),{TextTransparency = 0})
				local textOut = TweenService:Create(newText, TweenInfo.new(0.25, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),{TextTransparency = 1})

				local strokeTextIn = TweenService:Create(newText, TweenInfo.new(0.5, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),{TextStrokeTransparency = 0.9})
				local strokeTextOut = TweenService:Create(newText, TweenInfo.new(0.5, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),{TextStrokeTransparency = 1})

				--In
				textIn:Play()
				strokeTextIn:Play()

				textIn.Completed:Wait()

				task.wait(2)

				--Out
				textOut:Play()
				strokeTextOut:Play()

				strokeTextIn.Completed:Wait()

				--Destroy
				newText:Destroy()
			else
				warn("BetterText: Type Not Found")
			end
		end
		coroutine.wrap(createText)(text, textType)
	end
end)
```
