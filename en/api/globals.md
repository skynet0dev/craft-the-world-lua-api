# Global Functions

A handful of free functions are available without any object. These are registered directly as globals in the engine.

---

### `getEventPosition() → Vector`
Get the position of the current event. Used in event scripts (`*_drop.lua`, `*_OnCrash.lua`) to know *where* the event happened.

```lua
local pos = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

### `getLevel() → int`
Get the current level / depth. Useful for scaling difficulty or drop probability by depth.

```lua
local level = getLevel()
local maxLevel = 5
if level > maxLevel then level = maxLevel end
local probability = level / maxLevel
```

### `isOwnerDLC_monsters() → bool`
Whether the player owns the "Monsters" DLC. Gate content behind it.

```lua
if isOwnerDLC_monsters() then
    -- monsters-DLC-only content
end
```

### `isOwnerDLC_wonderwood() → bool`
Whether the player owns the "Wonderwood" DLC.

```lua
if isOwnerDLC_wonderwood() then
    if World.new():getPlanet() == "wonderwood" then
        World.new():setWaterDeadline(23)
    end
end
```

### `spawnRecipeScroll(name, x, y)`
Spawn a recipe scroll (unlocked crafting recipe) at a position.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Recipe/scroll id. |
| `x`, `y` | float | Position. |

```lua
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
```

### `openRecipe(name)`
Open (unlock) a crafting recipe by id. The player then sees the recipe in the crafting menu.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Recipe id. |

```lua
openRecipe("axe_steel")
```

### `addResource(name, amount)`
Add a resource to the player's inventory. This is the inventory resource (`money`, `wood`, `stone`, …), *not* a block placed in the world — see [Resources](resources) for the difference and the full name list.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Resource id (from `craft_resources.xml`). |
| `amount` | int | How many to add. A float value is truncated to an integer. |

```lua
-- +10 wood to the inventory
addResource("wood", 10)

-- +25 coins
addResource("money", 25)
```

### `getLogic() → MobLogic`
Get the [`MobLogic`](moblogic) for the current mob. Available inside mob AI scripts.

```lua
mob = getLogic()
mob.patrolRadius = 5
```

---

<div class="callout warn">
<strong>Note on <code>log_print</code></strong> — a commented-out call to <code>log_print(...)</code> appears in <code>common.lua</code>, but the string <code>log_print</code> is <em>not</em> present in the final binary. It either doesn't exist in the shipping build or is debug-only.
</div>
