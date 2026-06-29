# 🌟 ZMenus
*Developed by Zenologia*

Note: This is only public documenation. The code is private.

A modern, in-game GUI menu builder for Paper servers — build clickable menus for shops, warps, kits, rules, info panels, and more without ever touching a YAML file by hand.  
Designed for performance, flexibility, and admin control, every menu is created and edited live in-game, backed by per-item commands, permissions, fallback items, and cooldowns.

## What it does (and doesn't) do

- ✅ Builds menus entirely in-game through a live GUI editor — no manual YAML required.
- ✅ Supports 10 container types (chest, hopper, furnace, brewing stand, and more) with multi-page chest menus.
- ✅ Runs per-item commands as the player, as the player with temporary OP, or as the console.
- ✅ Gates items and whole menus behind permissions, with per-item and global fallback items.
- ✅ Adds per-item cooldowns and one-time-use items, backed by YAML, SQLite, or MySQL.
- ✅ Renders all text with MiniMessage and resolves PlaceholderAPI placeholders per viewer.
- ✅ Registers a shortcut command for every menu (for example `/shop`).
- ❌ Not an economy or shop engine; it runs commands, so pair it with your economy and kit plugins.
- ❌ Does not register its own PlaceholderAPI placeholders — it consumes the placeholders other plugins provide.
- ❌ Not a chat, scoreboard, or hologram plugin.

---

## ⚙️ FEATURES
- 🛠️ Live in-game editor for creating, editing, renaming, and deleting menus
- 📦 10 container types, with 1–6 row chest menus and multi-page navigation
- 🖱️ Left-click to place a held item, right-click to delete, middle-click to clone
- ⚡ Per-item commands with `[player]`, `[playerop]`, and `[console]` execution modes
- 🔒 Per-item and per-menu permissions with automatic default permission nodes
- 🪧 Per-item fallback items, plus a configurable server-wide global fallback item
- ⏱️ Cooldowns (`10s`, `5m`, `1h`, `2d`, `1w`) and one-time-use items
- 🗄️ Cooldown storage in YAML, SQLite, or MySQL with automatic migration between backends
- 💬 MiniMessage and legacy `&` color codes, plus PlaceholderAPI support, in titles, names, lore, and commands
- 🔁 Hot-reload watcher that picks up menu file changes automatically
- 🧰 Per-menu shortcut commands with tab-completion across the admin command suite
- 🎨 Fully configurable editor GUIs, messages, and command names
- 🧩 `minecraft:item_model` support for custom item model definitions

---

## Requirements
- Paper 1.21.11+
- Java 21+
- PlaceholderAPI (required — ZMenus will not enable without it)
- PacketEvents (required — ZMenus will not enable without it)
- One cooldown storage backend:
  - YAML (default for new installs)
  - SQLite
  - MySQL or MariaDB

---

## Optional dependencies
- **LuckPerms** — automatically migrates a menu's permission nodes when the menu is renamed
- **Geyser / Floodgate** — for Bedrock clients; `item_model` rendering depends on the operator's Geyser mappings. ZMenus detects Bedrock support through the Geyser plugin **or** Floodgate, so standalone Geyser setups (which run Floodgate on the backend) are recognized without the Geyser plugin installed.

---

## Installation

1. Install **PlaceholderAPI** and **PacketEvents** if they are not already present. ZMenus depends on both and will not start without them.
2. Drop `ZMenus.jar` into your server's `plugins/` folder.
3. Start the server once so the plugin can generate its files:
   - `config.yml`
   - `messages.yml`
   - `editor-guis.yml`
   - `cooldowns.yml`
   - `menus/` (folder for your menu files)
4. (Optional) Edit `config.yml`, `messages.yml`, or `editor-guis.yml` to customize behavior, text, and editor appearance.
5. Create your first menu in-game:
```text
/zmenus create shop
```
6. Reload after editing config files:
```text
/zmenus reload
```

