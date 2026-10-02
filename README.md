# Cards

Cards is a TERA Toolbox mod that automatically switches card presets and collection effects for supported dungeons, bosses, and open-world encounters. Assign enemy categories to your saved presets in `config.json`, then let the mod select them as you play.

## Features

- Selects configured card presets on dungeon entry and specific boss encounters.
- Changes collection effects to match each supported encounter.
- Provides an in-game enable/disable command and selection messages.
- Includes two item-triggered collection-effect shortcuts.

## Requirements

- TERA Toolbox with the `mod.command`, `mod.hook`, and `mod.toServer` APIs.
- Saved card presets and collection effects available to your character.
- Server/client packet definitions, opcodes, and encounter IDs compatible with this mod.

The bundled opcode fragment targets protocol **381290**. Other protocols require their own matching values. Dungeon and boss IDs can vary between servers.

## Installation

1. Close Toolbox. Back up existing Cards settings and the Toolbox protocol files you will edit.
2. Download **Code → Download ZIP**, or clone this repository:

   ```powershell
   git clone https://github.com/pyureee/cards.git Cards
   ```

3. Place the downloaded folder in `<Toolbox>\mods\Cards`. Rename `cards-main` to `Cards` when using the ZIP. Ensure `index.js`, `module.json`, and `config.json` sit directly inside the folder.
4. Copy the three files from `definitions/` into `<Toolbox>\data\definitions`:

   ```text
   C_ACTIVATE_CARD_COMBINE_LIST.1.def
   C_DEACTIVATE_CARD_COMBINE_LIST.1.def
   C_CHANGE_CARD_PRESET.1.def
   ```

   Compare existing definitions before replacing them. Each bundled definition has one `uint32` field: `id` for effect activation/deactivation and `preset` for preset switching.

5. For protocol **381290**, merge the entries from `opcodes/protocol.381290.map` into Toolbox's active `data\opcodes\protocol.381290.map`:

   ```text
   C_CHANGE_CARD_PRESET 36656
   C_DEACTIVATE_CARD_COMBINE_LIST 20541
   C_ACTIVATE_CARD_COMBINE_LIST 30699
   ```

   The bundled map is a three-entry fragment. Preserve the rest of the active map and keep one entry per packet name. Retain existing matching entries. For another protocol, obtain that protocol's matching opcode values first.

6. Configure your presets using the next section.
7. Start Toolbox and launch TERA through it. Enter a supported dungeon and check the selected deck and collection effects in the Cards UI.

Cards starts enabled whenever the module loads. Automatic updates are disabled in `module.json`; install updates manually and preserve your configuration.

## Configuration

Save your card decks in TERA's Cards UI, then edit `mods\Cards\config.json`. Each preset setting uses the **in-game preset number**.

For example, these settings select preset 3 for Argon and preset 2 for Magical Device:

```json
"argonPreset": 3,
"magicaldevicePreset": 2
```

The configuration uses **1-based** numbers. The mod subtracts one before sending the packet: configured preset `3` becomes packet value `2`. Use positive whole numbers for presets your character has saved. The runtime performs no range validation. Multiple categories can share one preset.

### Preset settings

| Key | Category / purpose | Included value |
| --- | --- | ---: |
| `ancestorPreset` | Ancestor | 1 |
| `azartPreset` | Azart | 1 |
| `demonPreset` | Demon | 1 |
| `argonPreset` | Argon | 3 |
| `dragonPreset` | Dragon | 1 |
| `godPreset` | God | 1 |
| `magicalPreset` | Magical Creature | 1 |
| `magicaldevicePreset` | Magical Device | 2 |
| `giantPreset` | Giant | 1 |
| `beastPreset` | Beast | 1 |
| `basicPreset` | Basic / None Type | 1 |

Adjust the included values to match your saved decks.

### Collection-effect settings

| Key | Included value | Behavior |
| --- | ---: | --- |
| `defaultEffect` | 17 | Baseline effect activated during every normal preset switch. |
| `secondaryEffect` | 22 | Encounter effect used in Ghillieglade. |

