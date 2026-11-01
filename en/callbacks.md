# Callbacks & Script Entry Points

The engine does not "run" your script top-to-bottom as a program. Instead, it **loads** the file and then **calls specific global functions by name** at the right moments. Any function you define with one of the names below becomes a hook the engine will invoke.

> Top-level code in a script **does** run once when the file is loaded (this is why AI scripts set up `mob = getLogic()` at the top level). Everything else happens through the named callbacks.

## Game-level scripts (`scripts/game.lua`)

These drive the whole level lifecycle.

| Callback | When it fires |
|----------|---------------|
| `create()` | When the level object is created (before start). |
| `start()` | When the level actually starts. |
| `update(delta)` | Every frame while the level runs. `delta` is the elapsed time. |

```lua
-- scripts/game.lua
function create()
    -- called once, on level creation
end

function start()
    -- called when the level begins
    local world = World.new()
    local planetId = world:getPlanetId()

    if isOwnerDLC_monsters() and planetId == 4 then
        world:setWaterDeadline(23)
    end
end

function update(delta)
    -- called every frame; delta = time since last frame
end
```

## World generation (`scripts/generate.lua`)

| Callback | When it fires |
|----------|---------------|
| `generate()` | While the level map is being generated. Use this to place rooms, structures, bosses. |

```lua
-- scripts/generate.lua
require "scripts/common"

function generate()
    local world = World.new()
    local size = world:getSize()
    local home = world:getHomePosition()

    -- reshape terrain, spawn a boss, place a room, etc.
end
```

## Mob AI scripts (`data/Components/AI/*.lua`)

Each creature can have an AI script. Top-level code runs when the AI loads; the engine then calls the callbacks below.

| Callback | When it fires |
|----------|---------------|
| `onDestPointRequest()` | Whenever the mob's logic needs its next destination point. |
| `update(delta)` | Every frame while this AI is active. |

The script gets its mob via the global `getLogic()`, which returns a [`MobLogic`](api/moblogic) object.

```lua
-- data/Components/AI/defender.lua
require "scripts/common"

mob = getLogic()          -- top-level: runs once when AI loads
mob.patrolNearPoint = true
mob.patrolRadius = 5

function onDestPointRequest()
    -- the mob asks "where do I go next?"
    mob:setDest(mob:getSpawnCell())
end
```

## Event scripts (`scripts/*_drop.lua`, `*_OnCrash.lua`)

Small scripts fired by game events (a mob dropping loot, a chest crashing). They are executed **as a chunk** — you don't define named callbacks; you just write code. They can read the event position and current level.

```lua
-- scripts/rat_drop.lua  (runs when a rat drop event fires)
local pos = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

```lua
-- scripts/lighthouse_chest_OnCrash.lua  (runs when the chest crashes)
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part2", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part3", pos.x, pos.y)
```

## Quick reference by file

| File type | Defines | Engine calls |
|-----------|---------|--------------|
| `game.lua` | `create`, `start`, `update` | `create()`, `start()`, `update(delta)` |
| `generate.lua` | `generate` | `generate()` |
| `AI/*.lua` | top-level setup + `onDestPointRequest`, `update` | `onDestPointRequest()`, `update(delta)` |
| `*_drop.lua`, `*_OnCrash.lua` | plain top-level code | runs the whole chunk |