Important notes:
- Menus are stored as individual files in `plugins/ZMenus/menus/`. Each `.yml` file is one menu.
- With `behavior.auto-reload-menus: true` (default), changes to files in the `menus/` folder are detected and reloaded automatically. In-game edits save back to those files.
- Cooldown data migrates automatically when you change `storage.cooldowns.type` and run `/zmenus reload`. ZMenus imports existing entries from the previous backend into the new one.
- Newly created menus receive a default open permission of `zmenus.<menu-name>`, which defaults to operators only until you grant it (see [Permissions](#permissions)).

---

## Quick start

The fastest path from a fresh install to a working menu:

1. `/zmenus create shop` — choose a container type (and rows for chests) from the selector, then land in the page editor.
2. **Place items** — drag or left-click an item from your inventory into a slot in the page editor.
3. **Configure an item** — left-click an item already in the editor to open the item editor, where you can set its name, lore, commands, permission, cooldown, and more.
4. **Set the menu permission** — in the settings menu, use *Edit Menu Permission*. Leave it blank to make the menu public, or set a node your players already have.
5. **Open it** — run `/shop` (the auto-generated shortcut) or `/zmenus open shop`.

---

## Commands

The base command defaults to `/zmenus` and can be renamed in `config.yml` (`commands.base-command`).

| Command | Description | Permission |
|---|---:|---|
| `/zmenus help` | List available subcommands | Anyone |
| `/zmenus create <menu> [type] [rows]` | Create a menu and open the editor | OP or `zmenus.create` * |
| `/zmenus edit <menu>` | Open an existing menu in the editor | OP or `zmenus.create` * |
| `/zmenus delete <menu>` | Delete a menu (chat confirmation) | `zmenus.delete` |
| `/zmenus rename <menu>` | Rename a menu (chat input) | `zmenus.rename` |
| `/zmenus open <menu> [player]` | Open a menu for yourself or another player | Menu permission (self) / OP or `zmenus.open.others` (other) |
| `/zmenus list` | List all loaded menus | Anyone |
| `/zmenus reload` | Reload configs and all menus | `zmenus.reload` |
| `/zmenus clearcooldown <player> [menu] [item]` | Clear stored cooldowns for a player | `zmenus.clearcooldown` |
| `/zmenus clearcooldown * <menu> <item>` | Clear one item's cooldown for every player | `zmenus.clearcooldown` |
| `/<menu-command>` | Per-menu shortcut that opens that menu | Menu permission |

\* Creation and editing access is configurable — see [Permissions](#permissions). Tab-completion is included for subcommands, menu names, container types, online players, and stored cooldown slots.

### About `clearcooldown`

- `/zmenus clearcooldown <player>` — clears every cooldown for that player.
- `/zmenus clearcooldown <player> <menu>` — clears all cooldowns for that player within a menu.
- `/zmenus clearcooldown <player> <menu> <item>` — clears one item slot for that player.
- `/zmenus clearcooldown * <menu> <item>` — clears that one item slot for all players.

The `<item>` argument is the numeric slot of the item in the menu.

---

## Permissions

ZMenus permission nodes are not pre-declared, so they default to **operators only**. Grant them with your permissions plugin to delegate access to non-op staff.

| Node | Default | Effect |
|---|---:|---|
| `zmenus.create` | OP | Create and edit menus (applies when `require-op-for-menu-creation: false`) |
| `zmenus.delete` | OP | Delete menus |
| `zmenus.rename` | OP | Rename menus |
| `zmenus.reload` | OP | Reload configs and menus |
| `zmenus.clearcooldown` | OP | Clear stored cooldowns |
| `zmenus.open.others` | OP | Open menus for other players (applies when `require-operator: false`) |
| `zmenus.<menu>` | OP | Open the menu with that auto-generated default permission |

### Menu access permissions

When you create a menu named `shop`, it is given a default open permission of `zmenus.shop`. Because that node is undeclared, **only operators can open the menu until you grant it.** You have two options:

- Grant `zmenus.shop` to the groups that should access the menu, or
- Open the menu in the editor and use *Edit Menu Permission*, then send a blank message to **clear** the permission and make the menu public.

### Configurable gating

- `permissions.require-op-for-menu-creation` (default `true`) — when `true`, only operators can create or edit menus. Set it to `false` to instead require `permissions.menu-creation-permission` (default `zmenus.create`). Leave that value blank to allow anyone.
- `commands.require-operator` (default `true`) — when `true`, opening a menu for *another* player requires OP. Set it to `false` to require `zmenus.open.others` instead.

---

## The in-game editor

Everything about a menu is editable in-game. Open the editor with `/zmenus create <menu>` (new) or `/zmenus edit <menu>` (existing). A menu can only be edited by one person at a time.

### Settings menu

The hub for a menu. From here you can:

- **Edit Page** — open the live page editor for the current page
- **Previous / Next Page**, **Add Page**, **Delete Current Page** — manage pages (chest menus only)
- **Edit Title** — change the menu title via chat (MiniMessage supported)
- **Rename Menu** — rename the menu via chat
- **Edit Menu Permission** — set or clear the open permission
- **Delete Menu** — delete the menu after confirmation

### Page editor

A live view of the menu where you arrange items:

- **Left-click an empty slot** while holding an item — places that item in the slot
- **Left-click an existing item** — opens the item editor
- **Right-click an existing item** — deletes it (after confirmation)
- **Middle-click an existing item** — clones it onto your cursor (if `editing.allow-middle-click-clone` is enabled)

### Item editor

Configure a single item:

- **Use Main-Hand Item** — copy your held item's material, name, and lore into the slot
- **Edit Name / Edit Lore** — set the display name and lore via chat (use `|` to separate lore lines)
- **Edit Commands** — choose an execution mode, then enter commands separated by `;`
- **Edit Permission** — restrict who sees the item (send blank to clear)
- **Cycle Item Type** — switch between `ACTION`, navigation, and `FILLER`
- **Edit Cooldown Duration** — set a cooldown like `10s`, `5m`, `1h`, `2d`, `1w`
- **Toggle Hide Attributes** — hide vanilla item attributes
- **Toggle One-Time Use** — make the item usable only once per player
- **Edit Cooldown Message** — set a custom on-cooldown message
- **Clear Cooldown** — remove the cooldown block from the item
- **Edit Item Model Data** — set a `namespace:variant` item model
- **Edit Fallback** — open the fallback item editor (requires an item permission first)

### Fallback item editor

When an item has a permission, you can define a fallback item shown to players who lack it. The fallback always hides attributes and supports its own name, lore, commands, cooldown, and item model.

> **Chat prompts:** name, lore, command, permission, and other text fields are entered via chat. Type `cancel` to abort, and send a blank message to clear fields that support clearing. Prompts time out after `behavior.chat-input-timeout-seconds` (default 30 seconds).

---

## Container types

| Type | Slots | Multi-page | Notes |
|---|---|---|---|
| `CHEST` | 9–54 (1–6 rows) | ✅ | The only type that supports rows and multiple pages |
| `HOPPER` | 5 | ❌ | |
| `DISPENSER` | 9 | ❌ | 3×3 grid |
| `BREWING_STAND` | 5 | ❌ | |
| `FURNACE` | 3 | ❌ | |
| `SMITHING_TABLE` | 4 | ❌ | |
| `GRINDSTONE` | 3 | ❌ | |
| `CARTOGRAPHY_TABLE` | 3 | ❌ | |
| `LOOM` | 4 | ❌ | |
| `STONECUTTER` | 2 | ❌ | |

---

## Item types

| Type | Purpose |
|---|---|
| `ACTION` | Default. Runs its commands when clicked |
| `PAGE_NEXT` | Navigates to the next page |
| `PAGE_PREVIOUS` | Navigates to the previous page |
| `PAGE_FIRST` | Jumps to the first page |
| `PAGE_LAST` | Jumps to the last page |
| `FILLER` | Decorative; ignores clicks and any commands |

Navigation item types only work in `CHEST` menus. They hide automatically when there is no page to navigate to (for example, *Next Page* on the last page).

---

## Command execution modes

Each item command carries an execution mode. In the editor you pick the mode from a selector and enter only the command body; ZMenus stores the prefix for you.

| Prefix | Runs as | Use for |
|---|---|---|
| *(none)* or `[player]` | The clicking player | Commands the player can already run |
| `[playerop]` | The player, with temporary elevated permission | Commands the player normally cannot run |
| `[console]` | The server console | Admin, economy, and give commands |

Commands run in the order listed. Enter each command the way you would type it in chat, just without the leading slash. If a command fails, the player receives the configurable `commands.failed-execution` message.

---

## Cooldowns and one-time use

Items can be limited in two mutually exclusive ways:

- **Cooldown duration** — the item cannot be used again until the timer expires. Durations use `s`, `m`, `h`, `d`, and `w` suffixes (`30s`, `10m`, `1h`, `2d`, `1w`).
- **One-time use** — the item can be used only once per player, permanently (until cleared by an admin).

Cooldowns apply only after a command actually executes. The remaining time is available as the `%time%` token in the item's name, lore, and cooldown message. Stored cooldowns can be cleared with `/zmenus clearcooldown` (see [Commands](#commands)).

---

## Formatting and placeholders

All menu text — titles, item names, and lore — is rendered with **MiniMessage**, so you can use tags like `<red>`, `<gold>`, `<bold>`, and `<gradient:#ff0000:#00ff00>`.

**Legacy `&` color codes also work** in titles, item names, and lore, and can be mixed with MiniMessage in the same string. The `&0`–`&f` colors and `&k`–`&o` / `&r` format codes are translated to their MiniMessage equivalents before rendering (for example `&cSword` is the same as `<red>Sword`). Unlike vanilla, a legacy color code does not reset earlier formatting — a tag stays active until another tag overrides it, exactly like MiniMessage. Hex codes (`&#rrggbb`) are not translated; use MiniMessage `<#rrggbb>` for custom colors. (Chat messages in `messages.yml` remain MiniMessage-only.)

Two kinds of replacements are resolved per viewer:

- **PlaceholderAPI placeholders** — any `%expansion_value%` placeholder from an installed expansion is resolved in titles, names, lore, and commands.
- **`%time%`** — replaced with the remaining cooldown time (for example `1h 5m 30s`) on cooldown items; empty otherwise.

In addition, chest menu **titles** support `%page%`, which is replaced with the current page number on multi-page menus.

---

## Configuration (`config.yml`)

```yaml
global-fallback-item:
  material: BARRIER
  name: "<red>No Access"
  lore:
    - "<gray>You do not have permission"
    - "<gray>to use this item."

editing:
  allow-middle-click-clone: true
  default-container-type: CHEST
  default-rows: 3

commands:
  # The base command name. All commands are invoked via /<base-command>.
  base-command: "zmenus"
  # When true, opening a menu for another player requires OP instead of
  # the zmenus.open.others permission.
  require-operator: true

permissions:
  # 1. require-op-for-menu-creation: true  -> OP only
  # 2. menu-creation-permission non-empty  -> requires that permission
  # 3. menu-creation-permission blank      -> anyone can create
  require-op-for-menu-creation: true
  menu-creation-permission: "zmenus.create"

behavior:
  auto-reload-menus: true
  chat-input-timeout-seconds: 30
  suppress-stonecutter-recipe-grid: true

storage:
  cooldowns:
    # Supported values: YAML, SQLITE, MYSQL
    type: YAML
    sqlite:
      # Relative paths are resolved inside the plugin data folder.
      file: "cooldowns.db"
    mysql:
      host: "127.0.0.1"
      port: 3306
      database: "zmenus"
      username: "root"
      password: ""
      use-ssl: false
      allow-public-key-retrieval: true
      # Optional extra JDBC parameters appended to the connection URL.
      connection-parameters: ""
      table-prefix: "zmenus_"
```

### Notes
- `global-fallback-item` is shown when a player lacks an item's permission and that item has no per-item fallback. Remove the section, or use a blank/invalid material, to disable it.
- `editing.default-container-type` and `editing.default-rows` pre-select the highlighted option in the create selectors.
- `behavior.chat-input-timeout-seconds` has a minimum of 5 seconds.
- `behavior.suppress-stonecutter-recipe-grid` hides the recipe grid a stonecutter draws in the centre of its window for STONECUTTER menus, so the items you place are not joined by clickable-looking recipe outputs. It applies only to ZMenus stonecutter menus; real stonecutters are unaffected.
- Changing `storage.cooldowns.type` and running `/zmenus reload` migrates existing cooldown data into the new backend automatically.

---

## Configuration (`messages.yml`)

Every player-, admin-, and console-facing string ZMenus produces is configurable here, grouped into categories. Missing keys fall back to built-in defaults, so the plugin keeps working even if a key is removed, and new keys added in future versions are picked up automatically.

| Category | Audience | Rendering | Covers |
|---|---|---|---|
| `menu` | players | MiniMessage | reload/delete notices, edit locks, chat-prompt cancel/timeout |
| `cooldowns` | players / admins | MiniMessage | on-cooldown and "already used" messages, `clearcooldown` confirmations |
| `general` | players / admins | MiniMessage | `error` wrapper applied to surfaced error text |
| `commands` | command senders | MiniMessage | command feedback, `usage:` lines, the `help:` listing |
| `editor` | admins | MiniMessage | every editor chat `prompt:`, GUI `status:` words, inline `error:` messages |
| `validation` | players / console | plain text | menu-name rejection reasons (shown wrapped in `general.error`, or logged plainly) |
| `log` | console | plain text | every server-log diagnostic (`dependency`, `luckperms`, `menu`, `command`, `runtime`, `cooldown`, `config`, `gui`) |

Player- and admin-facing categories use **MiniMessage**, so color/format tags like `<red>` and `<gold>` work. The `validation` and `log` categories are written to the server log as **plain text** — do not add MiniMessage tags there.

Representative excerpt (see the generated file for the full set):

```yaml
general:
  error: "<red>%error%"

commands:
  menu-not-found: "<red>That menu does not exist."
  menu-created: "<green>Created menu '%menu%'."
  menu-list: "<green>Loaded menus: <white>%menus%"
  usage:
    create: "<red>Usage: /%command% create <menu> [type] [rows]"
  help:
    create: "<gold>/%command% create <menu> [type] [rows]"

editor:
  prompt:
    item-commands: "<yellow>Enter commands separated by ;. The selected %mode% mode will be applied to each command. Enter command bodies without a leading slash (for example smite, not /smite), and do not type [player], [playerop], or [console]. Send a blank message to clear commands. Type 'cancel' to abort."
  status:
    one-time-enabled: "<green>enabled"
  error:
    hold-item: "<red>Hold an item in your main hand first."

validation:
  name-blank: "Menu name cannot be blank."

log:
  dependency:
    placeholderapi-missing: "PlaceholderAPI is required. ZMenus will not start without it."
  menu:
    load-failed: "Failed to load menu file '%file%': %error%"
```

### Message tokens

Each message accepts only the tokens relevant to it; the default text shows which apply. Common tokens:

- `%time%` — remaining cooldown time
- `%player%` — target player name
- `%menu%` — menu name
- `%slot%` — item slot number
- `%count%` — number of affected players
- `%editor%` — the player currently editing a menu
- `%name%` — the conflicting menu name on rename
- `%command%` — the active base command label
- `%error%` — underlying reason text (in `general.error` and many `log` lines)
- `%menus%` — comma-separated list of loaded menus
- `%mode%` — the selected command execution mode in the command prompt
- `%old%` / `%new%` — previous and new menu name on rename
- `%file%`, `%type%`, `%material%`, `%fallback%`, `%path%`, `%backend%`, `%uuid%`, `%value%` — context tokens used by `log` and `validation` lines

---

## Configuration (`editor-guis.yml`)

Every editor screen — the type selector, row selector, settings menu, page editor titles, item editor, fallback editor, and command-mode selector — is configurable. For each button you can change the slot, material, name, and lore; for each screen you can change the title and (where applicable) the inventory size.

When ZMenus updates, any newly bundled editor-GUI keys are merged into your existing `editor-guis.yml` without overwriting your customizations.

GUI titles and buttons support these placeholders depending on the screen: `%menu%`, `%menu_title%`, `%page%`, `%page_count%`, `%slot%`, `%container_type%`, `%item_type%`, `%one_time_status%`, `%hide_attributes_status%`, `%editor_mode%`, and (in the create selectors) `%type%` and `%rows%`.

---

## Menu file format (`menus/<name>.yml`)

Menus are normally built in-game, but each one is stored as a plain YAML file you can inspect or edit by hand. Saved files are reloaded automatically.

```yaml
menu:
  name: shop
  title: "<gold>Server Shop"
  type: CHEST
  rows: 3
  command: shop
  permission: zmenus.shop
pages:
  '1':
    items:
      '11':
        material: DIAMOND_SWORD
        name: "<aqua>Buy a Sword"
        lore:
          - "<gray>Costs <green>100 coins"
          - "<gray>Cooldown: <yellow>%time%"
        commands:
          - "[console] eco take %player_name% 100"
          - "[player] kit sword"
        permission: zmenus.shop.sword
        hide-attributes: true
        item_model_data: "minecraft:netherite_sword"
        type: ACTION
        cooldown:
          duration: 1h
          one-time-use: false
          message: "<red>Wait %time% before buying again."
        fallback:
          material: BARRIER
          name: "<red>Locked"
          lore:
            - "<gray>You lack permission."
      '15':
        material: ARROW
        name: "<yellow>Next Page"
        type: PAGE_NEXT
```

### Field reference

| Field | Scope | Description |
|---|---|---|
| `menu.name` | menu | Unique menu identifier (also the file name) |
| `menu.title` | menu | MiniMessage title; supports `%page%` and placeholders |
| `menu.type` | menu | One of the [container types](#container-types) |
| `menu.rows` | menu | 1–6, chest menus only |
| `menu.command` | menu | The shortcut command label |
| `menu.permission` | menu | Open permission (omit/blank for public) |
| `material` | item | The item material (required) |
| `name` / `lore` | item | Display name and lore (MiniMessage and legacy `&` codes) |
| `commands` | item | Click commands with optional execution-mode prefix |
| `permission` | item | Hides the item from players who lack it |
| `hide-attributes` | item | Hide vanilla attributes (default `true`) |
| `item_model_data` | item | `namespace:variant` item model |
| `type` | item | One of the [item types](#item-types) |
| `cooldown.duration` | item | A duration like `10s`, `5m`, `1h`, `2d`, `1w` |
| `cooldown.one-time-use` | item | Single use per player (mutually exclusive with duration) |
| `cooldown.message` | item | Custom on-cooldown message |
| `fallback` | item | Item shown when permission is missing (requires `permission`) |

Slot keys under `items` are the numeric inventory slots. Menu names must be filesystem-safe (no whitespace or reserved characters).

---

## Cooldown storage

Cooldown and one-time-use data is persisted in one of three backends, selected by `storage.cooldowns.type`:

| Backend | Where data lives | Best for |
|---|---|---|
| `YAML` | `plugins/ZMenus/cooldowns.yml` | Single-server setups (default) |
| `SQLITE` | `plugins/ZMenus/cooldowns.db` | Single server with a file database |
| `MYSQL` | A MySQL/MariaDB table (`<prefix>cooldowns`) | Networks sharing data across servers |

All connection and pool-relevant values for MySQL — host, port, database, credentials, SSL, public-key retrieval, extra JDBC parameters, and the table prefix — are exposed in `config.yml`. When you switch backends and run `/zmenus reload`, ZMenus imports existing entries from the previous backend into the new one.

---

## Common scenarios

**Make a menu public to everyone:**  
Open the menu in the editor, choose *Edit Menu Permission*, and send a blank message to clear it.

**Give a kit only to players with permission, and show a "locked" item to everyone else:**  
Set an item permission, then use *Edit Fallback* to design the locked item shown to players without it.

**Run an admin-only economy command from a player's click:**  
Edit the item's commands, pick the `console` mode, and enter the command body (for example `eco take %player_name% 100`).

**Let players claim a reward only once:**  
On the reward item, toggle *One-Time Use*.

**Share cooldowns across a network:**  
Set `storage.cooldowns.type: MYSQL`, fill in the MySQL section, and run `/zmenus reload` on each server.

---

## Troubleshooting

- **ZMenus does not enable:** PlaceholderAPI and PacketEvents are required. Install them and restart.
- **Players can't open a menu they should see:** the menu's default permission is `zmenus.<menu-name>`, which only operators have by default. Grant it, or clear the menu permission in the editor.
- **A placeholder shows as raw text:** make sure the providing PlaceholderAPI expansion is installed and the placeholder is correct.
- **Menu edits aren't appearing:** ensure `behavior.auto-reload-menus` is `true`, or run `/zmenus reload` after manual file edits.
- **A console/playerop command does nothing:** confirm the command body is valid and that the chosen execution mode has the permission to run it.
- **`item_model` isn't rendering for Bedrock players:** Bedrock model rendering depends on Geyser mappings configured by the server operator.
- **Startup warns that Geyser/Floodgate was not found:** this only appears when neither the Geyser plugin nor Floodgate is detected. With standalone Geyser, install Floodgate on the backend server and the warning is suppressed — Geyser Standalone runs as a separate process and cannot be detected directly.

---

## Uninstall

1. Remove `ZMenus.jar` from `plugins/`.
2. Delete `plugins/ZMenus/` if you no longer need the menus, configs, or cooldown data.
3. Restart the server.

---

## 🧑‍💻 Author

**ZMenus** is developed and maintained by **Zenologia**.

- GitHub: [@Zenologia](https://github.com/Zenologia)

If ZMenus saves you time on your server, a ⭐ on the repository is the best way to say thanks.
