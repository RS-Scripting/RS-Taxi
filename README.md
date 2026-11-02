# RS-Taxi

RS-Taxi is a Paper plugin that lets players travel between named taxi stops. Stops are saved in a local SQLite database, and optional BlueMap integration displays taxi stops as markers on the server's web map.

## Features

- Create named taxi stops at a player's current location.
- Teleport to a stop by using its name.
- List available stops and click a stop name to travel there.
- Search stops by name or by BlueMap icon.
- Store stop data in SQLite, so stops persist across restarts.
- Optionally display taxi stops on BlueMap.
- Optionally require a configured 3×3 taxi platform before a stop can be created.
- Configure plugin messages through language files.
- Optionally restrict travel and stop listings to the player's current world.

## Requirements

- A compatible Paper server. This project currently targets Paper API `26.1.2`.
- Java 25 or newer, as required by this project's Maven configuration.
- BlueMap is optional. The plugin can run without it; map markers require BlueMap to be installed and enabled.

## Installation

1. Download or build the RS-Taxi JAR.
2. Place `RS-Taxi-<version>.jar` in your server's `plugins` directory.
3. Start or restart the server.
4. Edit `plugins/RS-Taxi/config.yml` as needed.
5. Restart the server after changing configuration.

On first startup, RS-Taxi creates its default configuration and English language file. Stop data is stored in the plugin's SQLite database.

## Commands

| Command | Description | Permission |
|---|---|---|
| `/cab <Stop Name>` | Teleport to the named stop. Stop names containing spaces can be supplied as multiple words. | `rs-cab.use` |
| `/cab list` | Display the available taxi stops. Click a listed stop to travel there. | `rs-cab.use` |
| `/cab search text <text>` | Search for stops whose names match the supplied text. | `rs-cab.use` |
| `/cab search icon <icon>` | Search for stops using the specified icon. | `rs-cab.use` |
| `/cab add <Stop Name> <Icon>` | Create a stop at your current location. | `rs-cab.add` |
| `/cab add "Stop Name With Spaces" <Icon>` | Create a stop whose name contains spaces. | `rs-cab.add` |
| `/cab remove <Stop Name>` | Remove a stop. | `rs-cab.delete` |

### Examples

```text
/cab Spawn
/cab list
/cab search text spawn
/cab search icon icons/marker.png
/cab add Spawn icons/marker.png
/cab add "Chuckies Home" icons/home.png
/cab remove "Chuckies Home"
```

Use an icon path that BlueMap can resolve from its web assets directory when BlueMap markers are enabled. The exact icon path depends on where the icon is stored under BlueMap's `web/assets` directory.

> **Implementation note:** In the source snapshot documented here, `/cab update` and `/cab edit` are declared in the command handler but their operation handlers are not implemented yet. They are therefore not listed as working commands.

## Permissions

| Permission | Default | Description |
|---|---|---|
| `rs-cab.use` | Everyone | Travel to stops, list stops, and search stops. |
| `rs-cab.add` | Operators | Create taxi stops. |
| `rs-cab.edit` | Operators | Intended for editing or updating stops; these operations are not implemented in the documented source snapshot. |
| `rs-cab.delete` | Operators | Delete taxi stops. |

## Configuration

The main configuration file is `plugins/RS-Taxi/config.yml`.

### Language

```yaml
language: "en_us"
```

Selects the language file to use. The included language file is `language/en_us.yml`. If a message is missing from a selected language file, RS-Taxi is designed to fall back to its English message.

### Cross-world travel

```yaml
allow-cross-world: true
```

- `true`: Stops in other worlds can be included in travel options.
- `false`: Travel lists and search results are limited to stops in the player's current world.

### Online map link

```yaml
online-map-address: ""
```

Set this to your server's online map address to display a clickable map link in the `/cab list` and search menus. Leave it blank to hide the link.

### Taxi platform

The platform requirement is disabled by default:

```yaml
platform:
  enabled: false
  target-block: "TARGET"
  protect-platforms: true
  sneak-rightclick-platform: false
  layout:
    north: "OAK_PLANKS"
    northeast: "OAK_PLANKS"
    east: "OAK_PLANKS"
    southeast: "OAK_PLANKS"
    south: "OAK_PLANKS"
    southwest: "OAK_PLANKS"
    west: "OAK_PLANKS"
    northwest: "OAK_PLANKS"
```

- `enabled`: Set to `true` to require a platform when creating a stop.
- `target-block`: Material required at the center of the platform. The default is `TARGET` (a Minecraft Target Block).
- `protect-platforms`: When enabled, platform blocks are protected from being broken by players.
- `sneak-rightclick-platform`: When enabled, sneaking and right-clicking a valid platform opens a clickable list of taxi stops. This requires `platform.enabled: true`.
- `layout`: Defines the required material at each of the eight positions surrounding the center block. Use exact Minecraft material names.

The default layout is a 3×3 platform: a Target Block in the center, surrounded by eight Oak Planks blocks.

## BlueMap integration

BlueMap integration is optional. If BlueMap is installed and enabled, RS-Taxi can create map markers for taxi stops. The icon is selected when the stop is created.

BlueMap marker icons are resolved from the BlueMap web assets directory (`web/assets`). Supported icon file extensions detected by the plugin include `.png`, `.jpg`, `.jpeg`, `.svg`, `.webp`, and `.gif`. Place custom icons in the assets directory and use the appropriate relative path as the stop's icon.

If BlueMap is not installed or enabled, RS-Taxi logs that map integration is disabled and the plugin's core travel functionality can still be used.

## Building from source

This project uses Maven. Open the project in IntelliJ IDEA or build it from a terminal with Maven installed and Java 25 configured:

```bash
mvn clean package
```

The JAR is generated in the `target` directory with a filename that includes the Maven project version, for example `RS-Taxi-0.1.0.jar`.

## Data and troubleshooting

- **A stop cannot be created:** Check that the sender has `rs-cab.add`. If platform requirements are enabled, stand on a platform matching the configured layout.
- **A stop is not visible on BlueMap:** Confirm BlueMap is installed and enabled, check the icon path, and verify the icon file exists under BlueMap's `web/assets` directory.
- **Stops from other worlds do not appear:** Check `allow-cross-world` in `config.yml`.
- **A command is denied:** Verify the relevant `rs-cab.*` permission.

## Source code

Repository: https://github.com/RS-Scripting/RS-Taxi
