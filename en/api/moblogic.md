# MobLogic

AI control for a single mob. You get one via the global `getLogic()` inside a mob AI script (`data/Components/AI/*.lua`).

```lua
mob = getLogic()
```

## Fields

| Field | Type | Description |
|-------|------|-------------|
| `patrolRadius` | int | How far from its point the mob patrols. |
| `patrolNearPoint` | bool | Whether to patrol near a specific point. |

Fields are read/written directly:

```lua
mob.patrolNearPoint = true
mob.patrolRadius = 5
```

## Methods

### `setDest(vector)`
Send the mob to a destination [`Vector`](vector).

| Param | Type | Description |
|-------|------|-------------|
| `vector` | Vector | Destination point. |

```lua
mob:setDest(mob:getSpawnCell())
```

### `getSpawnCell() → Vector`
The cell where this mob spawned.

```lua
local spawnPoint = mob:getSpawnCell()
```

### `getPosition() → Vector`
The mob's current position.

```lua
local p = mob:getPosition()
```

### `getPositionCell() → Vector`
The mob's current position, snapped to a cell.

```lua
local cell = mob:getPositionCell()
```

## Full example — patrol a fixed area

```lua
-- data/Components/AI/scarab_defender.lua
require "scripts/common"

local world = World.new()
local mob   = getLogic()

local home       = world:getHomePosition()
local spawnPoint = mob:getSpawnCell()

mob.patrolNearPoint = true
mob.patrolRadius    = 5

function onDestPointRequest()
    mob:setDest(spawnPoint)
end

function update(delta)
    -- per-frame AI, if needed
end
```
