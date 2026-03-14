# fixmemory

> **Fixing the key problem in AI: LLM agents that retrieve the right fact and still lie — a protocol for provenance-pinned generation that treats memory as a constraint on output, not just context fed into it.**

---

## The Problem

Current agent memory systems (e.g. OpenClaw, Claude Code auto-memory) solve *storage* and *retrieval* well. What they do not solve is *grounding* — ensuring that what the agent says is actually traceable to what it retrieved.

Memory today is **additive context**: facts are fetched and placed in the prompt, then the LLM generates freely, silently blending retrieved facts with parametric weights. This produces confident, specific, blatant lies — even when the correct fact was already retrieved.

---

## Three Failure Modes

### 1. Empty retrieval → silent hallucination
When `memory_search` returns nothing, the model interprets silence as permission to generate from weights. There is no closed-world fallback — no "I don't have this" state. The LLM fills the gap confidently.

### 2. Retrieved fact → ignored in generation
The correct fact can be present in context and still not appear in the output. Nothing compares the generated response against what was retrieved. Drift goes undetected.

### 3. Stored ≠ true
Anything written to memory is retrieved with full confidence. There is no write-time or read-time validation. False memories are cited with the same authority as true ones.

---

## The Missing Concept

**Provenance-pinned generation** — every claim in a generated output must map to a specific retrieved memory chunk, or be explicitly flagged as:
- uncertain (from parametric weights, unverifiable)
- absent (not in memory, agent should abstain or ask)

| Epistemic State | Source | Current Behavior | Required Behavior |
|---|---|---|---|
| Grounded claim | Retrieved memory chunk | Blended silently with #2 | Cited, traceable |
| Parametric claim | LLM weights | Treated as equally valid | Flagged as unverified |
| Unknown | Neither | Falls through to #2 | Explicit abstention |

---

## The Core Architectural Shift

> Memory must be a **constraint on generation**, not just an input to it.

This requires three additions current systems lack:

1. **Citation-tagged generation** — each factual claim in the output is linked to the memory chunk that grounds it
2. **Post-generation grounding check** — a verification pass after generation that audits claim→chunk alignment and flags ungrounded assertions
3. **Closed-world abstention mode** — when memory_search returns nothing relevant, the agent signals absence rather than falling back to weights silently

---

## What This Project Aims to Build

- A formal spec for provenance-pinned memory interaction (retrieval → generation → verification)
- A lightweight grounding verifier that runs post-generation
- An abstention protocol: structured output for "not in memory" vs "uncertain" vs "grounded"
- Reference integrations for OpenClaw and Claude Code memory formats
- Evaluation harness: given a memory corpus and a query, measure hallucination rate vs grounding rate

---

## Status

Manifest / early design phase. Contributions and critique welcome.
