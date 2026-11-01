# Ресурсы

Craft the World разделяет два связанных понятия:

| Понятие | Где находится | Через что | Идентификаторы из |
|---------|---------------|-----------|-------------------|
| **Ресурс** | **инвентарь** игрока (счётчики в интерфейсе) | [`addResource(name, amount)`](globals#addresourcename-amount) | `data/craft_resources.xml` |
| **Блок** | **сетка мира** (расставленные тайлы) | [`World:findBlockId`](world), [`World:setBlock`](world), [`World:getBlock`](world) | `data/blocks.xml` |

Один предмет может быть в обеих ролях: многие ресурсы, будучи поставленными из инвентаря в мир, становятся блоком (в `craft_resources.xml` у них есть атрибут `build="…"` — примерно 195 штук). Но идентификатор, который вы передаёте в `addResource`, — это всегда идентификатор **ресурса**.

---

## Добавление ресурсов

```lua
-- +10 дерева
addResource("wood", 10)

-- +25 монет (основная валюта)
addResource("money", 25)

-- +1 алмаз
addResource("diamond", 1)
```

`amount` — целое число. Вещественное значение усекается (`addResource("stone", 3.7)` добавит 3).

---

## Категории

Ресурсы сгруппированы по атрибуту `application` в `craft_resources.xml`. Всего: **484** ресурса.

| Категория | Кол-во | Что это |
|-----------|--------|---------|
| `equip` | 180 | Инструменты, оружие, броня, зелья, амулеты, книги навыков |
| `block` | 99 | Размещаемые постройки (стены, двери, башни, рабочие станции…) |
| `furniture` | 84 | Декор, мебель, достижения |
| `mining` | 40 | Сырьё, добываемое в мире |
| `food` | 38 | Съедобные предметы |
| `halfstuff` | 37 | Промежуточные материалы (доски, слитки, верёвки…) |
| `money` | 4 | Валюта и премиум-валюта |
| *(нет)* | 2 | Особые псевдо-ресурсы (`deadlock`, `recipe`, `resource_pack`) |

---

## Деньги / валюта

Основная валюта — **`money`** (монеты). Премиум-валюта — **`diamond`** (алмазы).

```
money, diamond, diamonds_pack1, diamonds_pack2
```

---

## Добыча (сырьё)

Эти ресурсы добываются копанием, рубкой и разведкой — основа крафта:

```
dirt, sand, stone, coal, iron, silver, gold, mithril, wood, sulfur,
leaves, wheat, resin, web, wool, water, brains, root, mucus, quartz,
pink_crystal, grey_mushroom, pink_mushroom, needle_crystal, snow, ice,
eye, bone, rune1, rune2, rune3, rune4, rune5, rune6,
portal_krs_blue, portal_krs_red, portal_krs_dblue, portal_krs_green,
portal_krs_violet, gargoyley_spawner
```

---

## Полуфабрикаты

Продукты предварительного крафта — вход для рецептов высших уровней:

```
christmas_tree_toy, cabels, trunk, trigger_mechanism, gunpowder, plank,
glass, steel, silver_ingot, gold_ingot, mithril_ingot, thread,
bricks_block, pot, rope, padlock, skin, chitin, nail, paint, paper,
fabric, needle, seed, rice, clay, oil_berries, feather, empty_bottle,
dragon_egg, wheel_for_trolley, flour,
portal_block1, portal_block2, portal_block3, portal_block4, portal_block5
```

---

## Еда

```
gingerbread_cookies, apple, cone, meat, meat_cooked, scrambled_eggs,
berries, cocos, egg, soup, bun, apple_pie, bread, greed_salad,
fried_fish, fish_soup, fish, fat, mushroom, mushroom_dry, mushroom_salat,
mushroom_soup, mushroom-pies, enchanted_berries, skull_cookies, punch,
spider_pie, witch_fingers, bamboo_shoots, duck_meat, toad_meat,
a_cup_of_rice, soup_from_a_toad, fried_duck, tea, beer, wine,
banshee_slowdown
```

---

## Блоки (размещаемые постройки)

Размещаемые блоки — при установке в мир становятся тайлами:

```
warrior_training_simple, warrior_training, archer_training_simple,
archer_training, magician_training_simple, magician_training,
mana_pylon, mana_generator, mana_expander, static_portal, truemp_warehouse,
wooden_block, asian_wood_wall, wall_stucco, wall_stucco_china, wall_wood_set,
wall_stone_set, wall_elite_set, ice_wall, stone_wall, brick_wall, steel_wall,
ladder, steel_ladder, fence_iron, fence_wooden, fence_brick,
door_wooden, door_room, door_iron, door_steel, hatch_wooden, hatch_iron,
collumn, collumn_gothic, collumn_stone, tower_spike, loophole_stone,
bush_flower, fountain, trap_wood, trap_iron, cell_wood, cell_iron,
wooden_spikes, ice_spikes, timbered_bridge, wooden_bridge, stone_bridge,
flag, warehouse, totem, totem_china, soul_keep, warehouse_biome_upgrade,
bas_relief, bas_relief1, roof_tile, roof_leaves, roof_asian,
smithy, workshop, workbench, magic_cauldron, millstone, ftp_craft, ftp_craft2,
elevator, trolley, trolley_switch, wood_tower, wood_tower_2, catapult_tower,
tesla_tower, chest_wooden, chest_metal, gargoyle, gargoyle2,
spawn_cube_zombie, spawn_cube_skeleton, spawn_cube_gargoyle,
spawn_cube_zombie_armorbreaker, spawn_cube_archer,
dragon_statue1, dragon_statue2, torch_mucus, miner_lamp, lamponthefloor3,
outdoor_lamp, staging, beware, pumpkin_pot, farm_fence, animal_trap,
release_process, brain_in_jar, disable_device, support, service_generate_hole
```

---

## Мебель и декор

```
new_year_tree, rudolf, garland, christmas_wreath, flashlight_china, flashlight,
flashlight_china_03, fireworks, window, round_window, stained_window_1,
window_stained_7, window3, chair_stone, chair, chair_china, big_chair,
chair_tissue, chair_leather, chair_leather_china, sofa, sofa_china, carpet,
locker3, locker_china, locker, case_glass, bookshelf, mirror_ring, mirror,
picture3, picture_02, halloween_picture, picture_china, flag1, elven_flag,
elven_shield, bed_leaves, bed, bed_02, bed_china, bed_chic, bed_multistorey,
table_wooden, table_china, table_ice, table_expensive, table_normal_01,
clay_vase, glass_vase, vase_china, decorative_skin, decorative_shield,
bones_shield, barrel, wine_barrel_dlc, plant_pot, plant, aquarium,
clock1, clock2, bird, statue3, gunnar_ironfist, tricky_toki, ice_statue,
beetle_biome_worker_in_stone, basilisk_pet_house, cat_pet_house, imp_pet_house,
crip_cat_pet_house, ACH_GIFTED_RESEARCHER, ACH_FORTRESS_KEEPER, ACH_GREAT_DONATOR,
ACH_FINISH_FIRST_LEVEL, ACH_HARDCORE_GAME, ACH_IMMORTAL, ACH_GIANT_WORM_KILER,
ACH_GREAT_EXPLORER, lamp, chandelier_small, kitchen_wood, stone_furnace,
big_furnace
```

---

## Снаряжение (инструменты, оружие, броня, зелья, навыки)

```
stone_dagger, axe_stone, chopper_iron, axe_steel, silver_axe, mithril_axe,
pick_stone, pick_iron, pick_cave_goblin, pick_steel, pick_silver, pick_mithril,
rusty_sceleton_saber, rusty_mummy_saber, cave_goblin_saber, sword_iron,
cave_goblin_saber1, cave_goblin_saber2, sword_silver, godeus_sword_th, sword_mithril,
club, rusty_sceleton_mace, rusty_sceleton_heavy_mace, club_iron,
beatle_poisonous_club, club_mithril, funny_sceleton_club, frostling_spear,
goblin_spear, cave_goblin_spear, elven_spear, zombie_spear, zombie_spear_02,
knight_sword, sword_dao, tower_arrow, bucket, scissors,
winter_hat1, winter_hat2, winter_hat3, desert_hat_01, desert_hat_02, base_hat,
cloak_hat, tinker_hat, woodman_hat, helm_diver, helm_wood, helm_leather,
helm_hitin, helm_steel, helm_silver, helm_elven, helm_gold, helm_mithril,
santa_hat, rusty_sceleton_helmet, jason_mask, jason_mask_adv, shaman_cloak,
knight_helmet, demon_hunter, witch_hat, witch2_hat, funny_sceleton_helmet,
angel_nimbus, bat_hat, demon_horns, pirat_hat, pumpkin_hat, scream_mask,
dragon_warrior_helmet, scaled_armor_helmet, asian_hat_01, asian_hat_02,
winter_boots, winter_shoes, desert_shoes, desert_sneakers, leather_boots,
boots_hitin, iron_boots, silver_boots, gold_boots, elven_boots, mithril_boots,
knight_boots, funny_sceleton_shoes, dragon_warrior_boots, scaled_armor_boots,
cloth_asian_01, cloth_asian_02, ordinary_shirt, ordinary_shirt1, ordinary_shirt2,
ordinary_shirt_fem1, wood_armor, shirt_skin, hitin_armor, steel_chain_armor,
silver_armor, gold_armor, elven_armor, mithril_armor, santa_armor, knight_armor,
funny_sceleton_cuirass, dragon_warrior_cuirass, scaled_armor,
portal_biome, portal_biome_single, mportal, mlight, mfireball, mforest,
mcompas, mhorn, mrain, mcollect, mexplosion, mimp, point_click,
backpack_01, backpack_02, saw, miners_lamp, bucket_of_worms, grinding_stone,
archer_glove, serving_spoon, smith_apron, alchemist_bag, sieve, climbing_cats,
float, necklace, chitin_ring, parachute, ladders_kit,
wood_shield, iron_shield, gold_shield, funny_sceleton_shield,
regen_ring1, regen_ring2, regen_ring3, skills_ring1, skills_ring2, skills_ring3,
undead_amulet1, undead_amulet2, undead_amulet3,
miner_amulet1, miner_amulet2, miner_amulet3,
healt_elexir, Broth_of_combat_madness, mana_elexir, fireball_elexir,
spark_elexir, icearrow_elexir, transform_elexir, forget_elexir, revival_elexir,
stonecutter_skill, logger_skill, minner_skill, fisher_skill, swordsman_skill,
archer_skill, cook_skill, smith_skill, carpenter_skill, mage_skill,
miller_skill, hunter_skill, climber_skill, swimmer_skill
```

---

## Примечания

- Источником истины для каждого идентификатора ресурса, названия, цены в магазине и свойств крафта является **`data/craft_resources.xml`** в папке игры.
- Чтобы поставить блок в мир (а не добавить ресурс инвентаря), используйте [`World:setBlock`](world) с идентификатором блока из `data/blocks.xml`.
- Имена ресурсов **регистрозависимые** и используют подчёркивание, а не пробел.