Keep these values unless you have verified the effect IDs for your server. The original configuration describes amplification / MCP / PCP effects; exact stat values require verification against server card data. Most encounter effects are fixed IDs in `index.js`.

The `"dont edit these"` entry is an informational string that the runtime ignores.

### Fishing settings

`fishing` defaults to `false`, `fishPreset` to `10`, and `rodID` contains fishing-rod item IDs. The runtime never reads these fields. Changing them has no effect; fishing automation is absent from this version.

### Applying changes

Save valid JSON with quoted keys, commas, and the surrounding braces. Restart Toolbox to reload the configuration. The enabled state is stored in memory and resets to enabled when the module loads.

## Commands

Use Toolbox's command chat channel, commonly `/8`:

| Command | Action |
| --- | --- |
| `/8 cards` | Toggle Cards. |
| `/8 cards on` | Enable Cards. |
| `/8 cards off` | Disable Cards. |

Arguments are case-insensitive. Any argument other than `on` or `off` toggles the state. The command reports `Cards enabled.` or `Cards disabled.`

Enabling applies to future events. Enter or re-enter a supported zone, or trigger a supported encounter, to select cards. Disabling stops further automatic changes and retains the current preset and effects.

## Dungeon-entry rules

These rules run when `S_LOAD_TOPO` reports the listed zone. The listed effect is activated alongside `defaultEffect`.

| Dungeon / source label | Zone ID | Preset key | Effect ID |
| --- | ---: | --- | ---: |
| Crab | 3012 | `basicPreset` | 22 |
| Hall of Argon Queen | 3047 | `argonPreset` | 24 |
| Hall of Argon Queen (Hard) | 3147 | `argonPreset` | 24 |
| Ice Throne | 3109 | `magicalPreset` | 29 |
| Chaos Ice Throne | 3209 | `magicalPreset` | 29 |
| Ruinous Manor (Hard) | 9970 | `basicPreset` | 22 |
| Twisted Abyss | 3920 | `argonPreset` | 24 |
| Ghillieglade | 9713 | `basicPreset` | `secondaryEffect` (22) |
| Akalath Quarantine | 3023 | `argonPreset` | 22 |
| Sky Cruiser (Hard) | 3036 | `demonPreset` | 9 |
| Manglemire | 9070 | `giantPreset` | 22 |
| Catalepticon | 3104 | `azartPreset` | 28 |
| Catalepticon (Hard) | 3204 | `azartPreset` | 28 |
| Lumikan Trial | 3040 | `azartPreset` | 22 |
| Killing Grounds | 3106 | `ancestorPreset` | 33 |
| Killing Grounds (Hard) | 3206 | `ancestorPreset` | 33 |
| Killing Grounds Trial | 3042 | `ancestorPreset` | 22 |
| Draakon Arena | 3102 | `azartPreset` | 28 |
| Draakon Arena (Hard) | 3202 | `azartPreset` | 28 |
| Forbidden Arena [Undying Warlord] | 3103 | `ancestorPreset` | 22 |
| Forbidden Arena [Undying Warlord] (Hard) | 3203 | `ancestorPreset` | 22 |
| Desolarus Testing Grounds | 3107 | `argonPreset` | 22 |
| Corrupted Skynest | 3026 | `argonPreset` | 22 |
| Corrupted Skynest (Hard) | 3126 | `argonPreset` | 22 |
| Fusion Laboratory Trial | 3046 | `azartPreset` | 37 |
| Fusion Laboratory | 3105 | `azartPreset` | 22 |
| Cursed Fusion Laboratory | 3205 | `azartPreset` | 22 |
| Pit of Petrax | 9126 | `basicPreset` | 22 |
| Damned Citadel | 3041 | `demonPreset` | 22 |
| Stormed Citadel | 3044 | `demonPreset` | 22 |
| RK-9 Rampaging | 3034 | `magicaldevicePreset` | 31 |

