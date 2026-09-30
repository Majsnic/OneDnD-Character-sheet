# Item note frontmatter schema

This is the machine-readable schema used on every item note that the D&D 2024 Character Sheet plugin can read via "Link Item" on the Inventory tab, on top of the human-readable body text. It lets the plugin compute attack bonus, damage, and AC automatically instead of everything being retyped by hand.

Every field is optional — a note only includes the fields that actually apply to it (a potion has no `armor_category`, a ring has no `damage_dice`). `null`/blank means "not applicable," not "zero."

## Always present (magic items)

```yaml
Rarity: Uncommon                 # Common | Uncommon | Rare | Very Rare | Legendary | Artifact
type: object
tags:
  - Item
  - item/uncommon                # item/common, item/uncommon, item/rare, item/very-rare, item/legendary, item/artifact
item_category: Wondrous Item     # Weapon | Armor | Shield | Ring | Rod | Staff | Wand |
                                  # Wondrous Item | Potion | Scroll | Ammunition | Crafting Material |
                                  # Adventuring Gear
item_base: ""                    # the mundane item this counts as (e.g. "Dagger"), blank if none
requires_attunement: false
attunement_note: ""              # any special attunement wording, blank if the plain default applies
```

Mundane (non-magic) item notes — weapons, armor, and adventuring gear — omit `Rarity` entirely rather than writing `Rarity: Common`, and also omit `requires_attunement`/`attunement_note`, since attunement is purely a magic-item concept.

## Weapon fields (weapons only)

```yaml
damage_dice: "1d8"
damage_type: Slashing
weapon_properties: [Finesse, Light, Thrown]
range_normal: 20                 # feet, thrown/ammunition weapons only
range_long: 60
versatile_dice: "1d10"           # only if the weapon has the Versatile property
attack_bonus: 1                  # magic weapon's flat bonus to attack rolls
damage_bonus: 1                  # magic weapon's flat bonus to damage rolls
weapon_mastery: Sap              # the 2024 PHB mastery property name
```

## Armor fields (armor/shields only)

```yaml
armor_category: Medium           # Light | Medium | Heavy | Shield
armor_class_base: 14
armor_class_dex_cap: 2           # omit if uncapped, 0 if Dex adds nothing
armor_class_bonus: 0             # magic armor's flat bonus on top of the formula
strength_requirement: 13         # omit if none
stealth_disadvantage: true
```

## Charges

```yaml
charges_max: 3
charges_current: 3
charges_recharge: "1d3 at dawn"
```

## Misc bonuses and catch-all mechanical text

```yaml
other_bonuses:
  - { stat: "Passive Perception", effect: "+2" }
special_properties: "Automatically crits against objects; immune to damage from nonmagical sources."
```

## Named powers (activated effects)

```yaml
powers:
  - name: "Boar's Charge"
    action_type: "Passive (triggers on hit after a 20-ft charge)"
    charges_cost: 0               # 0 = doesn't draw from the item's shared charges_max pool
    save_ability: Strength
    save_dc: 13
    damage_dice: "2d6"
    damage_type: Piercing
    recharge: "1/Day"
    effect: "Target takes the extra damage and, on a failed save, is knocked prone."
```

`action_type` is free text (`Action`, `Bonus Action`, `Reaction`, `Magic action`, `Passive`, `Special`...) since 2024 terminology varies by item. `effect` is a short plain-language summary — the structured fields above it are what the plugin uses for the math.

## Cost and weight (mundane items)

```yaml
cost: "2 GP"                     # a plain string, whatever denomination is natural (CP/SP/GP)
weight_lb: 1                     # a bare number; omitted for negligible-weight items (candle, chalk, etc.)
```

These two fields are part of the convention for every mundane (non-magic) item note: weapons, armor/shields, and adventuring gear. Magic items generally don't carry a `cost`. The body text also ends with a plain "Cost X, weight Y lb." sentence (or "...negligible weight." when `weight_lb` is omitted).

## Mundane equipment (included in this repo's example vault)

Plain (non-magic) weapons, armor, and shields use the same schema, minus `Rarity`, with tags built as `item/common` plus `item/weapon`, `item/armor`, `item/shield`, or `item/gear`. `attack_bonus`/`damage_bonus` default to `0` and `armor_class_bonus` is omitted, since nothing magic is added.

The example vault under `example-vault/Items/` includes:
- `Common/Weapons/` — all 36 SRD weapons (10 Simple Melee, 4 Simple Ranged, 18 Martial Melee, 4 Martial Ranged), each with damage, properties, range, versatile die, and weapon mastery property.
- `Common/Armor/` — all 13 SRD armors and the shield.
- `Common/Adventuring Gear/` — 78 items from the adventuring gear table (bedroll, rope, torch, tinderbox, rations, tools and containers, etc.).
- A handful of standalone gear notes directly under `Items/` (Healer's Kit, Thieves' Tools, Antitoxin, Alchemist's Fire, Alchemist's Supplies, Poisoner's Kit, Herbalism Kit, Spell Scroll) and `Common/Potion of Healing.md`.
- `Uncommon/`, `Rare/`, `Very Rare/` — the generic magic weapon/armor/shield/ammunition templates (`+1`/`+2`/`+3`), including a dedicated per-weapon note for each of the 36 weapons at each bonus tier, so you can link straight to e.g. "+1 Scimitar" instead of a generic placeholder.

This example vault deliberately does **not** include any uniquely-named magic item (Bag of Holding, Ioun Stones, artifacts, etc.) or any homebrew content from the original campaign vault this plugin was built for — see the main README for why.

## Plugin integration: what "Equip" reads off this schema

The plugin's Inventory tab can link straight to a note that carries this schema (via "Link Item", tag `#Item`) and, when its Equip checkbox is on, feeds the following into the Actions tab / AC automatically:

- `item_category: Weapon` + `damage_dice` → an Actions row: Hit = ability mod (Str, or the better of Str/Dex if `weapon_properties` includes Finesse, or Dex if the weapon is a ranged weapon) + proficiency bonus (if a Defenses-tab weapon-proficiency entry matches) + `attack_bonus`; Damage = `damage_dice` + the same ability mod + `damage_bonus`.
- `armor_category` (+ `armor_class_base`/`armor_class_dex_cap`/`armor_class_bonus`) → recomputes AC from whichever equipped body armor gives the best result, plus any equipped Shield's bonus.
- `powers` → one Actions row per power, with its uses shown as a checkbox Uses row (charge math derived from `charges_max`/`charges_cost`, or a leading number in `recharge`).

This only works for notes reached through "Link Item" (a real `[[wikilink]]`) — inventory rows added via the manual "Add Inventory" form have no linked note to compute from, so their Equip checkbox is just a plain flag. Attunement is tracked separately on the Inventory tab's "Attunement" page (3 physical slots, enforced).
