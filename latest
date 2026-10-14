local Rayfield = loadstring(game:HttpGet("https://sirius.menu/gen2"))()

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local localPlayer = Players.LocalPlayer
local backpack = localPlayer.Backpack
local playerGui = localPlayer.PlayerGui :: PlayerGui
local localCharacter = localPlayer.Character

local remoteEvents = ReplicatedStorage:WaitForChild("Events")

local configs = {
    validBlessings = {
        ["An actual Mole"] = "An actual Mole",
        ["Aspiration"] = "Aspiration",
        ["Better Flashlight"] = "Better Flashlight",
        ["Charmed Dice"] = "Charmed Dice",
        ["Divine Shield"] = "Divine Shield",
        ["Minimap"] = "Minimap",
        ["Pomegranate: Silver Bullets"] = "Pomegranate: Silver Bullets",
        ["Rabbit Foot"] = "Rabbit Foot",
        ["Tool Belt"] = "Tool Belt",
        ["Wrench"] = "Wrench",
    },

    validBadges = {
        3874110400924637,
        1326923835431376,
        2867198960485061,
        3135433455853246,
        2082067513342409,
        2887723509100453,
        1105185605047810,
        1978855829917980,
        3196816411149877,
        3574897773180858,
        3000312548267557,
        1005562475096003,
        2731133768842391,
        3013165365065227,
        2170442024947967,
        1046979991297675,
        1287664688452932,
        1821485024126360,
        1449859048069917,
        3727282747470690,
        3304380145666761,
        4115434233023062,
        3587013674771108,
        1286066784536879,
        3153588968519010,
        14859838755150,
        3118699128079930,
        3620761915549236,
        3482312684358694,
        3496701479407235,
        2328072899143899,
        3768815392421732,
        3002205004786425,
        3791934041907124,
        1601600987056418,
        3251260217709120,
        2496788416783492,
        2256267995417871,
        956462664712323,
        2477567326500757,
        1924155496133530,
        567711073367824,
        462002287809773,
        2877284401946924,
        658056957748983,
        2352761362962418,
        921573079298351,
        3435626204943689,
        1393889270584377, 
    }
}

local threads = {
    main = {}
}

local hookedFunctions = {}
local hookedMetaMethods = {} :: {[string]: {instance: Instance, metamethod: string, original: (...any) -> (...any)}}

local blockedRemotes = {}

local threadIndexes = {
    --main
    autoGenerator = 1,
    autoAltar = 2,
    autoGamble = 3,
    autoKill = 4,

}

local chosenBlessings = {
    ["An actual Mole"] = "An actual Mole",
    ["Rabbit Foot"] = "Rabbit Foot",
    ["Tool Belt"] = "Tool Belt",
    ["Wrench"] = "Wrench",
}

-- FEATURE FUNCTIONS
local function init()
    local oldNamecall
    oldNamecall = hookmetamethod(game, "__namecall", function(self, ...)
        local method = getnamecallmethod()

        if method == "FireServer" and blockedRemotes[self] then
            return
        end

        return oldNamecall(self, ...)
    end)

    hookedMetaMethods.namecall = {instance = game, metamethod = "__namecall", original = oldNamecall}
end

--main
local function toggleCompleteGenerators(bool: boolean)
    if not bool or threads.main[threadIndexes.autoGenerator] ~= nil then
        cleanupThread("main", threadIndexes.autoGenerator)
        
        if not bool then
            return
        end
    end

    threads.main[threadIndexes.autoGenerator] = task.spawn(function()
        while task.wait(1) do
            local generators = getGenerators()
            if countTable(generators) == 0 then
                continue
            end

            task.wait(0.75)

            repeat
                for _, model in generators do
                    remoteEvents.FUNCTIONS.repairGenerator:FireServer(model)
                end

                generators = getGenerators()
                task.wait(0.2)
            until countTable(generators) == 0

            for _, model in generators do
                repeat
                    remoteEvents.FUNCTIONS.repairGenerator:FireServer(model)
                    task.wait()
                until not model:FindFirstChild("playerDetect")
            end
        end
    end)
end

local function toggleAutoAltar(bool: boolean)
    if not bool or threads.main[threadIndexes.autoAltar] ~= nil then
        cleanupThread("main", threadIndexes.autoAltar)
        
        if not bool then
            return
        end
    end

    threads.main[threadIndexes.autoAltar] = task.spawn(function()
        while task.wait(1) do
            local blessings = getBlessings()
            if not blessings or countTable(blessings) == 0 then
                continue
            end

            for blessing, info in blessings do
                if not chosenBlessings[blessing] then
                    continue
                end

                fireProximityPrompt(info.proxPrompt)
            end

            fireProximityPrompt(workspace.ShopMap.exit.ProximityPrompt)
        end
    end)
end

local function editValidBlessings(blessings: {string})
    --add all selected content
    for _, v in blessings do
        chosenBlessings[v] = v
    end

    --remove content that was taken off
    for i, v in chosenBlessings do
        if table.find(blessings, v) then
            continue
        end

        chosenBlessings[i] = nil
    end
end

local function toggleAutoGamble(bool: boolean)
    if not bool or threads.main[threadIndexes.autoGamble] ~= nil then
        cleanupThread("main", threadIndexes.autoGamble)
        
        if not bool then
            return
        end
    end

    threads.main[threadIndexes.autoGamble] = task.spawn(function()
        while task.wait(1) do
            local gambleMap = workspace:FindFirstChild("GambleMap")
            if not gambleMap then
                continue
            end

            local exit = gambleMap:WaitForChild("exit")
            fireProximityPrompt(exit.ProximityPrompt)
        end
    end)
