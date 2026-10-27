[README.md](https://github.com/user-attachments/files/34051226/README.md)
# A-A-Store-Telescope-
A&amp;A Store — Pier Telescope &amp; Scenic Viewfinder
# A&A Store — Pier Telescope & Scenic Viewfinder

Professional coin-telescope script for FiveM. Players look through pier telescopes and scenic binoculars, pan across the view, zoom up to 30x, and switch between standard, night, and thermal vision.

Version **1.0.0**  
Author: **A&A Store**  
Support: [Discord](https://discord.gg/aastore)

The resource is sold through Tebex and delivered by the Cfx.re Keymaster. This page is the setup and configuration manual.

---

## Features

- Works with world telescope and binocular props, plus locations you add yourself.
- QBCore, Qbox, ESX, and standalone. Framework and target resources are detected automatically.
- ox_target and qb-target, with an E-key prompt when no target resource is running.
- Optional cash, bank, or item payment. The view opens only after the charge succeeds.
- One player per telescope. The lock clears when they leave, disconnect, die, or hit the session limit.
- Sea-facing start heading, 180° viewing arc by default, optional full 360° rotation.
- Mouse-wheel zoom from 1.0x to 30.0x, with distant scenery streamed in as you zoom.
- Standard, night vision, and thermal modes.
- On-screen heading, elevation, magnification, and control hints. Each one can be turned off.
- English and Turkish included. Add another language by copying a locale file.
- `config.lua` and `locales/*.json` stay editable after Asset Escrow encryption.

---

## Requirements

| Requirement | Notes |
| :--- | :--- |
| FiveM server | Artifact with `cerulean` and Lua 5.4. |
| OneSync | Required. Without it the server cannot check distance, and telescopes will not open. |
| Framework | Optional. `qbx_core`, `qb-core`, or `es_extended`. Standalone is free to use. |
| Target | Optional. `ox_target` or `qb-target`. |
| ox_lib | Optional. Used for notifications when it is started. |
| ox_inventory | Optional. Used automatically when payment type is `item`. |

Game build should be current enough for the telescope props in the base map (`prop_telescope_01`, `prop_telescope_02`, `prop_binocs_01`).

---

## Installation

1. Download the resource from the Cfx.re Keymaster after purchase.
2. Put the folder in your server `resources` directory.
3. Keep the folder name **`aa_piertelescope`**. The folder name is the resource name.
4. Start it **after** your framework, inventory, and target resources.
5. Add this to `server.cfg`:

```cfg
set onesync on

ensure ox_lib
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure aa_piertelescope
```

Use the framework and target lines that match your server. A QBCore server looks like this:

```cfg
set onesync on

ensure ox_lib
ensure qb-core
ensure ox_target
ensure aa_piertelescope
```

6. Restart the server, or run:

```cfg
refresh
ensure aa_piertelescope
```

7. Join the server and use a telescope on Del Perro Pier, or any other mapped telescope prop.

On startup the server console prints the A&A Store banner and the detected framework. If OneSync is off, it prints an error and tells you to set `onesync on`.

---

## Before you open the server

Payment is **on** in the default config.

- Price: **$100 cash** per view.
- If the player does not have enough cash, the script tries their **bank** account.
- A view can stay open for **20 minutes** (`MaxSessionSeconds = 1200`), then it closes.
- Standalone mode cannot remove money. If no framework is detected, the console says telescopes are free.

To make every telescope free:

```lua
Config.Monetization.Enabled = false
```

Change the price in `Config.Monetization.Amount`. Restart the resource after any config change.

---

## How a player uses it

With ox_target or qb-target, look at the telescope and choose **Look through Telescope**. If a price is set, the price is shown on that option. A telescope someone else is using shows a locked option instead.

With no target resource, or with `Config.InteractionType = 'key'`, walk up to the telescope and press **E**.

| Action | Control |
| :--- | :--- |
| Zoom | Mouse wheel |
| Look around | Mouse |
| Vision mode | `G` — Standard, Night Vision, Thermal |
| Leave | `E`, `Backspace`, `Esc`, or right mouse button |

The center of the lens stays clear. Heading, elevation, zoom, and the control hints sit on the edges. `Esc` leaves the telescope instead of opening the pause menu.

These states block use: dead, last stand, ragdoll, handcuffed (QBCore / Qbox), sitting in a vehicle, or an open ox_inventory.

---

## Configuration

Everything the server owner should change is in `config.lua`.

### General

| Option | Default | What it does |
| :--- | :--- | :--- |
| `Config.Debug` | `false` | Prints detection info. Also enables `/aatelescope_coords`. |
| `Config.Locale` | `'en'` | Locale file name inside `locales/`, without `.json`. |

`/aatelescope_coords` prints your current `vector4` in F8 and in a notification. Use it while standing where a new telescope should go. Turn `Config.Debug` back off on a live server.

### Framework and interaction

| Option | Default | What it does |
| :--- | :--- | :--- |
| `Config.Framework` | `'auto'` | `'auto'`, `'qbox'`, `'qbcore'`, `'esx'`, or `'standalone'`. |
| `Config.Target` | `'auto'` | `'auto'`, `'ox_target'`, `'qb-target'`, or `'none'`. |
| `Config.InteractionType` | `'target'` | `'target'` or `'key'`. |
| `Config.InteractionDistance` | `2.5` | How close the player must be, in meters. |
| `Config.RequireBehindTelescope` | `false` | `true` makes the player stand on the eyepiece side. |

`auto` checks Qbox first, then QBCore, then ESX.

`InteractionType = 'target'` uses ox_target or qb-target when one of them is running. If neither is running, the script falls back to **E**.  
`InteractionType = 'key'` always uses **E**, even if a target resource is started.

Keep `Config.Security.MaxDistanceTolerance` a little higher than `Config.InteractionDistance` (default 3.5 vs 2.5). The extra room covers latency between the client and the server.

### Map props and custom telescopes

| Option | Default | What it does |
| :--- | :--- | :--- |
| `Config.DetectMapProps` | `true` | Allows every prop listed in `Config.TelescopeModels`. |
| `Config.TelescopeModels` | three models | Models the target and the E prompt will accept. |
| `Config.StaticTelescopes` | two examples | Named spots. Used for labels, blips, and stable ids. |
| `Config.EnablePierBlip` | `false` | Draws a blip for each static telescope. |
| `Config.PierBlip` | sprite `744` | Blip style. `coords` is used only when the static list is empty. |

Default models:

- `prop_telescope_01`
- `prop_telescope_02`
- `prop_binocs_01`

You can add another model hash with backticks:

```lua
`prop_telescope_01`,
`your_custom_telescope`,
```

#### Static telescope fields

```lua
{
    id = 'del_perro_pier_01',          -- unique text id
    coords = vector4(x, y, z, heading),
    spawnProp = false,                 -- true creates the prop for you
    model = `prop_telescope_01`,
    label = 'Del Perro Pier Telescope', -- blip text when blips are on
    zone = false                       -- true adds a target zone with no prop
}
```

Rules:

- Every `id` must be unique. The server console warns you if two entries share an id.
- Keep telescopes at least **3 meters** apart. Closer than `StaticMatchRadius` (2 meters) they can be treated as the same telescope.
- `spawnProp = false` is right when the prop already exists in the map. The script does not spawn a second one.
- `spawnProp = true` creates a frozen local prop on each player. Use this for a custom spot that has no map prop.
- `zone = true` adds an ox_target / qb-target sphere even when there is no prop. Leave it off if a real prop is already there, or players will see two options.
- With `DetectMapProps = false`, zones are added for every static telescope automatically.

The two spots shipped in the config are examples. Stand on your pier, enable debug, run `/aatelescope_coords`, and paste that `vector4` into the list.

#### Blips

Set `Config.EnablePierBlip = true` and restart. Each static telescope gets a short-range blip using `label`.

If sprite `744` is invisible on your game build, change `Config.PierBlip.sprite`. Sprite `184` is a safe camera icon. `color`, `scale`, and `shortRange` work the same way as any other blip.

### Camera

| Option | Default | What it does |
| :--- | :--- | :--- |
| `AutoAimSea` | `true` | Opens facing the heading that points toward open water. |
| `DefaultPitch` | `-2.5` | Starting up/down angle. Negative looks down. |
| `BaseFov` | `50.0` | Field of view at 1.0x. |
| `MinFov` | `1.6667` | Smallest field of view, matched to 30x. |
| `MinZoom` / `MaxZoom` | `1.0` / `30.0` | Zoom limits. |
| `ZoomStep` | `0.75` | Zoom added or removed per wheel tick. |
| `DefaultZoom` | `1.0` | Zoom when the telescope opens. |
| `ZoomSmoothing` | `0.30` | How fast zoom catches the wheel. `1.0` is instant. |
| `MinPitch` / `MaxPitch` | `-42` / `60` | How far the player can look down and up. |
| `Allow360Rotation` | `false` | `false` is a 180° arc. `true` is a full turn. |
| `MaxYawOffset` | `90.0` | Half of the arc when 360° is off. 90 left + 90 right = 180. |
| `SensitivityX` / `SensitivityY` | `7.5` / `5.2` | Mouse speed. High zoom slows this down automatically. |
| `Smoothing` | `0.55` | Camera follow. `1.0` has no smoothing. |
| `AllowVisionModes` | `true` | Allows the `G` vision cycle. |
| `DisableDOF` | `true` | Keeps the zoomed view sharp. |
| `NearClipDistance` | `0.15` | Pulls the near plane in so the telescope mesh does not fill the lens. |

Full rotation:

```lua
Allow360Rotation = true,
```

A wider sea arc, still not a full circle:

```lua
Allow360Rotation = false,
MaxYawOffset = 120.0, -- 240 degrees total
```

### Player ped

| Option | Default | What it does |
| :--- | :--- | :--- |
| `PlayAnim` | `false` | Plays a binocular idle while they look. Other players can see it. |
| `AutoGenderAnim` | `true` | Picks the male or female version of that animation. |
| `AnimDict` / `AnimName` | binocular idle | Used when `AutoGenderAnim` is `false`. |
| `HidePedWhileViewing` | `true` | Hides the player on their own screen so their body does not clip the lens. Other players still see them. |
| `FreezePed` | `true` | Stops the player from walking away while looking. |
| `CameraOffsetZ` | `0.65` | Fallback eye height if the head bone cannot be read. |
| `CameraForwardOffset` | `0.65` | Moves the camera forward, out of the prop. |

The animation is off by default because a coin telescope reads cleaner when the player simply stands at it. Set `PlayAnim = true` if you want the binocular pose.

### Interface

| Option | Default | What it does |
| :--- | :--- | :--- |
| `ShowCompass` | `true` | Heading degrees and cardinal direction. |
| `ShowZoomGauge` | `true` | Magnification and field of view. |
| `ShowPitch` | `true` | Elevation angle. |
| `ShowKeyHints` | `true` | The control hints along the bottom. |
| `SoundEffects` | `true` | Mechanical click while zooming, plus a quiet loop while looking. |
| `SoundVolume` | `0.30` | `0.0` to `1.0`. `0` turns the audio off. |

The interface does not load fonts from the internet. It uses fonts that are already on the machine, so it still appears if the host has no connection to Google.

Colors live in `web/css/style.css`. Those files are not encrypted by escrow. A future update will overwrite edits in `web/`, so prefer the switches above when they cover what you need.

### Payment

```lua
Config.Monetization = {
    Enabled = true,
    Type = 'cash',               -- 'cash', 'bank', or 'item'
    Amount = 100,                -- dollars when Type is cash or bank
    AllowBankFallback = true,    -- cash can fall back to bank
    ItemName = 'telescope_token',
    ItemCount = 1,               -- removed when Type is 'item'
}
```

| Type | What is taken |
| :--- | :--- |
| `cash` | Cash. With `AllowBankFallback`, bank is used when cash is short. |
| `bank` | Bank only. |
| `item` | `ItemCount` of `ItemName`. Money settings are ignored. |

The player sees a success message after a real charge. If the charge fails, the telescope stays closed and nobody else is locked out of it.

Bank only:

```lua
Type = 'bank',
Amount = 100,
AllowBankFallback = false,
```

Item payment:

```lua
Enabled = true,
Type = 'item',
ItemName = 'telescope_token',
ItemCount = 1,
```

ox_inventory item, in `ox_inventory/data/items.lua`:

```lua
['telescope_token'] = {
    label = 'Telescope Token',
    weight = 0,
    stack = true,
    close = true,
    description = 'Pays for one look through a scenic telescope.'
},
```

QBCore item, in `qb-core/shared/items.lua`:

```lua
telescope_token = {
    name = 'telescope_token',
    label = 'Telescope Token',
    weight = 0,
    type = 'item',
    image = 'telescope_token.png',
    unique = false,
    useable = false,
    shouldClose = true,
    combinable = nil,
    description = 'Pays for one look through a scenic telescope.'
},
```

Qbox servers that use ox_inventory only need the ox_inventory entry. The script checks ox_inventory before the framework inventory.

There is no charge on standalone, because there is no account to remove it from. The console prints that when the resource starts.

### Security

| Option | Default | What it does |
| :--- | :--- | :--- |
| `RateLimitMs` | `1200` | Minimum milliseconds between use attempts from the same player. |
| `MaxDistanceTolerance` | `3.5` | Server-side distance check, in meters. |
| `StaticMatchRadius` | `2.0` | A map prop inside this radius uses the static telescope's id. |
| `MaxSessionSeconds` | `1200` | Closes the view after this many seconds. `0` disables the limit. |
| `StrictLocations` | `false` | `true` allows only telescopes in `Config.StaticTelescopes`. |

`StrictLocations = false` is what you want if every pier telescope on the map should work.

`StrictLocations = true` ignores map props that are not within `StaticMatchRadius` of a configured spot. Use the debug command so those coordinates actually sit on the props. If they are off by more than 2 meters, strict mode will reject the real telescope.

The server decides the telescope id from the player's position. A player has to be standing at the telescope. Disconnect, death, ragdoll, entering a vehicle, leaving the resource, or the session timer all release the lock.

---

## Languages

Included files:

- `locales/en.json`
- `locales/tr.json`

```lua
Config.Locale = 'tr'
```

To add a language, copy `locales/en.json` to `locales/fr.json`, translate the values, and set `Config.Locale = 'fr'`. Missing keys fall back to English. Keep every `%s` placeholder in the same order.

| Key | Shown when |
| :--- | :--- |
| `target_label` | Target option on a free telescope. |
| `not_enough_money` | The player cannot pay. `%s` is the price. |
| `not_enough_item` | The player does not have the item. First `%s` is the count, second is the item name. |
| `paid_success` | Cash or bank payment went through. |
| `item_consumed` | An item was removed. |
| `occupied_by_other` | Someone else is using it. |
| `session_expired` | The session timer closed the view. |
| `ui_*` | Text inside the lens. |

Restart the resource after editing a locale.

---

## Other resources

Client export:

```lua
local using = exports['aa_piertelescope']:IsUsingTelescope()
```

Close it from the server:

```lua
TriggerClientEvent('aastore_piertelescope:client:forceExit', playerId, 'external')
```

Close it on the same client:

```lua
TriggerEvent('aastore_piertelescope:client:forceExit', 'external')
```

The reason `'session'` is reserved for the built-in timer and shows the session-expired notification. Any other reason closes the view quietly.

---

## Files

| Path | Encrypted on Keymaster | Edit this? |
| :--- | :--- | :--- |
| `config.lua` | No | Yes. This is the settings file. |
| `locales/*.json` | No | Yes. |
| `web/` | No. NUI files are not escrowed. | Only for visual changes. |
| `client/`, `server/`, `bridge/`, `shared/` | Yes | No. Updates replace these. |

`fxmanifest.lua` already contains:

```lua
lua54 'yes'

escrow_ignore {
    'config.lua',
    'locales/*.json'
}
```

That is what keeps the config readable for buyers after you upload the zip to the asset portal.

---

## Troubleshooting

**The telescope never opens, and the player is told OneSync is required.**  
`server.cfg` needs `set onesync on` (or `set onesync legacy` on an older host). Restart the server after changing it.

**The target eye does not appear.**  
Confirm `ox_target` or `qb-target` is started before this resource. `Config.Target` should be `'auto'` unless you are forcing one. The prop model must be in `Config.TelescopeModels`. If there is no prop, set `spawnProp = true` or `zone = true` on that static entry.

**E does nothing, and there is no target option.**  
`Config.InteractionType` is `'target'` and a target resource is running, so the E prompt is correctly disabled. Use the target eye. Set `InteractionType = 'key'` if you want E instead.

**The player is close enough, but the server says they are too far.**  
Raise `Config.Security.MaxDistanceTolerance` slightly, for example to `4.5`. Do not raise `StaticMatchRadius` for this. That setting only decides which name a telescope uses.

**Strict mode rejects a telescope that is standing right there.**  
The static coordinates are more than 2 meters from the prop. Run `/aatelescope_coords` at the prop and update that entry, or set `StrictLocations = false`.

**Money is not taken.**  
Read the startup line `Server framework:`. If it says `standalone`, the framework started after this resource or the resource name does not match (`qbx_core`, `qb-core`, `es_extended`). Fix `server.cfg` order and set `Config.Framework` manually if you need to.  
If it says the core object was not found, the framework export was not ready. Update es_extended or qb-core, and start it first.

**The item is not taken.**  
`Type` must be `'item'`. `ItemName` must match the inventory item exactly. `ItemCount` is how many are removed, not `Amount`. With ox_inventory started, the ox_inventory item definition is the one that matters.

**Players are charged and the view still does not open.**  
The script locks the telescope, takes the payment, and opens the view only if the framework confirms the removal. If you still see this, check the server console for a framework error at the moment of use and send that line with your framework name.

**Two players used one telescope.**  
They were inside `StaticMatchRadius` of each other, so both props shared one id. Move one, or lower the radius only if you are sure the props are not that close.

**A telescope stays occupied after a crash.**  
Disconnect releases it. If the player is still online, it releases when `MaxSessionSeconds` runs out. Set that to `0` only if you accept a telescope staying locked until they disconnect.

**The view is blurry when zoomed.**  
Leave `DisableDOF = true`. Very high `MaxZoom` on a low graphics setting will still look soft because of the game's own LOD. The script moves the HD focus point toward whatever you are looking at.

**Night vision or thermal will not turn on.**  
`Config.Camera.AllowVisionModes` must be `true`. The key is `G` (`Config.Controls.CycleVision = 47`).

**The blip does not show.**  
`Config.EnablePierBlip` must be `true`, and there must be at least one static telescope. Try `sprite = 184` if `744` does not exist in your build.

**The UI text is still English.**  
`Config.Locale` must match the file name. `locales/tr.json` means `Config.Locale = 'tr'`. Restart the resource.

**Esc opens the pause menu for a moment.**  
The script blocks the pause control while the telescope is open and uses Esc to exit. A single-frame flash can still happen on some clients. Players can also leave with E, Backspace, or right mouse button.

---

## Publishing on Tebex

Use this when uploading the package. Buyers do not need this section to install the script.

1. Test in-game with OneSync on: target interaction, E-key fallback, cash payment, item payment, two players on one telescope, disconnect, and `G` vision modes.
2. Set `Config.Debug = false`.
3. Zip the **`aa_piertelescope`** folder itself. The zip root should be that folder, not a parent desktop folder.
4. Open [portal.cfx.re/assets](https://portal.cfx.re/assets), create an asset, and upload the zip. `lua54 'yes'` is already set, which escrow requires.
5. Create the Tebex package and attach that Cfx.re asset. Tebex then delivers the encrypted build through Keymaster.
6. `config.lua` and `locales/*.json` are in `escrow_ignore`, so buyers can edit them. The rest of the Lua is encrypted.
7. HTML, CSS, and JavaScript are not encrypted by escrow. Keep prices, permissions, and item names in `config.lua` and in the server script, which this resource already does.
8. If this documentation is going on a public GitHub repository, publish the manual there. Ship the script itself through Keymaster, not as a public code dump.
9. Put your Tebex package URL in the Support line at the top of this file when you have it. The Discord link is already live: https://discord.gg/aastore

Asset escrow accepts Lua, and also `.yft`, `.ydd`, and `.ydr` if you add models later. There are no streamed models in this package.

---

## Support

When you ask for help, include:

- Framework: QBCore, Qbox, ESX, or standalone
- Target: ox_target, qb-target, or none
- The `Server framework:` line from the server console
- Whether OneSync is on
- The `Config.Monetization` block
- A screenshot of the F8 client console if the client reports a script error

Discord: https://discord.gg/aastore

---

## License

This is a paid FiveM resource from A&A Store.

- The copy you receive from Tebex / Keymaster may be used on the server covered by that purchase.
- Do not resell, leak, reupload, or include this resource in a free or paid bundle.
- `config.lua`, locale files, and the interface style may be edited for your own server.
- Support covers the unmodified encrypted scripts with a normal framework start order. Edits inside encrypted files are replaced by the next update.
- Refunds follow the policy on the Tebex package.

© 2026 A&A Store. All rights reserved.
