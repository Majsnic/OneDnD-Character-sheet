# D&D 2024 Character Sheet

An Obsidian plugin that renders a fully interactive D&D 2024 character sheet directly inside your vault — styled like D&D Beyond, but running entirely on your own Markdown notes.

Built to replace Dataview-based character sheets: instead of `dataviewjs` blocks re-rendering on every metadata change, this plugin uses its own `MarkdownRenderChild`-based code block processors, so editing one field doesn't force a multi-second refresh across the whole sheet.

## Features

- Full character sheet with dedicated, always-live tabs: Overview, Abilities, Saving Throws, Skills, Senses, Proficiency Bonus, Defenses, HP, Conditions, Speed, Actions, Spells, Inventory, Features, and Notes.
- Create Character wizard: card-based Species/Class selection, 2024 backgrounds, ability score methods, languages, and class/species info panels — modeled on D&D Beyond's own creation flow.
- Inventory tab with Equipment/Backpack/Party Inventory sections, weight and currency tracking, and a real 3-slot Attunement mechanic.
- Item notes can be linked straight into a character's inventory ("Link Item"); when equipped, the plugin automatically computes attack bonus, damage, and AC from the note's frontmatter — see [`docs/item-schema.md`](docs/item-schema.md).
- Actions tab that automatically lists weapon attacks and damage-dealing cantrips/spells alongside manually-added actions.
- Mobile-friendly navigation, adapted layouts for phone/tablet use.
- D&D Beyond character importer (Settings tab).
- PDF export, built around the official 2014-style character sheet PDF layout.
- Level-up flow wired to Ability Score Improvement / Feat choices.

## Installation

1. Download `main.js`, `manifest.json`, and `styles.css` from a release (or this repo).
2. Create a folder named `dnd-2024-character-sheet` inside your vault's `.obsidian/plugins/` directory.
3. Copy the three files into that folder.
4. Reload Obsidian (or restart it), then enable "D&D 2024 Character Sheet" under Settings → Community plugins.

## Example vault

The `example-vault/` folder contains a ready-to-use content library that follows the schemas documented in [`docs/item-schema.md`](docs/item-schema.md) and used throughout this plugin's Spells/Monsters tabs:

- **`Items/`** — all 36 SRD weapons, all 13 SRD armors + shield, 78 pieces of adventuring gear (`Common/`), a handful of standalone gear notes (potions, kits, spell scroll), and generic `+1`/`+2`/`+3` magic weapon, armor, shield, and ammunition templates (`Uncommon/`, `Rare/`, `Very Rare/`).
- **`Spells/`** — all 339 SRD 5.2.1 spells (27 cantrips through 9th level), one note per spell, organized into level folders, formatted for this plugin's Spells tab.
- **`Monsters/`** — all 330 SRD 5.2.1 monster stat blocks (Aboleth through Zombie, animals included), one note per monster, formatted for the [Fantasy Statblocks](https://github.com/javalent/fantasy-statblocks) plugin convention this vault's Monster tooling expects.

Copy the contents of `example-vault/` into your own vault to get a working, linkable content library out of the box. Download it directly from [`example-vault.zip`](https://github.com/Majsnic/DnD-2024-Character-sheet-plugin/raw/refs/heads/main/example-vault.zip) in this repository, unzip it, and copy its contents into your vault — no need to clone the whole repo just for this.

### A note on scope

**Starting with v1.1.0, `main.js` itself is trimmed to match this exact scope.** Earlier releases of this plugin shipped with a much larger, homebrew-expanded set of species, classes, subclasses, and spells hardcoded into the character creator (drawing on several other sourcebooks). That larger version is what the author actually plays with, but it isn't freely licensed for public redistribution, so **this public release intentionally ships a reduced character creator**:

- **9 species**: Dragonborn, Dwarf, Elf, Gnome, Goliath, Halfling, Human, Orc, Tiefling.
- **12 classes**: Barbarian, Bard, Cleric, Druid, Fighter, Monk, Paladin, Ranger, Rogue, Sorcerer, Warlock, Wizard (no Artificer).
- **12 subclasses**, exactly one per class: Path of the Berserker, College of Lore, Life Domain, Circle of the Land, Champion, Warrior of the Open Hand, Oath of Devotion, Hunter, Thief, Draconic Sorcery, Fiend Patron, and Evoker.
- **339 spells**, the complete SRD 5.2.1 spell list and no others.

If you want the fuller character creator with more species/classes/subclasses, you'll need to add that content yourself, checked against whatever source material you have the rights to use — this repository won't include it.

**Known limitation:** the Sorcerer (Draconic Sorcery) and Wizard (Evoker) subclasses were rewritten feature-by-feature against the official SRD 5.2.1 text and should be accurate. The other 10 subclasses use feature names/levels carried over from this plugin's original, older data (some may reflect 2014-edition wording/levels rather than the 2024 SRD phrasing) — the subclass *names* are all correct 2024-SRD choices, but their feature text hasn't yet been individually verified byte-for-byte against the SRD. Treat those 10 as "close, but worth double-checking" rather than guaranteed word-for-word.

The item library also intentionally contains **only** base equipment and generically-named magic item templates — no uniquely-named magic items (Bag of Holding, Ioun Stones, artifacts, etc.). The spell and monster libraries, by contrast, reproduce the SRD's actual spell/stat-block text in full, since the SRD's Creative Commons license explicitly permits that.

## SRD Attribution

This work includes material from the System Reference Document 5.2.1 ("SRD 5.2.1") by Wizards of the Coast LLC, available at https://www.dndbeyond.com/srd. The SRD 5.2.1 is licensed under the Creative Commons Attribution 4.0 International License, available at https://creativecommons.org/licenses/by/4.0/legalcode.

This project is compatible with fifth edition and is not affiliated with, endorsed, sponsored, or specifically approved by Wizards of the Coast LLC.

## License

The plugin code in this repository (`main.js`, `styles.css`, and the schema/documentation) is released under the MIT License — see [`LICENSE`](LICENSE). The example item, spell, and monster notes in `example-vault/` are derived from the D&D System Reference Document 5.2.1 and are used here under the CC-BY-4.0 license described above.