end

local function toggleAutoKill(bool: boolean)
    if not bool or threads.main[threadIndexes.autoKill] ~= nil then
        cleanupThread("main", threadIndexes.autoKill)
        
        if not bool then
            return
        end
    end

    threads.main[threadIndexes.autoKill] = task.spawn(function()
        while task.wait(1) do
            local enemies = workspace.Entities:GetChildren()
            for i, enemy in enemies do
                if enemy:IsA("Model") then
                    continue
                end

                remoteEvents.FUNCTIONS.killEnemy:FireServer(enemy, enemy:GetPivot().Position)
                task.wait()
            end
        end
    end)
end

local function toggleImmortal(bool: boolean)
    local target = remoteEvents.FUNCTIONS.killPlayer

    if bool then
        blockedRemotes[target] = true
    else
        blockedRemotes[target] = nil
    end
end

local function awardBadges()
    for _, badgeId in configs.validBadges do
        remoteEvents.FUNCTIONS.awardBadge:FireServer(badgeId)
        task.wait()
    end
end

local function toggleAntiIdle(bool: boolean)
    local connection = getconnections(localPlayer.Idled)[1]

    if bool then
        connection:Disable()
    else
        connection:Enable()
    end
end

-- HELPER FUNCTIONS
function cleanupThread(threadCategory: string, threadIndex: number)
    local thread = threads[threadCategory][threadIndex]
    if type(thread) == "thread" then
        task.cancel(thread)
    elseif thread ~= nil then
        thread:Disconnect()
    end

    threads[threadCategory][threadIndex] = nil
end

function countTable(list: {[any]: any}): number
    local count = 0

    for _, _ in list do
        count += 1
    end

    return count
end

function fireProximityPrompt(proxPrompt: ProximityPrompt)
    local ancestor = proxPrompt:FindFirstAncestorOfClass("Part") or proxPrompt:FindFirstAncestorOfClass("Model")
    local cframe = if ancestor:IsA("Model") then ancestor:GetPivot() else ancestor.CFrame

    localPlayer.Character:PivotTo(cframe * CFrame.new(0, 3, 0))
    task.wait(0.25)
    proxPrompt:InputHoldBegin()
    proxPrompt:InputHoldEnd()
end

function listToArray(list: {[any]: any}): {any}
    local array = {}

    for _, v in list do
        table.insert(array, v)
    end

    return array
end

function getGenerators(): {Model}
    local waypoints = workspace.Waypoints:GetChildren()
    local generators = {}

    for _, part: Part in waypoints do
        local generator = part:FindFirstChildOfClass("Model")
        if not generator then
            continue
        end

        generators[generator] = generator
    end

    return generators
end

function getBlessings(): {string: {index: number, proxPrompt: ProximityPrompt}}?
    local shop = workspace:FindFirstChild("ShopMap")
    if not shop then
        return
    end

    local blessings = {}

    for i = 1, 3, 1 do
        local altar = shop:FindFirstChild("Blessing"..i)
        if not altar then
            warn(`altar {i} not found?`)
            continue
        end

        local blessing = altar.BillboardGui.Blessing.blessingText.Text

        blessings[blessing] = {index = 1, proxPrompt = altar.ProximityPrompt}
    end

    return blessings
end

--UI
local window = Rayfield:CreateWindow({
    name = "MXNTINGCLIMB",
    subtitle = "ez lol",
    sidebarLayout = true,

    configuration = {
        autoSave = true,
        autoLoad = true,
        fileName = "MxntingClimb",
    },
})

local mainTab = window:CreateTab({ name = "Main", icon = 0 })

local completeGeneratorsToggle = mainTab:CreateToggle({
    name = "Complete Generators",
    flag = "CompleteGenerators",
    value = false,
    callback = toggleCompleteGenerators
})

local autoAltarToggle = mainTab:CreateToggle({
    name = "Auto Vote Altar",
    flag = "AutoAltar",
    value = false,
    callback = toggleAutoAltar
})

local editAutoAltar = mainTab:CreateDropdown({
    name = "Blessings",
    multiSelect = true,
    options = listToArray(configs.validBlessings),
    value = listToArray(chosenBlessings),
    callback = editValidBlessings
})

local autoGambleToggle = mainTab:CreateToggle({
    name = "Auto Gamble",
    flag = "AutoGamble",
    value = false,
    callback = toggleAutoGamble
})

local awardBadgesButton = mainTab:CreateButton({
    name = "Award Badges",
    callback = awardBadges
})

local combatTab = window:CreateTab({ name = "Combat", icon = 0 })

local autoKillToggle = combatTab:CreateToggle({
    name = "Auto Kill",
    flag = "AutoKill",
    value = false,
    callback = toggleAutoKill
})

local immortalToggle = combatTab:CreateToggle({
    name = "Immortal",
    flag = "Immortal",
    value = false,
    callback = toggleImmortal
})

local globalTab = window:CreateTab({ name = "Global", icon = 0 })

local antiIdleToggle = globalTab:CreateToggle({
    name = "Anti Idle",
    flag = "AntiIdle",
    value = false,
    callback = toggleAntiIdle
})

local settingsTab = window:CreateTab({ name = "Settings", icon = 0 })

local function Destroy()
    for category, list in threads do
        for index, thread in list do
            cleanupThread(category, index)
        end
    end

    for _, target in hookedFunctions do
        restorefunction(target)
    end

    for _, info in hookedMetaMethods do
        hookmetamethod(info.instance, info.metamethod, info.original)
    end

    window:Unload()
end

settingsTab:CreateButton({
    name = "Unload Script",
    callback = Destroy
})

--init
init()
