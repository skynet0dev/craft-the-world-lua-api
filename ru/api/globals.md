# Глобальные функции

Несколько свободных функций доступны без какого-либо объекта. Они зарегистрированы прямо как глобалы в движке.

---

### `getEventPosition() → Vector`
Получить позицию текущего события. Используется в скриптах событий (`*_drop.lua`, `*_OnCrash.lua`), чтобы узнать, *где* произошло событие.

```lua
local pos = getEventPosition()
local world = World.new()
world:spawn("rat", pos.x, pos.y)
```

### `getLevel() → int`
Получить текущий уровень / глубину. Полезно для масштабирования сложности или шанса дропа по глубине.

```lua
local level = getLevel()
local maxLevel = 5
if level > maxLevel then level = maxLevel end
local probability = level / maxLevel
```

### `isOwnerDLC_monsters() → bool`
Принадлежит ли игроку DLC «Monsters». Открывает за ним контент.

```lua
if isOwnerDLC_monsters() then
    -- контент только для DLC Monsters
end
```

### `isOwnerDLC_wonderwood() → bool`
Принадлежит ли игроку DLC «Wonderwood».

```lua
if isOwnerDLC_wonderwood() then
    if World.new():getPlanet() == "wonderwood" then
        World.new():setWaterDeadline(23)
    end
end
```

### `spawnRecipeScroll(name, x, y)`
Заспавнить свиток рецепта (открытый рецепт крафта) в позиции.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | ID рецепта/свитка. |
| `x`, `y` | float | Позиция. |

```lua
local pos = getEventPosition()
spawnRecipeScroll("lighthouse_part1", pos.x, pos.y)
```

### `openRecipe(name)`
Открыть (разблокировать) рецепт крафта по id. После этого рецепт появляется в меню крафта игрока.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор рецепта. |

```lua
openRecipe("axe_steel")
```

### `addResource(name, amount)`
Добавить ресурс в инвентарь игрока. Это ресурс инвентаря (`money`, `wood`, `stone`, …), *не* блок, поставленный в мире — разница и полный список имён на странице [Ресурсы](resources).

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор ресурса (из `craft_resources.xml`). |
| `amount` | int | Сколько добавить. Вещественное значение усекается до целого. |

```lua
-- +10 дерева в инвентарь
addResource("wood", 10)

-- +25 монет
addResource("money", 25)
```

### `getLogic() → MobLogic`
Получить [`MobLogic`](moblogic) текущего моба. Доступно внутри скриптов ИИ мобов.

```lua
mob = getLogic()
mob.patrolRadius = 5
```

---

<div class="callout warn">
<strong>Замечание про <code>log_print</code></strong> — закомментированный вызов <code>log_print(...)</code> встречается в <code>common.lua</code>, но строка <code>log_print</code> <em>отсутствует</em> в финальном бинарнике. Либо такой функции в продакшн-сборке нет, либо она только отладочная.
</div>