## Boss and open-world rules

These rules use `S_SPAWN_NPC`, except DSU, which uses `C_MEET_BOSS_INFO`. Hunting-zone and template IDs identify the encounter. The listed effect is activated alongside `defaultEffect`.

| Area | Zone ID | Encounter | Hunting-zone ID | Template ID | Preset key | Effect ID |
| --- | ---: | --- | ---: | ---: | --- | ---: |
| DSU | 9034 | Dakuryon | 434 | 3000 | `magicalPreset` | 29 |
| DSU | 9034 | Lakan | 434 | 7000 | `godPreset` | 26 |
| DSU | 9034 | Desolarus | 434 | 8000 | `argonPreset` | 24 |
| DSU | 9034 | Darkan | 434 | 9000 | `demonPreset` | 32 |
| DSU | 9034 | Manaya | 434 | 10000 | `argonPreset` | 24 |
| Catalepticon | 3104 | Lumikan | 3104 | 1000 | `azartPreset` | 28 |
| Catalepticon (Hard) | 3204 | Nightmare Lumikan | 3204 | 1000 | `azartPreset` | 28 |
| Killing Grounds | 3106 | Gardan | 3106 | 1000 | `ancestorPreset` | 22 |
| Killing Grounds (Hard) | 3206 | Nightmare Gardan | 3206 | 1000 | `ancestorPreset` | 22 |
| RK-9 Rampaging | 3034 | Ventarun | 3034 | 1000 | `magicaldevicePreset` | 31 |
| RK-9 Rampaging | 3034 | Hexapleon | 3034 | 2000 | `magicaldevicePreset` | 31 |
| RK-9 Rampaging | 3034 | Rampaging RK-9 | 3034 | 3000 | `magicaldevicePreset` | 31 |
| Frost Reach | 7012 | Sabranak | 34 | 2003 | `dragonPreset` | 22 |
| Frost Reach | 7012 | Frygaras | 34 | 2002 | `dragonPreset` | 22 |
| Timeless Woods / Blessing Basin | 7011 | Anansha | 29 | 2001 | `magicalPreset` | 22 |
| Timeless Woods / Blessing Basin | 7011 | Ortan | 434 | 7000 | `beastPreset` | 22 |
| Timeless Woods / Blessing Basin | 7011 | Dreadreaper | 622 | 1000 | `magicalPreset` | 22 |
| Ruinous Manor (Normal) | 9770 | Resurrected Atrocitas | 770 | 1000 | `magicalPreset` | 22 |
| Ruinous Manor (Normal) | 9770 | Lachelith | 770 | 3000 | `demonPreset` | 22 |
| Gossamer Vault (Easy) | 3101 | Hellgrammite | 3101 | 1000 | `magicalPreset` | 22 |
| Gossamer Vault (Easy) | 3101 | Gossamer Regent | 3101 | 2001 | `magicalPreset` | 22 |
| Grotto of Lost Souls (Hard) | 9982 | Nightmare Nedra | 982 | 1000 | `demonPreset` | 22 |
| Grotto of Lost Souls (Hard) | 9982 | Nightmare Ptakum | 982 | 2000 | `demonPreset` | 22 |
| Grotto of Lost Souls (Hard) | 9982 | Nightmare Kylos | 982 | 3000 | `dragonPreset` | 22 |
| Velik's Hold | 9780 | Kavador | 780 | 1000 | `magicalPreset` | 22 |
| Velik's Hold | 9780 | Prokyon | 780 | 2000 | `magicalPreset` | 22 |
| Velik's Hold | 9780 | Veldeg | 780 | 3000 | `demonPreset` | 22 |
| Lorcada | 7022 | Cerrus | 994 | 1000 | `beastPreset` | 22 |
| Vehemos area | 7015 | Vehemos | 620 | 1000 | `giantPreset` | 22 |
| Val Palrada | 7013 | Hazard | 777 | 77730 | `magicalPreset` | 22 |
| Commander Residence | 3030 | Maknakh | 3030 | 1000 | `azartPreset` | 22 |
| Commander Residence | 3030 | LB-1 | 3030 | 2000 | `magicaldevicePreset` | 22 |

