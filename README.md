# letapis panel

A menu-bar supervisor for the letapis service stack. It starts and stops the services, watches
their health, and shows one row per service — so the whole stack is one click away in the menu
bar instead of a terminal window per component.

What it does, in the order you meet it:

- **starts the stack** — everything at once at login, or a service at a time from its row;
- **watches health** — a lamp per service, and a red menu-bar icon the moment one goes down;
- **tells you about updates** — a mark appears when there is something to install, for the panel
  itself or for the engine, and goes out when there is not;
- **updates both** — the panel replaces itself, and asks the engine to replace itself, from one
  button.

## The panel does not include the engine

The engine (`letapis-core`) is licensed separately and is not part of this download — not as
source, not as a binary. On its own the panel starts and finds nothing to supervise: an empty
stack, or rows that stay red. That is expected, not a defect.

## Install

Download the signed bundle from [Releases](../../releases) and open it. The panel updates itself
from the same channel afterwards.

A panel on its own supervises nothing, so the installation is a stack: Docker and Qdrant, two
small models, and the engine. **[docs/install.md](docs/install.md)** walks the whole path on a
fresh Mac.

## Documentation

[docs/](docs/README.md) is the rest, and it is written for the person running this, not for the
person who wrote it:

| Page | What it answers |
|---|---|
| [install.md](docs/install.md) | the whole path on a fresh Mac, in order |
| [services.md](docs/services.md) | what each card is, what the lamps mean, what the buttons do |
| [models.md](docs/models.md) | the two models the stack needs, and where they come from |
| [panel.md](docs/panel.md) | the window itself: rows, the tray, the settings |
| [updating.md](docs/updating.md) | taking an update, rolling one back, removing the stack |

## License

Apache-2.0 — see [LICENSE](LICENSE). The engine is licensed separately and is not covered by it.
