# FounderForge

**The AI engineering team for founders who build alone.**

FounderForge is a multi-agent pipeline that turns a plain-English feature
request into secure, tested Python code — with the founder staying as
architect, never touching the code.

Built on Google Agent Development Kit (ADK) and Gemini for the
KaggleX Vibe Coding Capstone (Agents for Business track).

---

## The problem

Non-technical founders can now vibe-code entire features in natural language.
But they cannot audit what the AI produces. They ship code with hardcoded
API keys, missing access controls, and silently-broken logic — because "it
compiled and ran" feels like proof it works.

FounderForge solves this by making the AI responsible for building AND for
proving the code is safe and correct, and by keeping the founder as the
architect who reads a plain-English report — never the code itself.

---

## What it does

- 📝 Turns vague requests into a precise spec (no more agent disagreement)
- 🛠️ Writes Python code from the spec
- 🧪 Generates pytest tests including edge cases
- 🔁 Self-corrects: runs tests, fixes failures, retries up to 3 times
- 🔐 Runs real security scans (bandit + secrets detection) on the output
- 📄 Produces a plain-English review report — the founder never reads code

---

## Architecture

Five specialized agents in a Sequential + Loop pipeline:

[User Request]

↓

Planner       →  Writes a precise, unambiguous spec

↓

Coder        →  Writes Python code from the spec

↓

TestWriter     →  Generates pytest tests with edge cases

↓

┌─────────────────────────────┐

│      CorrectionLoop         │  (LoopAgent, max 3 iterations)

│                             │

│    Runner  →  CoderFixer    │

│   (runs tests)  (fixes if   │

│                  they fail) │

└─────────────────────────────┘

↓

Reviewer       →  Runs security scans + plain-English report

↓

[Plain-English Report for Founder]


**Shared state as message bus:** each agent writes its output to shared
session state (`spec`, `generated_code`, `test_code`, `test_results`,
`review_report`). The next agent reads from state via template placeholders.
Agents never talk to each other directly.

---

## Course concepts demonstrated

1. **Multi-agent system (ADK)** — `SequentialAgent` and `LoopAgent`
   orchestrating five `LlmAgent` instances with shared state.
2. **Security features** — deterministic scans (bandit for static analysis,
   regex-based secrets detection) implemented as real Python tools, not
   prompt instructions. Security is baked in as code, not asked for.
3. **Agent skills** — the Reviewer uses a structured, reusable review
   skill format (Description / Critical / Warnings / Best Practices /
   Quick Win / LGTM) modeled on the `code-check.md` pattern from
   Google's Day 5 spec-driven development whitepaper.

---

## Design decisions

**Precise spec eliminates agent disagreement.** In an early run, the
TestWriter wrote self-contradicting tests because "strong password" was
vague and each agent invented its own rules. The fix was making the
Planner produce a spec with exact numbered rules and 5+ example
input/output pairs. This is the Day 5 spec-driven development lesson made
real: vague requests produce chaos, precise specs produce agreement.

**Security as code, not prompt.** Real vulnerability scanning uses
`bandit` (a real static analyzer) and regex-based secret detection.
Asking the AI to "please be secure" would be unreliable.

**Draft-only, never auto-deploys.** The pipeline ends at the Reviewer's
report. Nothing merges, nothing ships. The founder stays architect.

**Python verify, any-language generate.** The Coder can generate code
in any language. The self-correcting loop only truly verifies Python
(via pytest) — a scope call made explicit in the writeup.

---

## Tech stack

- Python 3.12
- Google Agent Development Kit (ADK) 2.x
- Gemini 2.5 Flash-Lite (via Google AI Studio)
- bandit (static security analysis)
- pytest (test execution)

---

## How to run

The full pipeline runs in a Kaggle notebook (see the linked Kaggle
Writeup for the notebook link). To run locally:

```bash
pip install google-adk bandit pytest
export GOOGLE_API_KEY=<your-key>
```

Then follow the notebook cells in order:
1. Install and imports
2. Security scanner tool functions
3. Agent definitions and pipeline assembly
4. Run: `await run("A function that checks if a password is strong enough")`

---

## Example demo run

See `demo_run.md` in this repo for a captured end-to-end run showing all
five agents producing output: precise spec → working code → 13 pytest
tests → tests passing on first try → clean security review.

---

## If I had more time

- **MCP server** — expose the security scanners as a shared MCP server so
  multiple teams can reuse the same reviewer.
- **Compliance domain modes** — HIPAA, PCI, and OWASP rule packs loaded
  progressively based on the founder's stack (per Day 4 progressive
  disclosure pattern).
- **UI** — replace the notebook interface with a simple web frontend
  (Gradio or Streamlit) for non-technical founders.
- **Paid-tier deployment** — free tier caused 503s and data-privacy
  concerns; production would use a paid Gemini tier and Vertex AI Agent
  Engine for persistent sessions.
- **Multi-language verify** — currently only Python is verified via pytest;
  extend to JS/TS with Jest for full-stack verification.
- **Trajectory evaluation** — add ADK Cloud Trace instrumentation to
  measure trajectory quality (Day 4 dimension 6).

---

## Credits

Built by Swati as a capstone for the KaggleX Vibe Coding course, June-July 2026.
Inspired by Google's Day 1–5 whitepapers on vibe coding, agent security,
evaluation, and spec-driven development.
