# Примеры

Готовые Lua-скрипты, показывающие типовые приёмы. Каждый пример указывает файл, в который он belongs, и колбэк, который вызывает движок.

---

## 1. Настройка уровня при старте (`scripts/game.lua`)

Выполняется при старте уровня. Настраивает дедлайн воды в зависимости от DLC и планеты.

```lua
-- scripts/game.lua

function create()
    -- при создании уровня
end

function start()
    local world    = World.new()
    local planetId = world:getPlanetId()

    if isOwnerDLC_monsters() then
        if planetId == 4 then          -- подземелье
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
    -- каждый кадр
end
```

---

## 2. Генерация рельефа и спавн босса (`scripts/generate.lua`)

Выполняется при генерации карты. Строит гранитную камеру, залитую водой, и спавнит босса.

```lua
-- scripts/generate.lua
require "scripts/common"

function generate()
    local world = World.new()
    local size  = world:getSize()

    local graniteId = world:findBlockId("granite")
    local indent    = 45

    -- очистить лаву и построить гранитную поверхность
    for x = indent, size.x - indent do
        for y = size.y - 4, size.y - 1 do
            world:setBlock(x, y, graniteId, true)
            world:setBlock(x, y, graniteId, false)
        end
        world:addWater(x, size.y - 5, 2)
    end

    -- полая камера
    local beginx = size.x / 2
    local endx   = beginx + 8
    for y = size.y - 4, size.y - 1 do
        for x = beginx, endx do
            if x == beginx or x == endx or y == size.y - 1 then
                world:setBlock(x, y, graniteId, true)
                world:setBlock(x, y, graniteId, false)
            else
                world:setBlock(x, y, -1, true)   -- очистить
                world:addWater(x, y, 5)
            end
        end
    end

    world:setWaterDeadline(23)

    -- заспавнить босса
    world:spawn("kraken", beginx + 4, size.y - 5)
end
```

---

## 3. Установка постройки на пустыне (`scripts/generate.lua`)

Находит пирамиду на линии горизонта и ставит рядом комнату-храм.

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

## 4. Простой дроп лута (`scripts/rat_drop.lua`)

Срабатывает при событии дропа — спавнит существо в позиции события.

```lua
-- scripts/rat_drop.lua  (выполняется как чанк, колбэк не нужен)
local pos   = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

---

## 5. Шанс дропа, зависящий от глубины (`scripts/ww_bullet_drop.lua`)

Использует текущий уровень, чтобы дроп был вероятнее глубже на карте.

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

## 6. Награда за упавший сундук (`scripts/lighthouse_chest_OnCrash.lua`)

Выбрасывает несколько свитков рецептов, когда сундук падает и разбивается.

```lua
-- scripts/lighthouse_chest_OnCrash.lua
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part2", pos.x, pos.y)
spawnRecipeScroll("lighthouse_part3", pos.x, pos.y)
```

---

## 7. ИИ моба — возврат к точке спавна (`data/Components/AI/defender.lua`)

Отправляет моба обратно к его клетке спавна, когда ему нужна новая точка назначения.

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

## 8. ИИ моба — защита базы (`data/Components/AI/scarab_assault.lua`)

Охраняет путь к позиции дома вместо спавна.

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
    -- ИИ каждый кадр, если нужен
end
```

---

## 9. Поиск ровной площадки (`scripts/common.lua`)

Переиспользуемый хелпер, который сканирует горизонтальный диапазон в поисках ровной платформы и возвращает её центр. Показывает множественные возвратные значения и доступ к полям `Vector`.

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
