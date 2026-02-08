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

The skill tracks your project through four phases:

| Phase | What Happens |
|---|---|
| **exploring** | Build a basic vertical slice. No friction. |
| **pattern_candidate** | Scale moment detected. Interview starts. |
| **pattern_established** | You've articulated the pattern. AI goes fast. |
| **scaling** | Normal operation with periodic check-ins. |

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

The skill creates a small state file at `/.friction-first/state.yml` in your project root to persist phase across sessions. It also works with any agent CLI that can read YAML.

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
