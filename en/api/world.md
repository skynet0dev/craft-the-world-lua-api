# World

The central class for reading and modifying the map. Obtain an instance with `World.new()` (it's a lightweight handle — creating one does not copy the world, it just gives you access).

```lua
local world = World.new()
```

## Coordinates

- `x`, `y` are **floats** — you can pass fractional values (e.g. `pos.x + 0.5`).
- For block methods, the trailing `bool` is the **layer** flag: `true` = upper layer, `false` = lower layer.
- Block IDs are integers. `-1` means "empty / air" when setting or reading.

---

## Methods

### `instance(name, x, y)`
Spawn a named **object/structure** (e.g. a building or interactive object) at a position.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Object identifier. |
| `x`, `y` | float | Position. |

```lua
world:instance("lighthouse", x, y)
```

### `spawn(name, x, y)`
Spawn a **mob/creature** at a position.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Creature id (e.g. `"rat"`, `"kraken"`). |
| `x`, `y` | float | Position. |

```lua
world:spawn("kraken", x, y)
```

### `disallowResource(id)`
Mark a resource as unavailable on this level.

| Param | Type | Description |
|-------|------|-------------|
| `id` | string | Resource id, e.g. `"rune1"`. |

```lua
world:disallowResource("rune1")
```

### `setWaterDeadline(value)`
Set the water deadline (used in underwater / special levels).

| Param | Type | Description |
|-------|------|-------------|
| `value` | float | Deadline value. |

```lua
world:setWaterDeadline(23)
```

### `addWater(x, y, amount)`
Add water at a cell.

| Param | Type | Description |
|-------|------|-------------|
| `x`, `y` | float | Cell position. |
| `amount` | float | Water amount. |

```lua
world:addWater(x, y, 5)
```

### `checkBlockProperty(prop, x, y) → bool`
Check whether the block at a cell has a given property (e.g. is it lava?).

| Param | Type | Description |
|-------|------|-------------|
| `prop` | string | Property name, e.g. `"lava"`. |
| `x`, `y` | float | Cell position. |

**Returns:** `true` if the block has that property.

```lua
if world:checkBlockProperty("lava", x, y) then
    world:setBlock(x, y, -1, true)
    world:setBlock(x, y, -1, false)
end
```

### `getPlanetId() → int`
Numeric id of the current planet/world type (e.g. `1` desert, `2` forest, `3` winter, `4` underground).

```lua
local planetId = world:getPlanetId()
```

### `searchSurfaceY(x) → int`
Find the surface Y coordinate at a given X (useful for placing things on the ground).

```lua
local y = world:searchSurfaceY(x)
```

### `getCoastPosition() → Vector`
Get the coast position as a [`Vector`](vector).

```lua
local c = world:getCoastPosition()
-- c.x, c.y
```

### `getPlanet() → string`
Get the current planet's string name, e.g. `"wonderwood"`.

```lua
if world:getPlanet() == "wonderwood" then
    world:setWaterDeadline(23)
end
```

### `putRoom(map, x, y, sx, sy, flip)`
Stamp a room template (a `.map` file) into the world.

| Param | Type | Description |
|-------|------|-------------|
| `map` | string | Room file, e.g. `"sand_temple.map"`. |
| `x`, `y` | float | Anchor position. |
| `sx`, `sy` | float | Scale/stretch factors. |
| `flip` | bool | Horizontal flip. |

```lua
world:putRoom("sand_temple.map", pos.x, pos.y + 6, -0.8, -1, true)
```

### `getShopPosition() → Vector`
Get the shop position.

```lua
local shop = world:getShopPosition()
```

### `getHomePosition() → Vector`
Get the home/base position.

```lua
local home = world:getHomePosition()
```

### `removeBlock(x, y, layer)`
Remove the block at a cell.

| Param | Type | Description |
|-------|------|-------------|
| `x`, `y` | float | Cell position. |
| `layer` | bool | `true` = upper layer, `false` = lower. |

```lua
world:removeBlock(j, i, false)
world:removeBlock(j, i, true)
```

### `getBlock(x, y, layer) → int`
Read the block id at a cell.

| Param | Type | Description |
|-------|------|-------------|
| `x`, `y` | float | Cell position. |
| `layer` | bool | `true` = upper layer, `false` = lower. |

**Returns:** block id (`-1` = empty).

```lua
if world:getBlock(x, c.y, true) == pyramideId then
    -- found it
end
```

### `setBlock(x, y, id, layer)`
Write a block id into a cell.

| Param | Type | Description |
|-------|------|-------------|
| `x`, `y` | float | Cell position. |
| `id` | int | Block id (`-1` = clear). |
| `layer` | bool | `true` = upper layer, `false` = lower. |

```lua
world:setBlock(x, y, graniteId, true)
world:setBlock(x, y, graniteId, false)
world:setBlock(x, y, -1, true)   -- clear
```

### `findBlockId(name) → int`
Look up a block's numeric id by name.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Block name, e.g. `"granite"`, `"stone"`. |

**Returns:** the block id.

```lua
local graniteId = world:findBlockId("granite")
```

### `getSize() → Vector`
Get the map dimensions as a [`Vector`](vector) (`size.x`, `size.y`).

```lua
local size = world:getSize()
for x = 0, size.x do
    -- ...
end
```

---

## Full example

```lua
require "scripts/common"

function generate()
    local world  = World.new()
    local size   = world:getSize()
    local home   = world:getHomePosition()
    local shop   = world:getShopPosition()
    local coast  = world:getCoastPosition()

    local graniteId = world:findBlockId("granite")

    -- build a granite floor and fill it with water
    for x = 45, size.x - 45 do
        for y = size.y - 4, size.y - 1 do
            world:setBlock(x, y, graniteId, true)
            world:setBlock(x, y, graniteId, false)
        end
        world:addWater(x, size.y - 5, 2)
    end

    -- spawn a boss at the centre
    world:spawn("kraken", size.x / 2, size.y - 5)
end
```
