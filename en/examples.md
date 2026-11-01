# Examples

Ready-to-use Lua scripts showing common patterns. Each example names the file it belongs in and the callback the engine calls.

---

## 1. Level setup on start (`scripts/game.lua`)

Runs when a level starts. Adjust water deadline based on DLC and planet.

```lua
-- scripts/game.lua

function create()
    -- on level create
end

function start()
    local world    = World.new()
    local planetId = world:getPlanetId()

    if isOwnerDLC_monsters() then
        if planetId == 4 then          -- underground
            world:setWaterDeadline(23)
        end
    end

    if isOwnerDLC_wonderwood() then
        if world:getPlanet() == "wonderwood" then
            world:setWaterDeadline(23)
        end
    end
end

function update(delta)
    -- every frame
end
```

---

## 2. Generate terrain & spawn a boss (`scripts/generate.lua`)

Runs during map generation. Builds a granite chamber filled with water and spawns a boss.

```lua
-- scripts/generate.lua
require "scripts/common"

function generate()
    local world = World.new()
    local size  = world:getSize()

    local graniteId = world:findBlockId("granite")
    local indent    = 45

    -- clear lava and build a granite surface
    for x = indent, size.x - indent do
        for y = size.y - 4, size.y - 1 do
            world:setBlock(x, y, graniteId, true)
            world:setBlock(x, y, graniteId, false)
        end
        world:addWater(x, size.y - 5, 2)
    end

    -- hollow chamber
    local beginx = size.x / 2
    local endx   = beginx + 8
    for y = size.y - 4, size.y - 1 do
        for x = beginx, endx do
            if x == beginx or x == endx or y == size.y - 1 then
                world:setBlock(x, y, graniteId, true)
                world:setBlock(x, y, graniteId, false)
            else
                world:setBlock(x, y, -1, true)   -- clear
                world:addWater(x, y, 5)
            end
        end
    end

    world:setWaterDeadline(23)

    -- spawn the boss
    world:spawn("kraken", beginx + 4, size.y - 5)
end
```

---

## 3. Place a structure on the desert (`scripts/generate.lua`)

Finds a pyramid on the horizon line and stamps a temple room next to it.

```lua
-- scripts/generate.lua
function generate()
    local world = World.new()
    local home  = world:getHomePosition()
    local shop  = world:getShopPosition()
    local size  = world:getSize()
    local c     = world:getCoastPosition()

    local roomSize   = 14
    local pyramideId = world:findBlockId("pyramid")
    local pos        = Vector.new()

    for x = home.x, size.x do
        if world:getBlock(x, c.y, true) == pyramideId then
            pos.x = x
            pos.y = c.y

            if pos.x - roomSize <= shop.x then
                pos.x = shop.x + roomSize
            end

            world:putRoom("sand_temple.map", pos.x, pos.y + 6, -0.8, -1, true)
            break
        end
    end
end
```

---

## 4. Simple loot drop (`scripts/rat_drop.lua`)

Fires on a drop event — spawns a creature at the event position.

```lua
-- scripts/rat_drop.lua  (executed as a chunk, no callback needed)
local pos   = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

---

## 5. Depth-scaled drop chance (`scripts/ww_bullet_drop.lua`)

Uses the current level to make a drop more likely the deeper you are.

```lua
-- scripts/ww_bullet_drop.lua
local pos   = getEventPosition()
local world = World.new()

local level    = getLevel()
local maxLevel = 5
if level > maxLevel then
    level = maxLevel
end

local probability = level / maxLevel
if math.random() < probability then
    world:spawn("ww_bullet", pos.x, pos.y)
end
```

---

## 6. Reward a crashed chest (`scripts/lighthouse_chest_OnCrash.lua`)

Drops several recipe scrolls when a chest crashes.

```lua
-- scripts/lighthouse_chest_OnCrash.lua
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part2", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part3", pos.x, pos.y)
```

---

## 7. Mob AI — return to spawn (`data/Components/AI/defender.lua`)

Sends a mob back to its spawn cell whenever it needs a new destination.

```lua
-- data/Components/AI/defender.lua
require "scripts/common"

mob = getLogic()
mob.patrolNearPoint = true
mob.patrolRadius    = 5

function onDestPointRequest()
    mob:setDest(mob:getSpawnCell())
end
```

---

## 8. Mob AI — defend the base (`data/Components/AI/scarab_assault.lua`)

Guards path toward the home position instead of spawn.

```lua
-- data/Components/AI/scarab_assault.lua
require "scripts/common"

local world = World.new()
local mob   = getLogic()

local home       = world:getHomePosition()
local spawnPoint = mob:getSpawnCell()

mob.patrolNearPoint = true
mob.patrolRadius    = 5

function onDestPointRequest()
    mob:setDest(home)
end

function update(delta)
    -- per-frame behaviour, if needed
end
```

---

## 9. Search for flat ground (`scripts/common.lua`)

A reusable helper that scans a horizontal range for a flat platform, returning its centre. Shows multi-return values and `Vector` field access.

```lua
-- scripts/common.lua
function searchPlatform(b, e, delta, length, max_height)
    local world = World.new()
    local c     = world:getCoastPosition()

    local minY, maxY, count = 0, 0, 0

    for x = b, e do
        local y = world:searchSurfaceY(x)

        if y < c.y - max_height then count = 0; end

        if count == 0 then
            minY, maxY, count = y, y, 1
        else
            minY = math.min(minY, y)
            maxY = math.max(maxY, y)

            if math.abs(minY - maxY) > delta then
                count = 0
            else
                count = count + 1
            end
        end

        if count >= length then
            return x - count / 2, minY + (maxY - minY) / 2
        end
    end

    return 0, 0
end
```
