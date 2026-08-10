# letapis panel — documentation

The panel is a menu-bar supervisor. It starts and stops the services a letapis installation is
made of, watches their health, and shows one row per service. It does not do the work itself:
the searching, indexing and answering all happen in the **engine**, which is licensed
separately and does not travel with this repository.

So a working installation is five things, and the panel is only the first:

| # | Piece | What it is | Comes from |
|---|---|---|---|
| 1 | **Panel** | this app | [Releases](https://github.com/letapis-ai/letapis-panel/releases) |
| 2 | **Docker + Qdrant** | the vector database the engine stores its index in | Docker Hub |
| 3 | **Embedder** | a small model turning text into vectors | model weights + `llama-server` |
| 4 | **Reranker** | a small model re-ordering search results | model weights + `llama-server` |
| 5 | **Engine** | `letapis-core` — the licensed binary | your licence |

The panel starts them in that order, bottom-up, and will happily run with pieces missing —
those rows simply stay red. That is the normal state of a fresh install, not a defect.

## Where to start

**[install.md](install.md)** — the whole path, in order, from a Mac with nothing on it to a
stack where every lamp is green. Read this first; everything else is reference you come back to.

## Reference

| Document | Answers |
|---|---|
| [models.md](models.md) | the launch flags, replacing a model, and what to do when one will not start |
| [services.md](services.md) | `services.yaml` and the service cards — how to change a port, add a service, point at different weights |
| [panel.md](panel.md) | what the lamps and the notes under each row mean, and what to do about them |
| [licence.md](https://github.com/letapis-ai/letapis-core/blob/main/docs/licence.md) | activating the engine, and reading an activation refusal — in the engine's repository |
| [updating.md](updating.md) | updating the panel, updating the engine, and uninstalling |

## Conventions in these pages

Commands are for **macOS 13 or newer on Apple silicon**. Paths starting with `~` are inside
your home directory. Where a page says a file is *seeded*, it means the panel writes it once,
on first start, and never overwrites it afterwards — your edits are safe.