Labels follow the source; server translations may differ. These tables cover active rules. Commented-out code is inactive.

### Bahaar Sanctum

Entering zone `9044` registers a spawn handler for hunting-zone ID `444`:

- **Phase one, template `1000`:** deactivates effect IDs `0` through `38`, preserves the current preset, and leaves the baseline effect deactivated.
- **Phase two, template `2000`:** selects `godPreset` with `defaultEffect` and effect `22`.

The phase-one message says `God`, while the code only clears effects. After registration, this handler checks hunting-zone and template IDs without checking the current zone ID.

## Item-triggered shortcuts

While enabled, using these items clears collection effects `0` through `38` and activates the listed pair:

| Item ID | Chat label | Activated effect IDs |
| ---: | --- | --- |
| 206046 | Demon Set | 22 and 32 |
| 200001 | Goblins Set | 9 and 32 |

These shortcuts preserve the current preset and skip `defaultEffect`. The source identifies the items by ID; confirm their names on your server. The original item-use packet continues to the server.

## Behavior and limitations

- Normal switches send 39 effect-deactivation packets, activate `defaultEffect`, activate the encounter effect, and send the preset-change packet, in that order.
- NPC rules react to matching spawns without distance, combat-state, or target checks.
- Boss rules can replace the dungeon-entry selection. Killing Grounds uses effect `33` at entry and effect `22` when its supported boss spawns.
- Unsupported zones and bosses retain the current selection. Leaving a dungeon also retains it until another rule or manual change selects cards.
- The server handles preset availability, effect ownership, and activation success. The mod sends requests without acknowledgement handling or retries.
- Other mods that change cards or collection effects can overwrite this mod's selection.
- This version has no configuration command, fishing automation, or automatic updater.

## Troubleshooting

| Symptom | Check / action |
| --- | --- |
| Unknown packet, definition, or opcode | Check the three version-1 definitions and matching entries for your active server protocol. Restart Toolbox. |
| Load error after editing settings | Validate `config.json` and check Toolbox's error log. |
| Wrong deck selected | Match the category's number to a saved in-game preset. Use positive 1-based whole numbers. |
| Automatic changes stop | Run `/8 cards on`, then enter a supported zone or trigger a supported encounter. |
| Enabling inside a dungeon keeps current cards | Re-enter the zone or trigger a supported boss; enabling alone sends no card packets. |
| Expected effect stays inactive | Check character ownership and the effect ID against your server's card data. |
| Selection changes again after a boss spawns | Check the boss table and other card-changing mods. |
| Fishing settings have no effect | The runtime ignores these settings. |

For a problem report, include the server, protocol, zone/boss, configured preset, expected result, actual result, and relevant Toolbox errors.

## Updating and removal

To update, close Toolbox, back up `config.json`, replace the module files, compare configuration changes, and restart. Update custom protocol files when the new version requires it.

To remove Cards, close Toolbox and remove `mods\Cards`. Retain shared packet definitions and opcode entries that other mods use. Select your preferred cards and effects manually in TERA.

## Files

| File / folder | Purpose |
| --- | --- |
| `index.js` | Commands, encounter rules, item shortcuts, and packet sending. |
| `config.json` | Preset assignments and effect settings. |
| `module.json` | Toolbox metadata; automatic updates are disabled. |
| `manifest.json` | Original empty manifest: `{"files": {}}`. |
| `definitions/` | Version-1 definitions for three custom card packets. |
| `opcodes/` | Three-entry fragment for protocol 381290. |
| `README.md` | Installation, configuration, commands, coverage, and troubleshooting. |

The runtime and configuration come from the installed Cards folder. This documentation describes source behavior. Live gameplay and server compatibility require verification on the target server.
