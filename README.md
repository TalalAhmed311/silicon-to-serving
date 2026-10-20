<div align="center">

<img src="assets/logo.svg" alt="" width="124" height="124">

<h1>⚡ Silicon-to-Serving</h1>

<p><b>From C pointers and SIMD to CUDA kernels and multi-cloud LLM serving.</b></p>

<p>
<img src="https://img.shields.io/badge/duration-~44%20weeks-1f8fb8" alt="~44 weeks">
<img src="https://img.shields.io/badge/lessons-64%20interactive-0d7a5f" alt="64 interactive lessons">
<img src="https://img.shields.io/badge/python-3.11%2B-3776AB?logo=python&logoColor=white" alt="Python 3.11+">
<img src="https://img.shields.io/badge/starts%20on-a%20laptop,%20no%20GPU-38c6e8" alt="Starts on a laptop, no GPU">
</p>

</div>

---

A self-paced course (≈ **44 weeks** at ~15 h/week — about 11 h on Lane A plus 4 h on Lane B) for becoming an **AI infrastructure and inference engineer**: deploy and operate LLM serving on cloud GPUs, and write CUDA C++ kernels.

Every concept is taught **from first principles** with a visual explainer (**Learn · Simulate · Practice**), then practised with real code, tests and cloud labs. **58 course modules**, **64 interactive lessons**, **20 standalone animations** and **18 portfolio projects** that stack into one platform.

