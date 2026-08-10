# The two models

letapis uses two small models, and it needs both. They do different jobs at different moments
of a single search:

* the **embedder** turns text into a vector. Every file the engine indexes goes through it
  once, and so does every query you type.
* the **reranker** takes the handful of candidates the vector search returned and re-orders
  them by actually reading each one against the query. It is what makes the top result the
  top result.

Both run under `llama-server`, which comes with llama.cpp (`brew install llama.cpp`). One
runtime for both is deliberate: it is the same binary on every Mac, it needs no Python
environment, and it uses the GPU through Metal without any further setup.

**The specification lives with the engine.** Which models, which checksums, which vector width —
that is what the engine is built against, and it is in the engine's repository:
[models.md](https://github.com/letapis-ai/letapis-core/blob/main/docs/models.md). Downloading them is
[step 5 of the install guide](install.md#5-the-two-models); this page is what the panel does with
them once they are on disk.

## The flags that are not optional

Both launch scripts pass one flag that is the server's **mode**, not a tuning choice, and
neither is exposed as configuration:

* the embedder runs with `--embedding`. Without it the server still answers `/v1/models`, and
  `/v1/embeddings` simply does not exist — so the health lamp goes green on a server that
  cannot do the one thing it is there for.
* the reranker runs with `--reranking --pooling rank`. Drop either and you get the same trap:
  a green lamp on a server that is no longer a reranker.

If you replace a model, keep the flags. The scripts are `~/.config/letapis-app/services/embedder.sh`
and `reranker.sh`, and they are yours to edit — but that pair of lines is load-bearing.

## Replacing a model

The cards do not care which file they point at, as long as the runtime can load it. To try a
different quantisation — a smaller `Q4_K_M`, say, to save a couple of hundred megabytes — put
the file in `~/models/` and change the `model:` line in the service's card. The panel's pencil
button on the row opens exactly that file.

Two things to check before you do:

1. **Dimensions**, for the embedder — see above.
2. **That it is the right kind of model.** A reranker and an embedder are not interchangeable,
   and neither will tell you it is the wrong one; you will simply get poor results from a green
   stack.

## If a model will not start

Both scripts fail loudly rather than dying into the log, and there are exactly three things
they say:

| In the log | Means |
|---|---|
| `FATAL: 'llama-server' not found in PATH` | llama.cpp is not installed, or is somewhere `PATH` does not reach. Install it, or add a `bin:` line to the card with the full path |
| `FATAL: model weights not found: <path>` | the file is not there, or is named differently |
| `FATAL: unknown engine '<value>'` | the card's `engine:` value is misspelt — it must be `llama-cpp` |

Open the log from the panel with the **Logs** button on the row.
