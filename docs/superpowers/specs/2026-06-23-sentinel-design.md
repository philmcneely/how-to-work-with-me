# Sentinel — Autonomous Quality Engineering Pipeline

**Design Spec** — 2026-06-23

## Overview

Sentinel is an autonomous quality engineering pipeline that watches a ClickUp board for work and orchestrates multiple specialized agents through the software development lifecycle. It is pluggable, enterprise-grade, and runs on dedicated Mac Minis.

The system proves a complete AI+QA vision with working code: autonomous bug detection, test generation from acceptance criteria, test quality evaluation, multi-LLM judge panels, and real-time dashboarding — backed by a public repo that anyone can inspect.

## Why This Exists

LinkedIn is full of people posting "I built an AI QA agent" with infographics and no code. Research across 30+ saved LinkedIn posts found that **2 out of 30+ link a public repo**. The broader landscape has only 3-4 real open-source tools (TestZeus Hercules, Autonoma, Playwright's native agents, Healenium). Everything else is commercial, single-file demos, or claims without proof.

Sentinel is the answer: a running system with real code, real metrics, and a live dashboard anyone can watch.

## Architecture Decisions

### Monorepo (not multi-repo)

**Decision:** Single monorepo for all components.

**Why:**
- Phil is the primary developer. Multi-repo overhead (6+ repos, independent versioning, cross-repo contract sync) isn't worth it until there are contributors.
- Contracts (JSON schemas) stay in sync because they live next to the code that uses them.
- Go + TypeScript monorepo is standard practice (Turborepo, Makefiles, or Nx).
- Can always split later if a component gets independent traction.
- Simpler CI — one pipeline tests everything, contract compatibility is guaranteed.

**When to revisit:** If any single component gets external contributors or needs an independent release cycle, extract it into its own repo at that point.

### Language: Go + TypeScript Hybrid

**Decision:** Go for the agent core, orchestrator, CLI, and all infrastructure. TypeScript for the dashboard and Playwright test execution layer.

**Why Go for agents:**
- Single static binaries (~15-20MB). Deploy by copying a file. No runtime, no node_modules, no venv.
- Built-in concurrency (goroutines) — agents are inherently concurrent (watching NATS, executing work, reporting).
- NATS client library is Go-native (NATS itself is written in Go).
- Enterprise infrastructure teams expect Go for this kind of tooling.
- Memory footprint: ~50-100MB per agent. Three Mac Minis with 16GB each is more than enough.

**Why TypeScript for dashboard + test runner:**
- Dashboard is Next.js (React) — TypeScript is the only sane choice.
- Playwright's native API is TypeScript-first. The test runner wraps Playwright.
- Phil's existing playwright-ai-framework-ts ports directly into the runner package.
- Dashboard and runner can share type definitions.

**Why not Python:**
- Phil's existing ATGF code is Python, but the patterns port to Go (AC extraction, orchestration, auto-healing are algorithms, not library-dependent).
- Enterprise perception: Python is associated with scripts and data science, not production infrastructure.
- Dependency management (pip, venv) is fragile compared to Go's single binary.

### Message Bus: NATS

**Decision:** NATS for inter-component communication.

**Why:**
- Single Go binary (~10MB), zero external dependencies. Fits the "no infrastructure overhead" constraint.
- Pub/sub with queue groups — supports both fan-out (all QA agents get the story) and load balancing (round-robin across identical agents).
- JetStream persistence — if an agent goes down, it picks up where it left off. Messages aren't lost.
- Request/reply pattern — orchestrator can ask "who's registered?" and get responses.
- Near-zero latency (microseconds, not milliseconds).
- Used in production by Synadia, Tesla, Cloudflare — enterprise-credible.

**Alternatives considered:**
- Filesystem + polling: too much latency, Phil flagged this immediately.
- HTTP APIs: point-to-point, no fan-out, no replay, more coupling.
- Redis Streams: adds Redis as a dependency. More ops than NATS for this use case.

## Pipeline Flow

```
ClickUp "idea"
    │
    ▼
┌─────────┐   NATS: story.specced    ┌──────────┐   NATS: story.reviewed
│ PM Agent │ ───────────────────────► │ QA Agent │ ──────────────────────►
│  (Go)    │                          │ (pre)    │
└─────────┘                          └──────────┘
                                                          │
                                          NATS: story.ready-for-dev
                                                          │
                                                          ▼
┌──────────┐   NATS: story.coded     ┌──────────────┐
│ QA Agent │ ◄─────────────────────  │ Coder Agent  │
│ (post)   │                         │ (existing    │
└──────────┘                         │  fleet)      │
    │                                └──────────────┘
    │ NATS: story.verified
    ▼
┌───────────┐
│ Dashboard │  ← Real-time pipeline status, quality metrics, test reports
│ (Next.js) │
└───────────┘
```

Every arrow is a NATS message with a JSON payload conforming to a published contract schema. If you remove any agent, the pipeline pauses at that stage — it doesn't break. You can manually push a story past a stage via the CLI.

## Multi-Agent Architecture

### Agent Registry

Agents register themselves on startup with their role and specialization. The orchestrator tracks what's available and routes work accordingly. Multiple agents can fill the same role with different specializations.

| Role | Specialization | NATS Subject | Required |
|------|---------------|--------------|----------|
| pm | spec-writer | sentinel.pm.spec | yes |
| qa | ac-reviewer | sentinel.qa.ac-review | yes |
| qa | test-runner | sentinel.qa.test-run | yes |
| qa | explorer | sentinel.qa.explore | optional |
| qa | a11y-auditor | sentinel.qa.a11y | optional |
| security | sast-scanner | sentinel.sec.sast | optional |
| security | dep-audit | sentinel.sec.deps | optional |
| infra | deploy-verify | sentinel.infra.deploy | optional |
| coder | implementer | sentinel.code.impl | yes |
| judge | quality-gate | sentinel.judge.gate | yes |

### Fan-Out + Consensus

When a story reaches a stage, it fans out to ALL registered agents for that stage. The judge doesn't advance the story until all required agents have reported. Optional agents' reports are included if they arrive within a timeout window.

```
                    story.coded
                        │
            ┌───────────┼───────────┐
            ▼           ▼           ▼
     ┌────────────┐ ┌────────┐ ┌──────────┐
     │ QA: Runner │ │ QA:    │ │ Security │
     │ (runs AC   │ │Explorer│ │ (SAST +  │
     │  tests)    │ │(crawls │ │  deps)   │
     │            │ │ UI)    │ │          │
     └─────┬──────┘ └───┬────┘ └────┬─────┘
           │            │           │
           ▼            ▼           ▼
      report.json  report.json  report.json
           │            │           │
           └────────────┼───────────┘
                        ▼
                 ┌─────────────┐
                 │ Judge Panel │
                 └─────────────┘
```

### Agent Specialization via Config

Each agent is the same Go binary with a different config file. Adding a new specialization is adding a YAML file, not writing code.

```yaml
# sentinel-agent.yaml
role: qa
specialization: test-runner
subscribe:
  - sentinel.qa.test-run
publish:
  - sentinel.reports.qa
llm:
  provider: claude
  model: sonnet-4-6
plugins:
  - playwright-runner
  - allure-reporter
```

Adding a new agent (e.g., accessibility auditor):

```yaml
role: qa
specialization: a11y-auditor
subscribe:
  - sentinel.qa.a11y
publish:
  - sentinel.reports.qa
plugins:
  - axe-runner
  - lighthouse
```

The orchestrator sees the new agent register and includes it in the fan-out automatically.

## LLM-as-Judge: Oversight Model

### Why a Panel, Not a Single Judge

One judge is a single point of failure and a single point of bias. Sentinel uses the same multi-scorer pattern proven in Phil's book publishing pipeline (4 scorers: Claude adversarial, Claude cold, DeepSeek V3, GPT-4.1).

### Judge Panel Architecture

```
              Agent Reports
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
  ┌─────────┐ ┌─────────┐ ┌─────────┐
  │ Judge 1 │ │ Judge 2 │ │ Judge 3 │
  │ Claude  │ │ Gemini  │ │ GPT-4.1 │
  │ (harsh) │ │ (cold)  │ │ (cold)  │
  └────┬────┘ └────┬────┘ └────┬────┘
       │           │           │
       └───────────┼───────────┘
                   ▼
            ┌─────────────┐
            │  Consensus  │
            │  Engine     │
            └──────┬──────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   Unanimous    Majority   Split/Fail
   pass →       pass →     → human
   advance      advance    review
                + flag
```

### Consensus Rules

- **Unanimous pass** — story advances automatically.
- **Majority pass** (2/3) — advances but flagged for human spot-check on the dashboard.
- **Majority fail or split** — story blocked, human review required, notification sent.
- **Unanimous fail** — back to coder with combined reasoning from all three judges.

### Cross-Provider Independence

The judge panel uses **different LLM providers** than the agents it evaluates. If QA agents use Claude, judges include Gemini and GPT-4.1. This prevents the "grading your own homework" problem — no single model's biases dominate.

### Judge Inputs

Each judge receives:
- The original acceptance criteria
- All agent reports (QA, security, infra)
- The code diff
- Test execution results (pass/fail/skip counts, failure details)
- Previous judge verdicts for this story (if it was sent back for rework)

### Judge Evaluation Criteria

- Are all acceptance criteria actually covered by tests?
- Are the tests meaningful or superficial (asserting existence vs. asserting behavior)?
- Did security scanning find anything the QA agents missed?
- Are there obvious gaps nobody caught?
- Is the code diff consistent with what the tests verify?

### Calibration

The dashboard tracks each judge's behavior over time:
- Pass/fail rate per judge
- Agreement rate across the panel
- How often a solo dissent was correct (when Phil overrides)
- Alert if a judge rubber-stamps (>95% pass rate) or blocks everything (<50% pass rate)

### Judge Configuration

```yaml
judge:
  mode: panel            # single | panel
  consensus: majority    # unanimous | majority
  escalation: human      # human | auto-retry
  panelists:
    - provider: claude
      model: sonnet-4-6
      persona: adversarial
    - provider: gemini
      model: gemini-2.5-flash
      persona: cold-reviewer
    - provider: openai
      model: gpt-4.1
      persona: cold-reviewer
  calibration:
    min_pass_rate: 0.50
    max_pass_rate: 0.95
    alert_on_drift: true
```

## Contract Layer (Pluggability)

Every component communicates via JSON messages conforming to published schemas. This is the pluggability boundary.

### Schema Directory

```
contracts/
├── story.schema.json          # A work item moving through the pipeline
├── ac-review.schema.json      # QA pre-code: AC quality assessment
├── test-plan.schema.json      # Generated test scenarios
├── test-result.schema.json    # Test execution results
├── quality-report.schema.json # Agent quality assessment
├── judge-verdict.schema.json  # Judge panel decision
├── agent-register.schema.json # Agent self-registration on startup
└── event-envelope.schema.json # NATS message wrapper
```

### Event Envelope

Every NATS message uses the same envelope:

```json
{
  "id": "evt_abc123",
  "timestamp": "2026-06-23T10:30:00Z",
  "source": "sentinel-qa-runner-01",
  "type": "quality-report",
  "storyId": "CLICK-1234",
  "schemaVersion": "1.0.0",
  "payload": { }
}
```

### Pluggability Examples

- **Don't want ClickUp?** Write an adapter for Jira/Linear/GitHub Issues that produces `story.schema.json` messages. The pipeline doesn't care where stories come from.
- **Don't want Sentinel's test runner?** Write your own that publishes `test-result.schema.json` to `sentinel.reports.qa`.
- **Want to use Healenium for self-healing?** Write a plugin adapter that translates Healenium output to `quality-report.schema.json`.
- **Want your own dashboard?** Subscribe to `sentinel.judge.verdict` and consume the JSON.
- **Don't want a security agent?** Don't run one. The judge skips the security report — optional agents are not required for consensus.

## Repository Structure

```
sentinel/
├── cmd/                        # Go binaries
│   ├── sentinel-orchestrator/  # Core pipeline engine
│   ├── sentinel-agent/         # Generic agent binary (config determines role)
│   ├── sentinel-cli/           # CLI for manual operations
│   └── sentinel-nats/          # Embedded NATS server (optional, for single-machine)
├── pkg/                        # Shared Go packages
│   ├── nats/                   # NATS client wrapper
│   ├── clickup/                # ClickUp API client
│   ├── llm/                    # LLM provider abstraction (Claude, Gemini, OpenAI, Ollama)
│   ├── contracts/              # JSON schema validation
│   ├── plugin/                 # Plugin interface definitions
│   ├── agent/                  # Agent lifecycle (register, heartbeat, shutdown)
│   └── judge/                  # Judge panel consensus engine
├── runner/                     # TypeScript — test execution layer
│   ├── playwright/             # Playwright test runner
│   ├── evaluator/              # Test quality evaluator (LLM-as-judge for tests)
│   └── explorer/               # Autonomous exploratory testing agent
├── dashboard/                  # TypeScript — Next.js dashboard
│   ├── app/                    # Pages (pipeline view, metrics, judge log, agent status)
│   └── components/             # UI components
├── plugins/                    # Drop-in plugin adapters
│   ├── healenium/              # Healenium self-healing adapter
│   ├── browserstack/           # BrowserStack adapter
│   ├── jira/                   # Jira adapter (alternative to ClickUp)
│   └── axe/                    # axe-core accessibility adapter
├── contracts/                  # JSON Schema definitions
│   ├── story.schema.json
│   ├── ac-review.schema.json
│   ├── test-plan.schema.json
│   ├── test-result.schema.json
│   ├── quality-report.schema.json
│   ├── judge-verdict.schema.json
│   ├── agent-register.schema.json
│   └── event-envelope.schema.json
├── configs/                    # Example agent configurations
│   ├── pm-spec-writer.yaml
│   ├── qa-ac-reviewer.yaml
│   ├── qa-test-runner.yaml
│   ├── qa-explorer.yaml
│   ├── security-sast.yaml
│   ├── judge-panel.yaml
│   └── orchestrator.yaml
├── deploy/                     # Deployment configs
│   ├── launchdaemons/          # macOS LaunchDaemon plists
│   ├── systemd/                # Linux systemd units
│   └── docker-compose.yaml    # Docker option for non-Mac deployments
├── tests/                      # Test suites
│   ├── unit/                   # Go package tests
│   ├── integration/            # Multi-component tests (embedded NATS)
│   ├── e2e/                    # Full pipeline tests
│   └── contracts/              # Schema compatibility tests
├── docs/                       # Documentation
│   ├── architecture.md
│   ├── getting-started.md
│   ├── plugin-guide.md
│   └── agent-config-reference.md
├── Makefile                    # Build, test, deploy targets
├── go.mod
├── go.sum
└── README.md
```

## Deployment Model

### Containerized (Docker)

All components are containerized. Development and testing runs on HAL9000 (6x 3090, Ubuntu native, Docker available). Production deployment to Mac Minis comes later.

```
docker-compose.yml
├── nats          # Official NATS image, port 4222
├── orchestrator  # sentinel-orchestrator Go binary
├── agent-pm      # sentinel-agent with PM config
├── agent-qa-*    # sentinel-agent with QA configs (multiple)
├── agent-sec     # sentinel-agent with security config (optional)
├── judge         # sentinel-agent with judge config
└── dashboard     # Next.js dashboard, port 3000
```

Each Go agent is a multi-stage Docker build: build stage compiles the static binary, runtime stage is `scratch` or `alpine` (~20MB final image). All containers connect to the NATS service by hostname.

### Hardware

| Machine | Role | Phase |
|---------|------|-------|
| HAL9000 (192.168.1.225) | Development + testing | Now — Docker containers, full pipeline testing |
| Mac Minis (10 available, 16GB Intel each) | Production deployment | Later — LaunchDaemons or Docker, horizontally scaled |
| Dedicated CI machine | Jenkins for build/test automation | Later — when pipeline is mature enough to gate on |

### Single-Machine Mode

Default for development. All containers run on one machine via docker-compose, talking to `nats://nats:4222` on the Docker network. `docker-compose up` starts everything. Scale to multiple machines when test execution load warrants it.

### Future: Multi-Machine Production

When ready for production on Mac Minis:
- LaunchDaemons (headless Macs, consistent with existing fleet pattern) OR Docker on each Mini
- Each agent is either a launchd plist or a Docker container
- `sentinel-cli deploy` generates deployment configs
- Jenkins on a dedicated Mini for CI/CD (Phil has deep operational experience — 218K+ builds, 24/7, tiered SLA at HomeAway/Expedia)

### Resource Footprint

- Go binary: ~15-20MB disk, ~50-100MB RAM per agent
- NATS server: ~10MB disk, ~30MB RAM
- Dashboard: ~200MB RAM (Next.js)
- Playwright browsers: ~500MB RAM per browser instance (heaviest component, runs on Mini 2)
- SQLite: negligible

Total per Mini: ~2-4GB RAM used, well within 16GB.

## Testing Strategy

### Test Levels

| Level | Tool | What | When |
|-------|------|------|------|
| Unit | `go test` | Every Go package has `_test.go`. Schema validation, state machine, API client mocks. | Every commit |
| Integration | `go test` + embedded NATS | Components talking to each other. Pipeline flow, agent registration, judge consensus. | Every PR |
| E2E | Full system | Story through complete pipeline with real ClickUp board and real LLMs. | Nightly |
| Contract | Schema validation | New schemas must accept old messages. Every example payload validates. | Every commit |
| Dashboard | Playwright | Dashboard UI tests (eating our own dog food). | Every PR |

### Testing Principles

- Go tests use embedded NATS — no external dependency needed to run tests.
- Contract tests run on every PR — breaking a schema is a breaking change.
- Integration tests run the full pipeline with mock LLM responses (deterministic, fast).
- E2E tests run nightly against a real ClickUp board and real LLMs (expensive, slow, but catches real integration issues).
- Dashboard has Playwright tests — Sentinel tests itself.

## Reuse from Existing Repos

| Existing Code | How It Fits in Sentinel |
|---|---|
| playwright-ai-framework-ts | Becomes `runner/playwright/` — the test execution engine |
| test-forge (AC → test scenarios) | Logic ports into QA agent's pre-code AC review |
| ATGF (Jira poller, AC extraction, auto-healer) | Patterns inform the orchestrator and QA runner. Auto-healer becomes a plugin. |
| claude-agent ClickUp integration | ClickUp API client ports to Go. Same Team ID, List ID, status workflow. |
| claude-agent fleet patterns | LaunchDaemons, SSH deploy, cron monitoring — same operational model. |

## Demo & Visibility Strategy

### 1. Live Dashboard

Publicly accessible via Cloudflare Tunnel. Shows the pipeline in real-time: stories flowing through stages, agent activity, judge verdicts with reasoning, quality metrics over time.

### 2. GitHub README with Demo Video

Screen recording: idea dropped in ClickUp → PM specs it → QA reviews → code written → tests run → judge panel deliberates → story marked done. 60-second loop.

### 3. "Try It" Mode

Public endpoint where someone submits a story description and watches Sentinel spec it out, generate test scenarios, and evaluate AC quality. Read-only, no code execution. Demonstrates the PM + QA pre-code flow without infrastructure risk.

### 4. Blog Post / LinkedIn Article

"I Built an Autonomous QA Pipeline. Here's the Repo." Architecture diagrams, real metrics from the running system, link to the live dashboard. Unlike every other AI+QA post on LinkedIn — this one has code.

### 5. Conference Talk Material

Target conferences: CAST, TestBash, Agile Testing Days. Talk title: "Autonomous Quality Engineering: From Idea to Verified Code Without Human Intervention."

## Build Phases

| Phase | What | Duration | Deliverable |
|---|---|---|---|
| 0 | Repo scaffold, NATS setup, contract schemas, CI | 1 week | Monorepo builds, NATS runs, schemas validate |
| 1 | Orchestrator + PM Agent + ClickUp integration | 2 weeks | Ideas become specced stories with AC |
| 2 | QA Agent (pre-code): AC review + test scenario generation | 2 weeks | Stories get test plans before coding starts |
| 3 | QA Agent (post-code): Playwright test execution + reporting | 2 weeks | Code gets tested, results published |
| 4 | Judge Panel: multi-LLM consensus gate | 1 week | Quality verdicts with reasoning |
| 5 | Dashboard: real-time pipeline view + metrics | 2 weeks | Live visibility into everything |
| 6 | Security Agent + Explorer Agent | 2 weeks | SAST scanning + autonomous UI exploration |
| 7 | Polish, E2E tests, deploy to Mac Minis, demo recording | 2 weeks | Production-ready, demo-able |

**Total: ~14 weeks sequential.** Phases 1-3 are the critical path. Phases 4-7 can overlap.

## QA Agent Architecture — Generate / Run / Heal

The QA agent operates in three distinct modes. The key insight: **the agent is the brain, not the legs.** Agent intelligence is used at creation and maintenance time, not at execution time. This keeps cost sustainable as test suites grow.

### Mode 1: Generator (Expensive, Runs Once Per Feature)

Takes acceptance criteria + intent metadata → produces static Playwright test code + intent metadata file.

**Input:** AC from PM agent, feature context, application URL
**Output:** `*.spec.ts` Playwright test file + `*.intent.json` metadata file
**Execution backend options:**
- **Cloud LLM** (Claude, GPT-4.1) — highest quality code generation, ~$0.05-0.10 per test
- **CUA** (Computer Use Agent) — explores the app visually first, then writes tests from what it sees. Best for unknown UIs. ~$1-2 per test (vision model cost per screenshot step)
- **Self-hosted vision model** — lower cost, good enough for well-structured UIs. See Self-Hosted Model Options below.

**Intent metadata schema** (attached to every generated test):

```json
{
  "testFile": "login.spec.ts",
  "intent": {
    "goal": "Verify customer can log in with valid credentials",
    "thinking": "Login is the primary entry point. SSO and email/password both need to work. Session should persist across page refresh.",
    "expectedOutcome": "User sees dashboard with their name displayed",
    "businessContext": "Auth is revenue-gating — broken login = $0 revenue"
  },
  "selectors": {
    "loginButton": "#login-btn",
    "emailField": "[data-testid='email']",
    "dashboard": ".dashboard-header"
  },
  "generatedBy": "sentinel-qa-generator",
  "generatedAt": "2026-07-15T10:30:00Z",
  "schemaVersion": "1.0.0"
}
```

### Mode 2: Runner (Cheap, Runs Every CI Cycle)

Executes static Playwright tests. **No agent involved.** Just `npx playwright test`. Pennies per run.

The runner is a thin wrapper around Playwright that:
- Runs the `*.spec.ts` files generated by Mode 1
- Captures screenshots, traces, and DOM snapshots on failure
- Publishes results to NATS (`reports.qa`)
- Attaches the `*.intent.json` to failure reports so the healer has context

### Mode 3: Healer (Moderate Cost, Runs Only on Failure)

Receives a failure report → reads intent metadata → decides: **real bug or UI change?**

**If UI change:** Rewrites the test selectors/assertions to match the new UI, re-runs to verify, commits the healed test.
**If real bug:** Files a bug report with evidence (screenshot, DOM diff, intent context, reproduction steps).

**Input:** Failure report (error + screenshot + DOM + trace) + `*.intent.json`
**Output:** Healed `*.spec.ts` OR bug report published to NATS

The "thinking" field from the intent metadata is critical here — it tells the healer *why* the test exists, so it can distinguish "the button moved" (heal) from "the button is gone" (bug).

**Execution backend options:**
- **Cloud LLM** — analyzes failure context, rewrites test code. ~$0.03-0.05 per heal.
- **Self-hosted vision model** — can compare before/after screenshots to classify failure type.
- **CUA** — re-explores the changed UI visually. Most expensive but most accurate for complex UI changes.

### Cost Model: Generate / Run / Heal

| Activity | Frequency | Agent Cost | Compute Cost |
|----------|-----------|------------|--------------|
| Generate tests | Once per feature | $0.05-2.00/test | Negligible |
| Run tests | Every CI cycle | $0.00 | ~$0.01 (Playwright) |
| Heal tests | On failure only | $0.03-0.10/heal | Negligible |
| Full regen | Major UI overhaul | Same as generate | Negligible |

**Steady state cost for 200-test suite:** ~$0.01/run (Playwright only). Agent costs spike only at feature creation and UI breakage. Compare to full agentic execution: ~$200-400/run.

### Self-Hosted Model Options for Generator/Healer

For teams that want to minimize API costs or keep everything on-prem. Vision + code generation capability required.

**Viable candidates:**

| Model | Size | Memory | Vision | Code Gen | Speed (est.) | Runtime |
|-------|------|--------|--------|----------|-------------|---------|
| Qwen3-VL-72B (fp16) | 72B | ~144GB unified | Excellent | Strong | ~10-15 tok/s MLX | Mac Studio M2 Ultra (192GB) — fits without quantization |
| Qwen3-VL-72B (int4) | 72B | ~40GB | Excellent | Strong | ~15-20 tok/s CUDA | HAL9000 3x 3090 via vLLM (if GPU time available) |
| Qwen3-VL-32B (int4) | 32B | ~17GB | Strong | Good | ~35-40 tok/s | Mac Studio or 1x 3090. Fast healer alternative. |
| InternVL3-78B (int4) | 78B | ~42GB | Excellent | Good | ~12 tok/s | Mac Studio or 3x 3090. Best pure vision. |
| Qwen3-VL-8B (q4) | 8B | ~5GB | Good | Decent | ~60+ tok/s MLX | Mac Studio — fast triage model. |

**Not viable:** CogAgent (hasn't kept pace), ShowUI (research only), Qwen3-VL-235B (needs 4x A100 80GB).

**Recommended Mac Studio deployment (primary inference host):**

HAL9000's GPUs are reserved for podcast production (VibeVoice). Mac Studio M2 Ultra (192GB unified memory) is the QA agent's inference host.

| Role | Model | Runtime | Speed | When |
|------|-------|---------|-------|------|
| Triage (bug vs UI change?) | Qwen3-VL-8B | MLX on Mac Studio | ~60+ tok/s | Every test failure |
| Generator (screenshot → Playwright) | Qwen3-VL-72B fp16 | MLX on Mac Studio | ~10-15 tok/s | New feature only |
| Healer (rewrite broken tests) | Qwen3-VL-72B fp16 | MLX on Mac Studio | ~10-15 tok/s | Confirmed UI change |

**Advantages of Mac Studio over HAL9000 for this workload:**
- 192GB unified memory fits 72B at full precision — no quantization quality loss
- No GPU contention with podcast production
- Generate/run/heal pattern means inference is bursty, not continuous — Mac Studio handles burst workloads well
- Models already stored on NVMe (/Volumes/Data)

**Fallback:** If Mac Studio is under load (Ollama, other work), HAL9000 can run the 72B on 3x 3090 via vLLM during off-hours when VibeVoice isn't generating.

**Architecture note:** The generator and healer don't need to be the same model. A strong vision model can analyze the UI, then a strong code model (Qwen3-30B, DeepSeek-V3) writes the Playwright code from the vision model's description. Two-model pipeline trades latency for allowing each model to specialize.

**What does NOT work self-hosted (yet):**
- Full CUA-style interaction (screenshot → click → screenshot → type) requires models specifically trained for agentic computer use. Open models can classify and describe UIs but can't reliably drive multi-step browser interaction. Use cloud CUA (Anthropic Computer Use) for this until open agentic models mature.

### Pluggable Execution Backend

The QA agent defines an `ExecutionBackend` interface:

```go
type ExecutionBackend interface {
    GenerateTest(ac AcceptanceCriteria, appContext AppContext) (*TestArtifact, error)
    ClassifyFailure(report FailureReport, intent IntentMetadata) (*Classification, error)
    HealTest(report FailureReport, intent IntentMetadata, currentTest string) (*TestArtifact, error)
}
```

Implementations:
- `CloudLLMBackend` — Claude/GPT via API
- `CUABackend` — Anthropic Computer Use for visual exploration
- `LocalModelBackend` — self-hosted via vLLM/Ollama
- `HybridBackend` — local model for classification, cloud for generation (cost-optimized default)

The orchestrator config specifies which backend per mode:

```yaml
qa_agent:
  generator:
    backend: cloud-llm
    model: claude-sonnet-4-6
  runner:
    backend: playwright  # no agent needed
  healer:
    backend: hybrid
    classifier: local-model
    classifier_model: qwen2.5-vl-7b
    code_gen: cloud-llm
    code_gen_model: claude-sonnet-4-6
```

## NATS Subject Hierarchy

```
sentinel.
├── story.                    # Story lifecycle events
│   ├── created               # New idea from ClickUp
│   ├── specced               # PM finished, has AC
│   ├── reviewed              # QA pre-code approved AC
│   ├── ready-for-dev         # Ready for coder
│   ├── coded                 # Code complete, ready for QA
│   ├── verified              # Judge approved
│   └── failed                # Judge rejected (back to coder)
├── agent.                    # Agent lifecycle
│   ├── register              # Agent comes online
│   ├── heartbeat             # Periodic health signal
│   └── shutdown              # Agent going offline
├── reports.                  # Agent work products
│   ├── pm                    # PM agent outputs
│   ├── qa                    # QA agent outputs
│   ├── security              # Security agent outputs
│   └── infra                 # Infra agent outputs
├── judge.                    # Judge panel
│   ├── request               # Orchestrator asks for verdict
│   ├── verdict               # Individual judge decision
│   └── consensus             # Panel consensus result
└── system.                   # Operational
    ├── metrics               # Performance telemetry
    ├── errors                # Error events
    └── config                # Config change notifications
```

## LLM Cost Model

### Per-Ticket LLM Calls (Worst Case)

Each story that flows through the full pipeline triggers these LLM calls:

| Stage | Agent | Provider | Call Type | Est. Tokens (in/out) | Est. Cost |
|-------|-------|----------|-----------|---------------------|-----------|
| PM Spec | PM Agent | Claude (subscription) | Spec + write AC | ~4K/2K | $0.00 (sub) |
| AC Review | QA Pre-Code | Claude (subscription) | Review AC quality | ~3K/1K | $0.00 (sub) |
| Test Plan Gen | QA Pre-Code | Claude (subscription) | Generate test scenarios | ~4K/3K | $0.00 (sub) |
| Coding | Coder Agent | Local model (Ollama/vLLM) | Implement story | ~8K/4K | $0.00 (local) |
| Test Eval | QA Post-Code | Claude (subscription) | Evaluate test results | ~5K/2K | $0.00 (sub) |
| Judge 1 | Judge Panel | Claude API (OpenRouter) | Adversarial review | ~10K/2K | ~$0.04 |
| Judge 2 | Judge Panel | Gemini 2.5 Flash (OpenRouter) | Cold review | ~10K/2K | ~$0.01 |
| Judge 3 | Judge Panel | GPT-4.1 (OpenRouter) | Cold review | ~10K/2K | ~$0.02 |
| **Failed → Recode → Rejudge** | All judges again | Mixed | Second pass | ~30K/6K | ~$0.07 |

### Cost Summary Per Ticket

| Scenario | API Cost | Notes |
|----------|----------|-------|
| **Happy path** (pass first time) | **~$0.07** | 3 judge calls via OpenRouter |
| **One rejection** (recode + rejudge) | **~$0.14** | 6 judge calls total |
| **Two rejections** (worst case) | **~$0.21** | 9 judge calls total |
| **Monthly @ 20 tickets/week** | **$5.60–$16.80** | 80 tickets, depends on pass rate |

### Assumptions

- **Claude subscription** covers PM, QA pre-code, QA post-code, and test evaluation via Claude Code or Claude Max. These are the "thinking" agents — speccing, reviewing, evaluating. The subscription handles this volume easily.
- **Local models** (Ollama on Mac Studio or vLLM on HAL9000) handle coding. No API cost.
- **OpenRouter** for the judge panel only — this is the one place where cross-provider independence is required, so API calls are unavoidable. OpenRouter gives access to Claude, Gemini, and GPT-4.1 through a single API key.
- Token estimates assume a medium-complexity story with 5-8 AC items, a ~200-line code diff, and 3-4 QA reports feeding into each judge.
- If the security agent or explorer agent is also active, add ~$0.01-$0.02 per ticket for their evaluation (they can run on subscription or local).

### Cost Optimization Levers

- **Judge caching**: If a story is re-judged after minor fixes, only the changed diff and updated reports need re-evaluation. Context from the previous round can be cached in the prompt.
- **Single judge mode**: For low-risk stories (internal tooling, docs, config changes), run a single judge instead of the full panel. Configurable per-project.
- **Local judge option**: For development/testing, run all judges via local models. Zero API cost, lower quality — fine for iterating on the pipeline itself.
