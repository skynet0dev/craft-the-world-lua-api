# Vector

A simple 2D float point/vector. Used everywhere positions are returned ([`World.getHomePosition()`](world#gethomeposition), [`MobLogic.getSpawnCell()`](moblogic#getspawncell), etc.).

## Constructors

```lua
local a = Vector.new()        -- (0, 0)
local b = Vector.new(10, 20)  -- (10, 20)
```

## Fields

| Field | Type | Description |
|-------|------|-------------|
| `x` | float | X component. |
| `y` | float | Y component. |

Fields are read/written directly:

```lua
local pos = Vector.new()
pos.x = 12
pos.y = 34

print(pos.x, pos.y)  --> 12   34
```

## Methods

### `length() → float`
The vector's length (magnitude).

```lua
local len = v:length()
```

### `normalized() → Vector`
A copy of the vector scaled to unit length.

```lua
local dir = v:normalized()
```

### `rotate(angle) → Vector`
A copy of the vector rotated by `angle` (float, radians/degrees per engine convention).

```lua
local r = v:rotate(0.5)
```

### `equal(other) → bool`
Compare two vectors for equality.

```lua
if a:equal(b) then
    -- same point
end
```

## Math operators

`Vector` supports component-wise `+`, `-`, and `*` (confirmed from the binary's SSE binding thunks: `addss`, `subss`, `mulss`).

```lua
local a = Vector.new(1, 2)
local b = Vector.new(3, 4)

local sum  = a + b        -- (4, 6)
local diff = a - b        -- (-2, -2)
local prod = a * b        -- (3, 8)   component-wise
```

> There is no `/` (division) operator exposed for `Vector`.

## Full example

```lua
function onDestPointRequest()
    local mob  = getLogic()
    local home = World.new():getHomePosition()

    local target = Vector.new(home.x, home.y)

    -- nudge towards the target
    local offset = Vector.new(1, 0)
    target = target + offset

    mob:setDest(target)
end
```
