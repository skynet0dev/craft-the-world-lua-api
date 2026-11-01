# World

Центральный класс для чтения и изменения карты. Получить экземпляр можно через `World.new()` (это лёгкий дескриптор — создание не копирует мир, а лишь даёт доступ).

```lua
local world = World.new()
```

## Координаты

- `x`, `y` — **float**, можно передавать дробные значения (например `pos.x + 0.5`).
- Для методов блоков флаг `bool` в конце — это **слой**: `true` = верхний слой, `false` = нижний.
- ID блоков — целые числа. `-1` означает «пусто / воздух» при установке или чтении.

---

## Методы

### `instance(name, x, y)`
Заспавнить названный **объект/структуру** (например здание или интерактивный объект) в позиции.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор объекта. |
| `x`, `y` | float | Позиция. |

```lua
world:instance("lighthouse", x, y)
```

### `spawn(name, x, y)`
Заспавнить **моба/существо** в позиции.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | ID существа (например `"rat"`, `"kraken"`). |
| `x`, `y` | float | Позиция. |

```lua
world:spawn("kraken", x, y)
```

### `disallowResource(id)`
Сделать ресурс недоступным на этом уровне.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `id` | string | ID ресурса, например `"rune1"`. |

```lua
world:disallowResource("rune1")
```

### `setWaterDeadline(value)`
Установить дедлайн воды (используется в подводных / особых уровнях).

| Параметр | Тип | Описание |
|----------|-----|----------|
| `value` | float | Значение дедлайна. |

```lua
world:setWaterDeadline(23)
```

### `addWater(x, y, amount)`
Добавить воду в клетку.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `x`, `y` | float | Позиция клетки. |
| `amount` | float | Количество воды. |

```lua
world:addWater(x, y, 5)
```

### `checkBlockProperty(prop, x, y) → bool`
Проверить, имеет ли блок в клетке заданное свойство (например, является ли он лавой).

| Параметр | Тип | Описание |
|----------|-----|----------|
| `prop` | string | Имя свойства, например `"lava"`. |
| `x`, `y` | float | Позиция клетки. |

**Возвращает:** `true`, если блок имеет это свойство.

```lua
if world:checkBlockProperty("lava", x, y) then
    world:setBlock(x, y, -1, true)
    world:setBlock(x, y, -1, false)
end
```

### `getPlanetId() → int`
Числовой ID текущей планеты/типа мира (например `1` пустыня, `2` лес, `3` зима, `4` подземелье).

```lua
local planetId = world:getPlanetId()
```

### `searchSurfaceY(x) → int`
Найти Y координату поверхности по заданному X (удобно для установки объектов на землю).

```lua
local y = world:searchSurfaceY(x)
```

### `getCoastPosition() → Vector`
Получить позицию берега как [`Vector`](vector).

```lua
local c = world:getCoastPosition()
-- c.x, c.y
```

### `getPlanet() → string`
Получить строковое имя текущей планеты, например `"wonderwood"`.

```lua
if world:getPlanet() == "wonderwood" then
    world:setWaterDeadline(23)
end
```

### `putRoom(map, x, y, sx, sy, flip)`
Впечатать шаблон комнаты (файл `.map`) в мир.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `map` | string | Файл комнаты, например `"sand_temple.map"`. |
| `x`, `y` | float | Позиция якоря. |
| `sx`, `sy` | float | Коэффициенты масштаба/растяжения. |
| `flip` | bool | Горизонтальное отражение. |

```lua
world:putRoom("sand_temple.map", pos.x, pos.y + 6, -0.8, -1, true)
```

### `getShopPosition() → Vector`
Получить позицию магазина.

```lua
local shop = world:getShopPosition()
```

### `getHomePosition() → Vector`
Получить позицию дома/базы.

```lua
local home = world:getHomePosition()
```

### `removeBlock(x, y, layer)`
Удалить блок в клетке.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `x`, `y` | float | Позиция клетки. |
| `layer` | bool | `true` = верхний слой, `false` = нижний. |

```lua
world:removeBlock(j, i, false)
world:removeBlock(j, i, true)
```

### `getBlock(x, y, layer) → int`
Прочитать ID блока в клетке.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `x`, `y` | float | Позиция клетки. |
| `layer` | bool | `true` = верхний слой, `false` = нижний. |

**Возвращает:** ID блока (`-1` = пусто).

```lua
if world:getBlock(x, c.y, true) == pyramideId then
    -- нашли
end
```

### `setBlock(x, y, id, layer)`
Записать ID блока в клетку.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `x`, `y` | float | Позиция клетки. |
| `id` | int | ID блока (`-1` = очистить). |
| `layer` | bool | `true` = верхний слой, `false` = нижний. |

```lua
world:setBlock(x, y, graniteId, true)
world:setBlock(x, y, graniteId, false)
world:setBlock(x, y, -1, true)   -- очистить
```

### `findBlockId(name) → int`
Найти числовой ID блока по имени.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Имя блока, например `"granite"`, `"stone"`. |

**Возвращает:** ID блока.

```lua
local graniteId = world:findBlockId("granite")
```

### `getSize() → Vector`
Получить размеры карты как [`Vector`](vector) (`size.x`, `size.y`).

```lua
local size = world:getSize()
for x = 0, size.x do
    -- ...
end
```

---

## Полный пример

```lua
require "scripts/common"

function generate()
    local world  = World.new()
    local size   = world:getSize()
    local home   = world:getHomePosition()
    local shop   = world:getShopPosition()
    local coast  = world:getCoastPosition()

    local graniteId = world:findBlockId("granite")

    -- построить гранитный пол и залить его водой
    for x = 45, size.x - 45 do
        for y = size.y - 4, size.y - 1 do
            world:setBlock(x, y, graniteId, true)
            world:setBlock(x, y, graniteId, false)
        end
        world:addWater(x, size.y - 5, 2)
    end

    -- заспавнить босса в центре
    world:spawn("kraken", size.x / 2, size.y - 5)
end
```