> **Build status:** Stages 1–4 done; Stage 5 (QA) is a first pass. Phases P0–P6, Capstone, Lane B L1–L6, interactive lessons, exercises, benches, AWS/GCP guides and `platform/` projects are all written. The T0 test suite now runs, with known failures; most hardware runs are still `TODO(run)`. See [Where things stand](#-where-things-stand) and [GAPS.md](GAPS.md).

**Contents** — [What you will learn](#-what-you-will-learn) · [Course map](#️-course-map) · [Prerequisites](#-prerequisites) · [Quickstart](#-quickstart) · [Hardware tiers](#️-hardware-tiers) · [What you will have built](#-what-you-will-have-built) · [Repository layout](#-repository-layout) · [Useful commands](#️-useful-commands) · [Where things stand](#-where-things-stand) · [License](#-license)

---

## 🎯 What you will learn

| Path | Focus |
|---|---|
| **Readiness (R)** | Gaps only: C++, memory, terminal/git, Python/NumPy, maths/ML, HTTP, safe cloud account |
| **Lane A** | Systems → inference → serving engines → cloud deploy → multi-GPU → CUDA deep dive → engine internals → capstone |
| **Lane B** (optional) | CUDA C++ kernels on [LeetGPU](https://leetgpu.com/challenges) |

**Start here:** [course/R-readiness/README.md](course/R-readiness/README.md) — answer the self-check, skip the R lessons you don't need, then take one week per row of [PROGRESS.md](PROGRESS.md). Or open the interactive home at [`lessons/index.html`](lessons/index.html).

---

## 🗺️ Course map

| Phase | Weeks | Topic | Syllabus |
|---|---|---|---|
| 🧭 **R** | 0 | Readiness self-check + R.1–R.7 as needed | [start](course/R-readiness/README.md) · [syllabus](course/R-readiness/syllabus.md) |
| 🧱 **P0** | 1–3 | Systems primer: C/C++, OS, memory, SIMD | [syllabus](course/P0-systems-primer/syllabus.md) |
| 🧠 **P1** | 4–6 | How LLM inference works + hardware math; first CUDA | [syllabus](course/P1-inference-fundamentals/syllabus.md) |
| 🚀 **P2** | 7–12 | Serving with inference engines | [syllabus](course/P2-serving-engines/syllabus.md) |
| ☁️ **P3** | 13–20 | Deployment and infra (containers, K8s, multi-cloud) | [syllabus](course/P3-deployment-and-infra/syllabus.md) |
| 🔗 **P4** | 21–24 | Multi-GPU, GPU sharing, distributed jobs | [syllabus](course/P4-multi-gpu-and-distributed/syllabus.md) |
| ⚙️ **P5** | 25–34 | CUDA performance deep dive | [syllabus](course/P5-cuda-deep-dive/syllabus.md) |
| 🧬 **P6** | 35–40 | Engine internals | [syllabus](course/P6-engine-internals/syllabus.md) |
| 🏆 **Cap** | 41–44 | Multi-region failover + public teardown | [syllabus](course/capstone/syllabus.md) |
| 🔥 **Lane B** | 1–44 | CUDA levels L1–L6 on LeetGPU | [overview](laneB-cuda/README.md) |

Track weeks in [PROGRESS.md](PROGRESS.md). Terms live in [GLOSSARY.md](GLOSSARY.md).

---

## 💻 Prerequisites

- **Python ≥ 3.11** and [`uv`](https://docs.astral.sh/uv/)
- Linux or macOS (or WSL on Windows)
- A browser (for interactive lessons and LeetGPU)
- Optional later: NVIDIA GPU / AWS account (see [hardware tiers](#️-hardware-tiers))

---

## 🚀 Quickstart

### 1. Install

```bash
# From the repo root
uv sync                          # T0 stack (numpy, pytest, fastapi, …)
uv sync --extra docs             # optional: MkDocs for the markdown site
uv sync --extra torch            # optional: PyTorch / Triton (heavier)
```

### 2. Open the interactive lessons (main UI)

Lessons are static HTML/JS under `lessons/`. Serve the folder so module loads work:

```bash
uv run python tools/build_lessons.py    # regenerate lesson HTML pages (safe to re-run)
cd lessons && python3 -m http.server 8080
```

Then open **http://localhost:8080** — start with Readiness (**R.1**), or jump to a lesson like **P1.2** (KV cache).

> Tip: progress is saved in the browser (`localStorage`). Each lesson has **Learn · Simulate · Practice** tabs.

### 3. Serve the MkDocs reference site (markdown syllabus / notes)

```bash
uv sync --extra docs
uv run python tools/build_site.py serve
```

MkDocs prints the local URL (usually **http://127.0.0.1:8000**). This is the assembled Markdown course; the interactive explainers live in `lessons/`.

> Run the tools through `uv run` so they pick up the project's virtualenv — `tools/build_site.py` shells out to `mkdocs`, which is only on `PATH` inside it.

### 4. Run tests

```bash
S2S_SOLUTIONS=1 uv run pytest              # all T0 tests against reference solutions
uv run pytest course/P0-systems-primer     # your stubs: fail until you implement them
uv run python tools/check_links.py         # local link check
```

Some tests fail today — see [Where things stand](#-where-things-stand) for the current snapshot and the known causes.

### 5. Start local servers (practice / portfolio code)

**Mock LLM** (fake OpenAI-compatible server — great on a laptop, port **8001**):

```bash
uv run uvicorn mockllm.server:app --app-dir platform --port 8001

curl -s localhost:8001/v1/completions \
  -H 'content-type: application/json' \
  -d '{"prompt":"hello world","max_tokens":5}'
```

**Tiny engine v1** (portfolio engine, fake model — port **8002**):

```bash
S2S_RUNNER=fake uv run uvicorn s2s_engine.server:app \
  --app-dir platform/engine/v1 --host 127.0.0.1 --port 8002

curl -s localhost:8002/v1/completions \
  -H 'content-type: application/json' \
  -d '{"prompt": "5 17 3", "max_tokens": 8}'
```

| Server | Port | Use for |
|---|---|---|
| Interactive lessons | `8080` | Learn / Simulate / Practice UI |
| MkDocs site | `8000` | Markdown syllabus & notes |
| `mockllm` | `8001` | Load tests, gateway, observability labs |
| Engine `#0` v1 | `8002` | P6 serving engine |

Bind to `127.0.0.1` for anything that can take prompts. Cloud guides use SSM port forwarding — no open SSH, no unauthenticated public endpoints.

---

## 🖥️ Hardware tiers

| Tier | Where | Used for |
|---|---|---|
| **T0** | Laptop, no GPU | Most lessons, calculators, unit tests, mockllm |
| **T1** | LeetGPU in the browser | Lane B L1–L4 |
| **T2** | One NVIDIA GPU (local or AWS `g4dn`/`g5`/`g6`) | Single-node serving, P5 kernels |
| **T3** | Multi-GPU / multi-node AWS | Clusters, distributed, MIG, failover |

**Cloud safety (every AWS guide):** hourly cost stated · idle auto-stop · budget alarm · `make down` tested · SSM-only access · no unauthenticated public endpoint.

---

## 🧰 What you will have built

Every project is a layer of **one** platform, not a scrapbook of demos — see [platform/README.md](platform/README.md) for the architecture diagram and all 18.

| | Project | Phase | Tier |
|---|---|---|---|
| `#0` | Tiny serving engine: CPU v0 → GPU v1 with a scheduler, paged KV, radix prefix cache, sampling and CUDA graphs | P0 → P6 | T0/T2 |
| `#4` | Continuous-batching load test | P2 | T0/T2 |
| `#6` | Multi-model AI gateway: routing, retries, rate limits, token budgets | P2 | T0 |
| `#7` | Quantized serving bakeoff | P2 | T2 |
| `#1` | Self-hosted inference cluster on Kubernetes | P3 | T3 |
| `#13` | Observability spine (metrics, traces, dashboards) | P3 | T0/T3 |
| `#3` | Queue-depth GPU autoscaler with spot fallback | P3 | T3 |
| `#12` | A custom CUDA kernel, profiled and shipped into the serving path | P5 | T2 |
| `#14` | **Capstone:** multi-region failover with health hysteresis and CRDT budgets | Cap | T0/T3 |
| `#15` | **Capstone:** a public benchmark teardown of the whole thing | Cap | T0 |

---

## 📁 Repository layout

```
README.md          ← you are here
SOURCES.md         every external source (URL, pin, license)
GLOSSARY.md        one-line definitions
GAPS.md            UNVERIFIED / TODO(run) items + how to close them
PROGRESS.md        week-by-week checklist
assets/            repo mark (logo, social preview)
design/            revamp brief + lesson prototypes
lessons/           interactive lesson app (HTML/JS)  ← open this first
course/            R + P0–P6 + capstone Markdown modules
laneB-cuda/        CUDA levels L1–L6 + harness
platform/          portfolio code (engine, gateway, mockllm, …)
animations/        standalone HTML/JS/SVG animations
site/              MkDocs Material config
env/               Dockerfiles and lockfiles
infra/             AWS Terraform (+ GCP GKE for P3.9)
tools/             build_lessons, build_site, link checker, …
```

---

## 🛠️ Useful commands

```bash
uv run python tools/build_lessons.py        # rebuild lessons/*.html from lessons/content/*.js
uv run python tools/build_animations.py     # re-inline animations/src/* into animations/*.html
uv run python tools/build_site.py           # build MkDocs into site/build
uv run python tools/build_site.py serve     # live MkDocs preview
uv run python tools/check_links.py          # Markdown link check (add --external for http(s))
uv run python tools/list_gaps.py            # print the GAPS.md tables from the TODO markers
bash tools/ci_terraform.sh                  # Terraform format/validate (local, no AWS calls)
S2S_SOLUTIONS=1 uv run pytest               # full T0 suite
PW=$(npm root -g)/playwright node tools/check_lessons.js   # headless pass over every lesson (needs Playwright)
```

`build_lessons.py` and `build_animations.py` rewrite generated files in place — run them after editing `lessons/content/*.js` or `animations/src/*`, and check `git diff` before committing.

Authoring a new interactive lesson? See [lessons/AUTHORING.md](lessons/AUTHORING.md) and the reference lesson [`lessons/content/p1-2.js`](lessons/content/p1-2.js). Course revamp brief: [design/REVAMP.md](design/REVAMP.md). Repo mark and social card: [assets/README.md](assets/README.md).

---

## 📊 Where things stand

| Stage | Status |
|---|---|
| 1. Source index | ✅ Done — [SOURCES.md](SOURCES.md) |
| 2. Syllabus | ✅ Done |
| 3. Pilot module | ✅ Done |
| 4. Build-out (P0 → Capstone + Lane B) | ✅ Done |
| 5. QA and delivery | 🟡 First pass — T0 suite runs with known failures; hardware runs open ([GAPS.md](GAPS.md)) |
| Interactive lessons revamp | 🟢 Lesson shell + full catalog under `lessons/` |

**T0 snapshot** — `S2S_SOLUTIONS=1 uv run pytest`, macOS on Python 3.12, 2026-10-20:

> **465 passed · 22 failed · 25 skipped · 8 xfailed · 4 errors.** Three of the errors are modules that fail to import, so the bare command stops at collection — add `--continue-on-collection-errors` for the numbers above.

Three known causes, all in the harness or the environment rather than the course material:

1. **Dynamically loaded modules aren't registered in `sys.modules`.** Helpers such as `course/common/python/s2s/mockserver.py` load a module with `importlib.util.spec_from_file_location` and then `exec_module` it without putting it in `sys.modules`; on Python 3.12 any `@dataclass` in that module raises `AttributeError: 'NoneType' object has no attribute '__dict__'`. The repo's own `s2s/exercise.py` already does this correctly — the one-line registration there is the fix. This accounts for most of the P1–P3 failures.
2. **`platform/` shadows the standard library.** [`conftest.py`](conftest.py) inserts `platform/` at the front of `sys.path`, so `import platform` finds the portfolio folder; that breaks `prometheus_client` and with it `platform/engine/v1/tests/test_server.py`.
3. **R.3 assumes GNU coreutils.** `exercises/02-shell-pipeline` compares exact output, and BSD `wc -l` on macOS pads its count with leading spaces where GNU `wc -l` does not.

Closing these is the first T0 task in [GAPS.md](GAPS.md); the hardware (`TODO(run-on: …)`) items are listed there in full.

---

## 📜 License

Course text is original. External material is linked and summarized, never copied. LeetGPU problems are **CC BY-NC-ND 4.0** — only links appear here. See [SOURCES.md](SOURCES.md) for every source's license.

No `LICENSE` file is checked in yet, so default copyright applies to this repository's own code and text until one is added.
