# Колбэки и точки входа

Движок не «запускает» ваш скрипт сверху вниз как программу. Вместо этого он **загружает** файл, а затем **вызывает именованные глобальные функции** в нужные моменты. Любая функция, которую вы определите с одним из имён ниже, станет хуком, который движок вызовет.

> Код верхнего уровня в скрипте **выполняется** один раз при загрузке файла (именно поэтому AI-скрипты создают `mob = getLogic()` на верхнем уровне). Всё остальное происходит через именованные колбэки.

## Скрипты уровня игры (`scripts/game.lua`)

Управляют жизненным циклом всего уровня.

| Колбэк | Когда вызывается |
|--------|------------------|
| `create()` | При создании объекта уровня (перед стартом). |
| `start()` | Когда уровень реально стартует. |
| `update(delta)` | Каждый кадр во время игры. `delta` — прошедшее время. |

```lua
-- scripts/game.lua
function create()
    -- вызывается один раз, при создании уровня
end

function start()
    -- вызывается при старте уровня
    local world = World.new()
    local planetId = world:getPlanetId()

    if isOwnerDLC_monsters() and planetId == 4 then
        world:setWaterDeadline(23)
    end
end

function update(delta)
    -- вызывается каждый кадр; delta = время с прошлого кадра
end
```

## Генерация мира (`scripts/generate.lua`)

| Колбэк | Когда вызывается |
|--------|------------------|
| `generate()` | Во время генерации карты уровня. Используйте для размещения комнат, построек, боссов. |

```lua
-- scripts/generate.lua
require "scripts/common"

function generate()
    local world = World.new()
    local size = world:getSize()
    local home = world:getHomePosition()

    -- изменить рельеф, заспавнить босса, поставить комнату и т.д.
end
```

## Скрипты ИИ мобов (`data/Components/AI/*.lua`)

Каждое существо может иметь скрипт ИИ. Код верхнего уровня выполняется при загрузке ИИ; затем движок вызывает колбэки ниже.

| Колбэк | Когда вызывается |
|--------|------------------|
| `onDestPointRequest()` | Всякий раз, когда логике моба нужна следующая точка назначения. |
| `update(delta)` | Каждый кадр, пока этот ИИ активен. |

Скрипт получает своего моба через глобальную `getLogic()`, которая возвращает объект [`MobLogic`](api/moblogic).

```lua
-- data/Components/AI/defender.lua
require "scripts/common"

mob = getLogic()          -- верхний уровень: выполняется один раз при загрузке ИИ
mob.patrolNearPoint = true
mob.patrolRadius = 5

function onDestPointRequest()
    -- моб спрашивает «куда идти дальше?»
    mob:setDest(mob:getSpawnCell())
end
```

## Скрипты событий (`scripts/*_drop.lua`, `*_OnCrash.lua`)

Небольшие скрипты, запускаемые игровыми событиями (дроп предмета с моба, падение сундука). Они выполняются **как чанк** — не нужно определять именованные колбэки; просто пишите код. Они могут читать позицию события и текущий уровень.

```lua
-- scripts/rat_drop.lua  (выполняется при событии дропа крысы)
local pos = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

```lua
-- scripts/lighthouse_chest_OnCrash.lua  (выполняется при падении сундука)
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part2", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part3", pos.x, pos.y)
```

## Краткая справка по файлам

| Тип файла | Определяет | Движок вызывает |
|-----------|-----------|-----------------|
| `game.lua` | `create`, `start`, `update` | `create()`, `start()`, `update(delta)` |
| `generate.lua` | `generate` | `generate()` |
| `AI/*.lua` | настройка верхнего уровня + `onDestPointRequest`, `update` | `onDestPointRequest()`, `update(delta)` |
| `*_drop.lua`, `*_OnCrash.lua` | обычный код верхнего уровня | выполняет весь чанк |
