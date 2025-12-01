local scriptPaused = false
local savedState = { flying = false, following = false, noclip = false }

local function pauseScript()
    savedState.flying = flying
    savedState.following = following
    savedState.noclip = noclipEnabled

    stopFlying()
    following = false
    currentTarget = nil
    setNoclip(false)

    scriptPaused = true
end

local function resumeScript()
    if savedState.flying then startFlying() end
    if savedState.following then
        local target = getClosestPlayerInSight()
        if target then
            following = true
            currentTarget = target
        end
    end
    if savedState.noclip then setNoclip(true) end

    scriptPaused = false
end
