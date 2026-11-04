# Craft the World — Lua API Documentation

Reversed-engineered Lua API reference for **Craft the World (2014)**.

**[Web Site](https://skynet0dev.github.io/craft-the-world-lua-api/#)

> This documentation was reconstructed directly from the game binary (`CraftWorld.exe`, built with **sol2 + Lua 5.3**). There is no official API documentation — everything here was recovered via reverse engineering (registration code order, `usertype_metatable` CTTI signatures from the PDB, and disassembly).

## Choose a language

- **[Русская документация](ru/)** — полный справочник на русском.
- **[English documentation](en/)** — full reference in English.

## What's inside

| Section | Description |
|---------|-------------|
| [World](en/api/world) | The main world class — spawn, blocks, map generation, positions. |
| [Vector](en/api/vector) | 2D float vector with math operators (`+`, `-`, `*`). |
| [GameObjectManager](en/api/gameobject) | Instantiating objects and behaviours. |
| [MobLogic](en/api/moblogic) | AI logic for mobs — patrol, destinations. |
| [Global functions](en/api/globals) | Free functions like `getEventPosition()`, `getLevel()`. |
| [Callbacks](en/callbacks) | Entry points the engine calls (`create`, `start`, `generate`, …). |
| [Examples](en/examples) | Ready-to-use Lua scripts. |

## How mods work

Game scripts live in the `scripts/` folder (global level/game logic) and `data/Components/AI/` (mob AI logic). You pull in other files with:

```lua
require "scripts/common"
```

The engine calls specific **callback functions** by name (e.g. `create()`, `start()`, `generate()`) depending on the script's role. See [Callbacks](en/callbacks).
