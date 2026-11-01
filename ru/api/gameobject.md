# GameObjectManager

Обрабатывает создание объектов и поведений по имени. В бинарнике этот тип зарегистрирован под именем `luaGameObject` и отдан как usertype `GameObjectManager`.

> **Примечание:** Точный способ получить экземпляр `GameObjectManager` в скрипте зависит от того, как движок его привязывает (в отличие от `World`/`Vector`, он не создаётся простым `GameObjectManager.new()` в поставляемых скриптах). Методы ниже — полный набор, зарегистрированный в бинарнике.

## Методы

### `instantiate(name, x, y)`
Создать именованный объект в позиции.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор объекта. |
| `x`, `y` | float | Позиция. |

```lua
manager:instantiate("some_object", x, y)
```

### `instantiateBehaviour(name, behaviour, flag)`
Создать объект с именованным скриптом поведения.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор объекта. |
| `behaviour` | string | Имя поведения/скрипта. |
| `flag` | bool | Флаг поведения. |

```lua
manager:instantiateBehaviour("creature", "defender", true)
```

### `instantiateBehaviourEx(name, behaviour, flag, x, y, w, h)`
Расширенная форма — создать поведение в явно заданной позиции и размере.

| Параметр | Тип | Описание |
|----------|-----|----------|
| `name` | string | Идентификатор объекта. |
| `behaviour` | string | Имя поведения/скрипта. |
| `flag` | bool | Флаг поведения. |
| `x`, `y` | float | Позиция. |
| `w`, `h` | float | Ширина и высота. |

```lua
manager:instantiateBehaviourEx("creature", "defender", true, x, y, 2, 2)
```

## Конструктор / деструктор

Зарегистрированы конструктор по умолчанию `()` и деструктор (`__gc`), как и у остальных usertype'ов.
