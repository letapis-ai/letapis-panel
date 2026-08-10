# Installing letapis on a Mac

From a machine with nothing on it to a stack where every row in the panel is green. Work
through this in order — each step is what the next one expects to find.

Budget about an hour, most of it downloads.

## 1. What the machine needs

| | Requirement | Why |
|---|---|---|
| **OS** | macOS **13.0** or newer | the panel's minimum system version |
| **Chip** | Apple silicon | the models run on the GPU through Metal |
| **Memory** | **16 GB** or more | the two models together hold roughly 5.5 GB resident (see [models.md](models.md)); the engine and Qdrant want their share on top |
| **Disk** | about **4 GB** free | 1.2 GB of model weights, the engine, the panel, and room for the index to grow |
| **Docker Desktop** | installed | Qdrant runs as a container |
| **llama.cpp** | installed | both models run under its `llama-server` |
| **Homebrew** | installed | how llama.cpp gets onto the machine |

Install the three dependencies before going further.

Homebrew is not part of macOS — if `brew --version` does not answer, install it first with the
one-line command on [brew.sh](https://brew.sh), then follow the "Next steps" it prints at the end
(on Apple silicon it asks you to add `/opt/homebrew/bin` to your `PATH`, and `brew` does not work
until you do).

```bash
brew --version        # if this answers, Homebrew is ready
brew install llama.cpp
```

Docker Desktop is a download from [docker.com](https://www.docker.com/products/docker-desktop/);
open it once after installing so it can finish its own setup.

Check both answer:

```bash
llama-server --version
docker info
```

## 2. Install the panel

Download the `.dmg` from [Releases](https://github.com/letapis-ai/letapis-panel/releases) and
open it. The window has two icons: the app, and the `Applications` folder beside it — drag the
first onto the second. It is signed and notarised, so macOS opens it without argument.

Then eject the disk image; you can delete the `.dmg` afterwards.

Launch it. The panel lives in the **menu bar**, not the Dock — look for its icon at the top
right of the screen. There is no window until you ask for one: click the icon, or pick
**Show Panel** from its menu.

## 3. What the first launch created

Opening the panel for the first time writes a configuration directory and leaves it alone
forever after:

```
~/.config/letapis-app/
├── services.yaml            the list of services, in start order
└── services/
    ├── docker.yaml          one card per service…
    ├── qdrant.yaml
    ├── embedder.yaml
    ├── reranker.yaml
    ├── letapis-bin.yaml
    ├── embedder.sh          …and the two launch scripts
    └── reranker.sh
```

These are **yours** from this moment on. The panel never overwrites a file that exists, so
you can edit ports, paths and flags freely — see [services.md](services.md).

The panel window now shows five rows, and all of them but Docker are red. That is correct:
nothing else is installed yet.

## 4. Qdrant

Qdrant is the vector database the engine keeps its index in. You do not have to create the
container by hand — the Qdrant row's Start button does it the first time:

* the first Start runs `docker run` and creates a container named `letapis-qdrant`, publishing
  ports **6333** (REST) and **6334** (gRPC), with its storage on a named Docker volume called
  `letapis_qdrant_storage`;
* every later Start just starts that container again.

Make sure Docker Desktop is running (the Docker row is green), then press Start on the Qdrant
row. Within a few seconds it turns green. Confirm from a terminal if you like:

```bash
curl -s localhost:6333/healthz
```

**The volume is where your index lives.** Removing the container is harmless; removing the
volume `letapis_qdrant_storage` throws away everything the engine has indexed.

## 5. The two models

The engine needs an embedder and a reranker. Both are small, both run under the same
`llama-server` you installed in step 1, and both are ordinary files on disk.

Both are public downloads, about 610 MiB each:

```bash
mkdir -p ~/models

# embedder — MIT
curl -L -o ~/models/harrier-oss-v1-0.6b-Q8_0.gguf \
  https://huggingface.co/majentik/harrier-oss-v1-0.6b-GGUF-Q8_0/resolve/main/harrier-0.6b-Q8_0.gguf

# reranker — Apache-2.0
curl -L -o ~/models/Qwen3-Reranker-0.6B.Q8_0.gguf \
  https://huggingface.co/ggml-org/Qwen3-Reranker-0.6B-Q8_0-GGUF/resolve/main/qwen3-reranker-0.6b-q8_0.gguf
```

**Note that both commands rename the file.** On the hub they are `harrier-0.6b-Q8_0.gguf` and
`qwen3-reranker-0.6b-q8_0.gguf`; the service cards look for the names on the left. Download
them any other way and you must either use these names or point the cards at the real paths
([services.md](services.md) shows how). A file the card cannot find gives a readable
`FATAL: model weights not found` in the row's log — not a mystery, but not a working stack
either.

[models.md](models.md) explains the launch flags. The full specification — the exact models,
the checksums to verify against and the vector width — is what the engine is built against, and
lives with it: [models.md](https://github.com/letapis-ai/letapis-core/blob/main/docs/models.md).

With the files in place, press Start on the **Embedder** row, then on the **Reranker** row.
Each takes a few seconds to map its weights. They listen on `127.0.0.1:12436` and
`127.0.0.1:8086` — local only, not reachable from the network.

If a row stays red, open its log with the **Logs** button on the row: both scripts say plainly
what stopped them.

## 6-7. The engine, and logging this machine in

**These two steps live in the engine's own repository** — it is the licensed half, and its
documentation lives with it: [letapis-ai/letapis-core](https://github.com/letapis-ai/letapis-core).

Go there now and do both:

1. **[Put the engine in place](https://github.com/letapis-ai/letapis-core/blob/main/docs/install.md)** — three commands that take it
   out of the `.dmg` you were given and into the slot the panel starts it from. The panel also
   wrote the engine's configuration for you at first launch; that page says what is in it.
2. **[Activate the licence](https://github.com/letapis-ai/letapis-core/blob/main/docs/licence.md)** — the key button on the engine's
   row. An engine that has never been logged in refuses to start, so this comes before a green
   lamp, not after one.

**Then come back here for step 8.** That is the check that the whole stack talks to itself, and
it is the panel's half again.

## 8. Confirm the whole stack

At this point the panel should show five green rows:

| Row | Port | Confirm by hand |
|---|---|---|
| Docker daemon | — | `docker info` |
| Qdrant | 6333 | `curl -s localhost:6333/healthz` |
| Embedder | 12436 | `curl -s localhost:12436/v1/models` |
| Reranker | 8086 | `curl -s localhost:8086/health` |
| letapis-core | 3131 | `curl -s localhost:3131/api/v1/health` |

Turn on **Autostart** in the panel if you want the machine to bring this up by itself: the
switch arms two things at once — the panel relaunches when you log in, and it starts the whole
stack when it launches.

If a row is not green, [panel.md](panel.md) explains every state the panel can show and what
each one asks you to do.
