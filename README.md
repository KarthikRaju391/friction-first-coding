# Friction-First Coding

An AI skill that gates developers from scaling their codebase until they can articulate its architectural pattern.

## Philosophy

Most AI coding tools optimize for speed. This one adds friction — but only at the moment it matters most: **before you scale.**

The idea is simple:
- Build a basic version of your system
- Discover the pattern that holds it together
- Prove you can articulate it
- Then let AI go fast

If you can't describe how your system works, you can't debug it, extend it, or survive without the AI. This skill makes sure you can.

## How It Works

The skill tracks two kinds of state:

1. **Project mode (informational):** `exploring` or `scaling`
2. **Pattern status (enforced):** each pattern is independently `candidate`, `established`, or `drifting`

| Pattern Status | What Happens |
|---|---|
| **candidate** | Scale moment detected for that pattern. Interview starts. |
| **established** | Pattern is articulated and documented. AI can move fast in that area. |
| **drifting** | Pattern changed over time; re-interview before more scaling in that area. |

This is the key behavior: one established pattern must **not** disable gating for new patterns.

### Scale Moment Triggers

The skill activates when it detects you're about to scale without understanding. It watches for:

- **Scope expansion**: "Now do the same for...", "Build out the rest of the endpoints"
- **Repetition without understanding**: Asking AI to clone patterns you haven't articulated
- **Lost developer signals**: "How does this work again?", asking AI to debug what it just built
- **Speed over understanding**: "Just make it work", "I don't care how"
- **Structural shifts**: New modules, repeated patching in the same area

### The Pattern Interview

When triggered, the skill interviews you — one question at a time:

1. "What is the smallest complete loop in this system?"
2. "Name the pattern in one sentence."
3. "What are the core components? What does each one own?"
4. "If we add a new feature, what files change first, and why?"
5. "What must never break?"
6. "What's explicitly NOT part of this pattern?"

It pushes back on vague answers. It won't let you off the hook.

### Pattern Briefs

A codebase has multiple patterns. Each gets its own file under `docs/patterns/`:

```
docs/patterns/
  channels-and-events.md
  authentication.md
  message-persistence.md
```

The gate checks whether the **relevant pattern** for the current change has been established. Each brief is written in **your words** (not the AI's):

- Pattern name
- Core components + ownership
- Primary flow (end-to-end)
- Extension recipe ("to add a new feature, you typically...")
- Invariants (rules that must never break)
- Boundaries (what's in vs out)
- File map (key files and why they matter)
- Two examples of features mapped to the pattern

## Installation

```bash
amp skill add KarthikRaju391/friction-first-coding
```

Or clone and install locally:

```bash
git clone https://github.com/KarthikRaju391/friction-first-coding.git
amp skill add ./friction-first-coding
```

## Usage

The skill activates automatically when it detects scale moments. You can also trigger it explicitly:

- "Check my understanding"
- "Do I understand this codebase?"
- "Am I ready to scale?"

## State Tracking

The skill creates a state file at `./.friction-first/state.yml` in your project root.

The important rule: gating is based on the **relevant pattern's status**, not a single global phase. This allows many patterns to emerge over time (e.g., auth, eventing, persistence), each with its own lifecycle.

Example (see `state.example.yml` for a copy-paste starter):

```yaml
project_mode: exploring
patterns:
  channels-and-events:
    brief_path: docs/patterns/channels-and-events.md
    status: established
    last_verified: 2026-02-08
  authentication:
    brief_path: docs/patterns/authentication.md
    status: candidate
    last_verified: null
gates:
  active_pattern: authentication
  last_gate_reason: "Scaling auth without an established pattern brief"
  last_gate_at: 2026-02-18T15:10:00Z
```

## Origin

This skill grew out of a personal observation: AI compresses the time to build, but not the time to understand. The developer who builds a chat server and discovers the "channels and events" pattern can extend it in any direction. The developer who vibe-codes the same server can't debug it when it breaks.

The friction doesn't belong everywhere. It belongs at the moment before you scale.

## Related

- [writing-interviewer](https://github.com/KarthikRaju391/writing-interviewer-skill): Same philosophy applied to writing — interviews you instead of writing for you

## Contributors

- [Karthik Raju](https://github.com/KarthikRaju391)
- [Amp](https://ampcode.com)

## License

MIT
