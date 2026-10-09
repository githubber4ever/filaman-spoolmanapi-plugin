# Spoolman API Plugin for FilaMan

A FilaMan plugin that exposes a fully Spoolman-compatible REST API, allowing external tools like **Moonraker**, **OctoPrint** and others to use FilaMan as a drop-in replacement for Spoolman. Unlike Spoolman itself, this plugin includes IP-based access control — letting you restrict which devices are allowed to reach the API.

## Features

- Full Spoolman API v1 compatibility (all endpoints)
- Vendor, Filament and Spool CRUD operations
- Query filtering, sorting and pagination
- NFC/RFID tag linking for spools, lookup and scan relay for readers
- CSV and JSON export
- IP-based access control (a security layer missing in Spoolman)
- Admin UI for managing the IP allowlist

## Installation

Copy the `spoolmanapi/` folder into your FilaMan plugins directory and restart FilaMan.

## Configuration

### Moonraker

```ini
[spoolman]
server: http://<filaman-host>:8000/spoolman
```

### IP Access Control

By default, all IPs are allowed. To restrict access, open the plugin settings page in the FilaMan admin panel under **Spoolman API** and configure the IP allowlist.

The plugin accepts `extra.card_uids` as a comma-separated string and translates it to/from the
spool tag API without retaining it as a custom field. Native spool RFID tags take precedence.

## API

All Spoolman endpoints are available under:

```
http://<filaman-host>:8000/spoolman/api/v1/
```

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/info` | API info |
| GET | `/health` | Health check |
| GET/POST | `/vendor` | List / create vendors |
| GET/PATCH/DELETE | `/vendor/{id}` | Get / update / delete vendor |
| GET/POST | `/filament` | List / create filaments |
| GET/PATCH/DELETE | `/filament/{id}` | Get / update / delete filament |
| GET/POST | `/spool` | List / create spools |
| GET/PATCH/DELETE | `/spool/{id}` | Get / update / delete spool |
| POST | `/spool/{id}/tag` | Link an NFC/RFID tag to a spool |
| DELETE | `/spool/{id}/tag/{uid}` | Unlink a tag from a spool |
| PUT | `/spool/{id}/use` | Use filament from spool |
| PUT | `/spool/{id}/measure` | Measure spool weight |
| POST | `/tag/scan` | Resolve a scanned tag and relay it to WebSocket listeners |
| GET | `/tag/reader` | List recently active tag readers |
| GET | `/material` | List materials |
| GET | `/location` | List locations |
| PATCH | `/location/{name}` | Rename location |
| GET/POST | `/setting/{key}` | Get / set settings |
| GET | `/field/{entity_type}` | Read Spoolman extra-field definitions mapped to Filaman's native System Extra Fields |
| POST | `/field/{entity_type}/{key}` | Create or update a native Filaman System Extra Field from a Spoolman definition |
| DELETE | `/field/{entity_type}/{key}` | Delete a user-managed native Filaman System Extra Field |
| GET | `/export/spools` | Export spools (CSV/JSON) |
| GET | `/export/filaments` | Export filaments (CSV/JSON) |
| GET | `/export/vendors` | Export vendors (CSV/JSON) |
| POST | `/backup` | Create backup |

The field endpoints read and manage native Filaman System Extra Field definitions
through the Spoolman API, for filament and spool entities.
Numeric fields use Filaman's `decimal_places` setting to distinguish integer from
float types; dropdowns and multiselects map to Spoolman's choice type. Native formula
fields are omitted because Spoolman's field schema cannot represent computed fields.
Vendor fields are not included because Filaman System Extra Fields do not support
vendors. Deleting a field removes its definition but leaves existing custom-field
values on filaments or spools unchanged. Plugin-managed definitions cannot be deleted
through the Spoolman API. Supported Spoolman field types are `text`, `integer`,
`integer_range`, `float`, `float_range`, `datetime`, `boolean` and `choice`. Choice
fields require a non-empty `choices` list and an explicit `multi_choice` flag;
`default_value` must be a JSON-encoded value of the corresponding type. POST updates
an existing field of the same type, but cannot change its type or remove existing
choices.

## License

See the [FilaMan](https://github.com/Fire-Devils/FilaMan) project for license information.
