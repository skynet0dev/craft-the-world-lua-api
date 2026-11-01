# GameObjectManager

Handles instantiating objects and behaviours by name. In the binary this type is registered under the name `luaGameObject` and exposed as the `GameObjectManager` usertype.

> **Note:** The exact way you obtain an instance of `GameObjectManager` in a script depends on how the engine binds it (unlike `World`/`Vector`, it is not created with a plain `GameObjectManager.new()` in the shipped scripts). The methods below are the complete set registered in the binary.

## Methods

### `instantiate(name, x, y)`
Instantiate a named object at a position.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Object identifier. |
| `x`, `y` | float | Position. |

```lua
manager:instantiate("some_object", x, y)
```

### `instantiateBehaviour(name, behaviour, flag)`
Instantiate an object with a named behaviour script.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Object identifier. |
| `behaviour` | string | Behaviour/script name. |
| `flag` | bool | Behaviour flag. |

```lua
manager:instantiateBehaviour("creature", "defender", true)
```

### `instantiateBehaviourEx(name, behaviour, flag, x, y, w, h)`
Extended form — instantiate a behaviour at an explicit position and size.

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Object identifier. |
| `behaviour` | string | Behaviour/script name. |
| `flag` | bool | Behaviour flag. |
| `x`, `y` | float | Position. |
| `w`, `h` | float | Width and height. |

```lua
manager:instantiateBehaviourEx("creature", "defender", true, x, y, 2, 2)
```

## Constructor / destructor

A default constructor `()` and a destructor are registered (`__gc`), consistent with the other usertypes.
