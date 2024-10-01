-- กำหนดค่าขอบเขต
local players = game:GetService("Players")
local localPlayer = players.LocalPlayer
local espEnabled = true -- ตั้งค่าการเปิด/ปิด ESP

-- ฟังก์ชันสำหรับสร้าง ESP
local function createESP(player)
    local highlight = Instance.new("Highlight")
    highlight.Adornee = player.Character
    highlight.FillColor = Color3.new(1, 0, 0) -- สีของ ESP (สีแดง)
    highlight.OutlineColor = Color3.new(0, 0, 0) -- สีขอบของ ESP (สีดำ)
    highlight.FillTransparency = 0.5 -- ความโปร่งใส
    highlight.Parent = player.Character
end

-- ฟังก์ชันสำหรับลบ ESP
local function removeESP(player)
    if player.Character then
        local highlight = player.Character:FindFirstChildOfClass("Highlight")
        if highlight then
            highlight:Destroy()
        end
    end
end

-- ตรวจสอบผู้เล่นที่เข้าร่วมเกม
for _, player in pairs(players:GetPlayers()) do
    if player ~= localPlayer then
        createESP(player)
    end
end

-- สร้าง ESP ใหม่เมื่อผู้เล่นเข้าร่วม
players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        createESP(player)
    end)
end)

-- ลบ ESP เมื่อผู้เล่นออกจากเกม
players.PlayerRemoving:Connect(function(player)
    removeESP(player)
end)

-- ลบ ESP เมื่อผู้เล่นเสียชีวิต
localPlayer.CharacterAdded:Connect(function(character)
    removeESP(localPlayer)
    character:WaitForChild("Humanoid").Died:Connect(function()
        removeESP(localPlayer)
    end)
end)

-- สลับการเปิด/ปิด ESP
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.E then -- กด 'E' เพื่อเปิด/ปิด ESP
        espEnabled = not espEnabled
        for _, player in pairs(players:GetPlayers()) do
            if player ~= localPlayer then
                if espEnabled then
                    createESP(player)
                else
                    removeESP(player)
                end
            end
        end
    end
end)
