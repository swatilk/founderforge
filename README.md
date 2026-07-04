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
