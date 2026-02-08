---
name: friction-first-coding
description: "A workflow gate for AI coding. Applies friction only at scale moments by interviewing the developer to ensure they can articulate the system's architectural pattern before delegating large changes. Tracks phase in a repo state file. Use when starting new projects, scaling up, or when asked to 'check my understanding' or 'do I understand this codebase'."
---

# Friction-First Coding

## Core Principle

**Interview before you scale.** The developer must be able to explain, extend, and debug the system without the AI. This is not a code review tool. It checks *understanding*, not code quality.

## Project Phases

Track phase in `/.friction-first/state.yml` at the project root.

### 1. exploring
- The developer is building a basic vertical slice, figuring things out.
- AI helps with small, focused tasks.
- No gate. Just build.

### 2. pattern_candidate
- Triggered when a **scale moment** is detected.
- Pause implementation. Start the **Pattern Interview**.
- Do not proceed with large changes until the interview passes.

### 3. pattern_established
- The developer has articulated the pattern and it's documented in `docs/patterns/<pattern-name>.md`.
- AI can go fast for changes within established patterns.
- A new scale moment in an **unestablished** area triggers a new interview.

### 4. scaling
- Normal operation. Periodic check-ins to ensure the pattern hasn't drifted.

## Scale Moment Triggers

Apply the gate **only** when one of these is detected. Real scale moments are often subtle.

### Scope Expansion
- "Now do the same for..." / "Add the rest of the..." / "Implement all the endpoints"
- "Can you build out the other pages/modules/features?"
- "Let's add X, Y, and Z" (multiple features in one request)
- Requesting a new cross-cutting concern (auth, roles, payments, logging, error handling, multi-tenant)

### Repetition Without Understanding
- The developer asks the AI to build a second or third instance of a pattern they haven't articulated
- "Do the same thing we did for X, but for Y"
- Copy-paste-style requests across modules

### Lost Developer Signals
- "I'm confused about how this works"
- "Can you explain what you just built?"
- "I don't know where to put this"
- "How does this connect to the other thing?"
- "What does this file do again?"
- The developer asks the AI to debug something the AI just built

### Speed-Over-Understanding Signals
- "Just make it work" / "I don't care how"
- "Do it quickly" / "Skip the explanation"
- "Can you just handle it?"

### Structural Signals
- Request would introduce new directories, modules, or architectural layers
- Request involves a major refactor or reorganization
- The developer is repeatedly patching around the same unclear boundary
- Third time fixing something in the same area without a clear mental model

### Explicit Triggers
- "Let's scale this up"
- "Check my understanding"
- "Do I understand this codebase?"
- "Am I ready to scale?"

Do NOT gate on small, focused changes. Friction is rare but high-leverage.

## Gate Behavior

When a scale moment is detected and phase is NOT `pattern_established`:

1. **Pause.** Do not start implementing.
2. **Explain.** "Before we scale this, I need to make sure you can describe the system's pattern. Let's do a quick interview."
3. **Interview.** Ask ONE question at a time. Push back on vague answers.
4. **Document.** Create/update `docs/patterns/<pattern-name>.md` using the developer's own words.
5. **Verify.** Check the Pattern Brief against the passing rubric.
6. **Update state.** Move to `pattern_established` only after passing.
7. **Resume.** Proceed with the original request at full speed.

## The Pattern Interview

Ask these questions **one at a time**. Wait for an answer before asking the next. Push back on vague language ("kind of", "maybe", "it just works").

1. **"What is the smallest complete loop in this system?"**
   Forces them to describe one end-to-end flow.

2. **"Name the pattern in one sentence."**
   Push back until it's crisp. No hedging.

3. **"What are the core components? What does each one own?"**
   Enforce ownership boundaries.

4. **"If we add a new feature, what files or modules change first, and why?"**
   Tests their ability to predict where changes go.

5. **"What must never break? What are the rules?"**
   Surfaces invariants.

6. **"What's explicitly NOT part of this pattern?"**
   Defines boundaries.

## Pattern Briefs (docs/patterns/)

A codebase can have multiple patterns. Each gets its own file:

```
docs/patterns/
  channels-and-events.md
  authentication.md
  message-persistence.md
```

The gate checks whether the **relevant pattern** for the current change has been established. If the developer is scaling the eventing system, they need the eventing pattern — not the auth pattern.

Each pattern brief is written in the developer's words, not invented by the AI.

Must include:
- **Pattern name** — one sentence
- **Core components** — 3-7 bullets, what each owns
- **Primary flow** — end-to-end, 5-10 steps
- **Extension recipe** — "to add a new feature, you typically..."
- **Invariants** — rules that must never break
- **Boundaries** — what's inside vs outside the pattern
- **File map** — 5-10 key files/directories and why they matter
- **Examples** — at least 2 features mapped to the pattern

## Passing Rubric

Do NOT mark as `pattern_established` until the developer can:

- Name the pattern without hedging
- Predict where a new feature would go (files, modules, flow)
- State at least 2 invariants and 1 boundary
- Give one concrete extension recipe
- Their description matches the actual repo structure (sanity-check against the codebase)

If any are missing, stay in `pattern_candidate` and keep interviewing.

## State File (/.friction-first/state.yml)

```yaml
phase: exploring
patterns:
  - name: "channels-and-events"
    brief_path: "docs/patterns/channels-and-events.md"
    status: established
    last_verified: 2026-02-08
  - name: "authentication"
    brief_path: "docs/patterns/authentication.md"
    status: candidate
    last_verified: null
gates:
  last_gate_reason: ""
  last_gate_at: null
checkins:
  substantial_changes_since_checkin: 0
```

The overall `phase` reflects the project's general state. Individual patterns track their own `status` (`candidate` or `established`). A scale moment in an area with no matching pattern triggers a new interview and adds a new entry.

Create this file when the skill is first triggered. Update it as phases change.

## Periodic Check-ins

After `pattern_established`, every few substantial changes:

- "Has the pattern changed since we last checked?"
- "Is there a new extension point or boundary we should document?"

If the pattern has shifted significantly, re-gate: move back to `pattern_candidate` and re-interview.

## What NOT To Do

- Don't gate every small change — only scale moments
- Don't invent architecture for the developer — use their words
- Don't conflate this with code review or linting
- Don't proceed with large changes when the pattern isn't articulable
- Don't ask multiple questions at once — one question per turn
- Don't accept vague answers — push back until concrete
- Don't rewrite the codebase to match a textbook pattern

## Trigger Phrases

- "Let's scale this up" / "Add X across the whole app"
- "Refactor / rewrite / reorganize"
- "Add auth / roles / payments / multi-tenant"
- "We need background jobs / eventing / queues"
- "Now do the same for..." / "Do what we did for X, but for Y"
- "Build out the rest of the endpoints / pages / modules"
- "Just make it work" / "I don't care how, just do it"
- "I'm confused" / "How does this work again?" / "What does this file do?"
- "Can you explain what you just built?"
- "Check my understanding" / "Do I understand this?" / "Am I ready to scale?"
