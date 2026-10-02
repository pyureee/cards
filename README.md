# Cards

TERA Toolbox mod that automatically switches card presets and collection effects for supported dungeons and bosses.

## Setup

1. Close TERA Toolbox. Back up any existing Cards configuration and protocol files you will edit.
2. Download this repository using **Code → Download ZIP**. Extract it and rename `cards-main` to `Cards`.
3. Move the folder to `<Toolbox>\mods\Cards`. Make sure `index.js`, `module.json`, and `config.json` are directly inside it.
4. Copy the files from `Cards\definitions` into `<Toolbox>\data\definitions`.
5. For protocol **381290**, merge the entries from `Cards\opcodes\protocol.381290.map` into Toolbox's active `data\opcodes\protocol.381290.map`:

   ```text
   C_CHANGE_CARD_PRESET 36656
   C_DEACTIVATE_CARD_COMBINE_LIST 20541
   C_ACTIVATE_CARD_COMBINE_LIST 30699
   ```

   Preserve the rest of the active map and keep one entry per packet name. For another protocol, use that server's matching opcode values.

6. Save your card decks in TERA's Cards UI, then open `Cards\config.json`. Set each category's `Preset` value to the corresponding in-game preset number. For example:

   ```json
   "argonPreset": 3,
   "magicaldevicePreset": 2
   ```

   This selects preset **3** for Argon and preset **2** for Magical Device. Use positive whole numbers matching your saved presets. Multiple categories can share the same preset.

7. Keep `defaultEffect` at `17` and `secondaryEffect` at `22`. The fishing settings are unused in this version.
8. Save `config.json`, start Toolbox, and launch TERA through it. Enter a supported dungeon to apply the configured cards.

Cards starts enabled. Use `/8 cards` to toggle it, `/8 cards on` to enable it, or `/8 cards off` to disable it.

Restart Toolbox after editing `config.json` to load your changes. Automatic updates are disabled; preserve your configuration when updating manually.
