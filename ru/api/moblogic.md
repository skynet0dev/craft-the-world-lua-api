# MobLogic

Управление ИИ одного моба. Получить его можно через глобальную `getLogic()` внутри скрипта ИИ моба (`data/Components/AI/*.lua`).

```lua
mob = getLogic()
```

## Поля

| Поле | Тип | Описание |
|------|-----|----------|
| `patrolRadius` | int | Как далеко от своей точки патрулирует моб. |
| `patrolNearPoint` | bool | Патрулировать ли рядом с определённой точкой. |

Поля читаются/пишутся напрямую:

```lua
mob.patrolNearPoint = true
mob.patrolRadius = 5
```

## Методы

### `setDest(vector)`
Отправить моба в точку назначения [`Vector`](vector).

| Параметр | Тип | Описание |
|----------|-----|----------|
| `vector` | Vector | Точка назначения. |

```lua
mob:setDest(mob:getSpawnCell())
```

### `getSpawnCell() → Vector`
Клетка, где этот моб заспавнился.

```lua
local spawnPoint = mob:getSpawnCell()
```

### `getPosition() → Vector`
Текущая позиция моба.

```lua
local p = mob:getPosition()
```

### `getPositionCell() → Vector`
Текущая позиция моба, привязанная к клетке.

```lua
local cell = mob:getPositionCell()
```

## Полный пример — патрулирование фиксированной области

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
    -- ИИ каждый кадр, если нужен
end
```
