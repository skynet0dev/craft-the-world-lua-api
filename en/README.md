# Craft the World — Lua API

Reversed-engineered Lua API reference for **Craft the World (2014)**.

> This documentation was reconstructed directly from the game binary (`CraftWorld.exe`, built with **sol2 + Lua 5.3**). There is no official API — everything here was recovered via reverse engineering: the order of registration code, the `usertype_metatable` CTTI signatures embedded in the PDB, and disassembly of the binding thunks.

## What you can do

Craft the World exposes a small but powerful Lua API that lets you:

- **Generate and reshape the world** — place blocks, fill rooms, spawn water and creatures during level generation.
- **Spawn objects and mobs** — drop items, bosses, and interactive objects at exact coordinates.
- **Control mob AI** — write patrol and destination logic for custom creatures.
- **React to events** — run scripts when a chest crashes, a level starts, or a drop event fires.

## The four types

| Type | Constructor | Purpose |
|------|-------------|---------|
| [`World`](api/world) | `World.new()` | Read and modify the map, spawn things, query positions. |
| [`Vector`](api/vector) | `Vector.new(x, y)` | A 2D float point/vector with math operators. |
| [`GameObjectManager`](api/gameobject) | *(obtained via engine)* | Instantiate objects and behaviours by name. |
| [`MobLogic`](api/moblogic) | `getLogic()` | AI control for a mob (patrol, destinations). |

## Global functions

A handful of free functions are available without any object, e.g. [`getEventPosition()`](api/globals#geteventposition), [`getLevel()`](api/globals#getlevel), [`isOwnerDLC_monsters()`](api/globals#isownerdlc_monsters).

## Where scripts live

| Folder | Role |
|--------|------|
| `scripts/` | Global game logic — `game.lua`, `generate.lua`, event scripts. |
| `data/Components/AI/` | Mob AI scripts — one per creature behaviour. |

Pull in shared code with:

```lua
require "scripts/common"
```

## Next steps

- Read about [script callbacks](callbacks) — the named functions the engine calls.
- Browse the [API reference](api/world) for each type.
- Jump straight to [working examples](examples).
