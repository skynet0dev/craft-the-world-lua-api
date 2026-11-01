# Vector

Простой 2D вектор/точка из float. Используется везде, где возвращаются позиции ([`World.getHomePosition()`](world#gethomeposition), [`MobLogic.getSpawnCell()`](moblogic#getspawncell) и т.д.).

## Конструкторы

```lua
local a = Vector.new()        -- (0, 0)
local b = Vector.new(10, 20)  -- (10, 20)
```

## Поля

| Поле | Тип | Описание |
|------|-----|----------|
| `x` | float | Компонента X. |
| `y` | float | Компонента Y. |

Поля читаются/пишутся напрямую:

```lua
local pos = Vector.new()
pos.x = 12
pos.y = 34

print(pos.x, pos.y)  --> 12   34
```

## Методы

### `length() → float`
Длина (магнитуда) вектора.

```lua
local len = v:length()
```

### `normalized() → Vector`
Копия вектора, масштабированная до единичной длины.

```lua
local dir = v:normalized()
```

### `rotate(angle) → Vector`
Копия вектора, повёрнутая на `angle` (float, радианы/градусы в зависимости от соглашения движка).

```lua
local r = v:rotate(0.5)
```

### `equal(other) → bool`
Сравнить два вектора на равенство.

```lua
if a:equal(b) then
    -- одна и та же точка
end
```

## Математические операторы

`Vector` поддерживает поэлементные `+`, `-` и `*` (подтверждено по SSE-привязочным функциям бинарника: `addss`, `subss`, `mulss`).

```lua
local a = Vector.new(1, 2)
local b = Vector.new(3, 4)

local sum  = a + b        -- (4, 6)
local diff = a - b        -- (-2, -2)
local prod = a * b        -- (3, 8)   поэлементное
```

> Оператора `/` (деления) у `Vector` нет.

## Полный пример

```lua
function onDestPointRequest()
    local mob  = getLogic()
    local home = World.new():getHomePosition()

    local target = Vector.new(home.x, home.y)

    -- немного сдвинуться к цели
    local offset = Vector.new(1, 0)
    target = target + offset

    mob:setDest(target)
end
```
