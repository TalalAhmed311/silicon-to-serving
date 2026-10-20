# ⚡ Silicon-to-Serving

A self-paced course (~**44 weeks** at ≈15 h/week) for becoming an **AI infrastructure and inference engineer**: deploy and operate LLM serving on cloud GPUs, and write CUDA C++ kernels.

Every concept is taught **from first principles** with a visual explainer (**Learn · Simulate · Practice**), then practised with real code, tests, and cloud labs.

> **Build status:** Stages 1–4 done; Stage 5 (QA) is a first pass. Phases P0–P6, Capstone, Lane B L1–L6, interactive lessons, exercises, benches, AWS/GCP guides, and `platform/` projects are written. Many hardware runs are still `TODO(run)` — see [GAPS.md](GAPS.md).

---

## 🎯 What you will learn

| Path | Focus |
|---|---|
| **Readiness (R)** | Gaps only: C++, memory, terminal/git, Python/NumPy, maths/ML, HTTP, safe cloud account |
| **Lane A** | Systems → inference → serving engines → cloud deploy → multi-GPU → CUDA deep dive → engine internals → capstone |
| **Lane B** (optional) | CUDA C++ kernels on [LeetGPU](https://leetgpu.com/challenges) |

**Start here:** [course/R-readiness/README.md](course/R-readiness/README.md) · or open the interactive home at [`lessons/index.html`](lessons/index.html).

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
- Optional later: NVIDIA GPU / AWS account (see hardware tiers below)

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
python tools/build_lessons.py    # regenerate lesson HTML pages (safe to re-run)
cd lessons && python -m http.server 8080
```

Then open **http://localhost:8080** — start with Readiness (**R.1**), or jump to a lesson like **P1.2** (KV cache).

> Tip: progress is saved in the browser (`localStorage`). Each lesson has **Learn · Simulate · Practice** tabs.

### 3. Serve the MkDocs reference site (markdown syllabus / notes)

```bash
uv sync --extra docs
python tools/build_site.py serve
```

MkDocs prints the local URL (usually **http://127.0.0.1:8000**). This is the assembled Markdown course; the interactive explainers live in `lessons/`.

### 4. Run tests

```bash
S2S_SOLUTIONS=1 uv run pytest              # all T0 tests against reference solutions
uv run pytest course/P0-systems-primer     # your stubs: fail until you implement them
python tools/check_links.py                # local link check
```

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

## 📁 Repository layout

```
README.md          ← you are here
SOURCES.md         every external source (URL, pin, license)
GLOSSARY.md        one-line definitions
GAPS.md            UNVERIFIED / TODO(run) items + how to close them
PROGRESS.md        week-by-week checklist
CLAUDE.md          notes for contributors / agents
design/            revamp brief + lesson prototypes
lessons/           interactive lesson app (HTML/JS)  ← open this first
course/            P0–P6 + capstone Markdown modules
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
python tools/build_lessons.py              # rebuild lessons/*.html from content/*.js
python tools/build_site.py                 # build MkDocs into site/build
python tools/build_site.py serve           # live MkDocs preview
python tools/check_links.py                # Markdown link check
bash tools/ci_terraform.sh                 # Terraform format/validate (local)
S2S_SOLUTIONS=1 uv run pytest              # full T0 suite
```

Authoring a new interactive lesson? See [lessons/AUTHORING.md](lessons/AUTHORING.md) and the reference lesson [`lessons/content/p1-2.js`](lessons/content/p1-2.js). Course revamp brief: [design/REVAMP.md](design/REVAMP.md).

---

## 📊 Where things stand

| Stage | Status |
|---|---|
| 1. Source index | ✅ Done — [SOURCES.md](SOURCES.md) |
| 2. Syllabus | ✅ Done |
| 3. Pilot module | ✅ Done |
| 4. Build-out (P0 → Capstone + Lane B) | ✅ Done |
| 5. QA and delivery | 🟡 First pass — open: full `uv run pytest` + hardware runs ([GAPS.md](GAPS.md)) |
| Interactive lessons revamp | 🟢 Lesson shell + full catalog under `lessons/` |

---

## 📜 License

Course text is original. External material is linked and summarized, never copied. LeetGPU problems are **CC BY-NC-ND 4.0** — only links appear here. See [SOURCES.md](SOURCES.md) for every source’s license.
