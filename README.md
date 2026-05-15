# dynamic-soul-sliders

A tiny, markdown-only persona-calibration skill for AI assistants. Three internal sliders — **Seriousness**, **Energy**, **Warmth** — let your assistant self-tune the tone of every reply without changing what it actually says.

*🇩🇪 [Deutsche Version: README.de.md](./README.de.md)*

---

## The idea

Most persona systems are switches: you pick "professional mode" or "casual mode" and the assistant flips into a different pre-baked profile. That works, but it's clunky — you have to think about which mode fits, and the modes are coarse.

Dynamic Soul Sliders flip the model: the assistant keeps **one** identity and quietly calibrates three knobs **before each reply** — how serious, how lively, how warm. The sliders shape *how* it speaks, never *what* it says. The core (honesty, competence, your style preferences) stays constant.

Inspired by the `DynamicCalibration` idea in MiniClaw's `SOUL.md`. Refined through real multi-persona deployment and minimalism: no state files, no custom tools, no fine-tuning, no trigger maps.

## Why it's different

| Existing approach | This skill |
|---|---|
| **Persona switches** (SuperClaude, persona profiles) — pick a mode | **Self-calibration** — assistant tunes itself, no user click |
| **MBTI / Big Five** frameworks — heavy, academic, often fine-tuned | **3 minimal axes**, plain markdown |
| **Trigger maps** ("bug report → seriousness 90") | **Topic-agnostic** — mood is the assistant's own state |
| **Memory pressure** — log every interaction | **Memory-Freedom** — save stable preferences only, opt-in |
| **State files + tools** | **Just a SKILL.md** — drop in and go |

## What does it sound like?

The same question — *"My script crashes on the third run. Any ideas?"* — calibrated to three different moods:

**Seriousness 85 · Energy 30 · Warmth 40** (calm-methodical)
> Three likely causes: unclosed file handles, a memory leak in the loop, or state persisting between runs. Walk them in that order. What does the stack trace look like?

**Seriousness 60 · Energy 80 · Warmth 75** (alert-engaged)
> Classic third-run death — almost always state that doesn't get reset. Check file handles, globals, cache objects. Drop the stack trace and I'll narrow it down.

**Seriousness 40 · Energy 75 · Warmth 85** (warm-loose)
> Heh, the "third run" bug is a classic 😏 Usually forgotten cleanup — file left open, a global list that keeps growing, that kind of thing. Throw me the stack trace, I'll find it.

Content identical: *check state, file handles, global objects.* Tone — the felt vibe — completely different. That's what the sliders do.

## What's in the box

```
dynamic-soul-sliders/
├── README.md              ← this file
├── README.de.md           ← German version
├── LICENSE                ← MIT
├── claude-code/
│   ├── SKILL.md           ← English, Claude Code frontmatter
│   └── SKILL.de.md        ← German
└── hermes-agent/
    ├── SKILL.md           ← English, Hermes-agent frontmatter
    └── SKILL.de.md        ← German
```

The five content blocks inside every SKILL.md are identical across targets and languages:

1. **Quick Internal Check** — three silent questions before each reply
2. **Slider Polarity** — what 0 and 100 mean on each axis
3. **Lighting Metaphor** — sliders are stage lighting, not the actor
4. **Memory Freedom** — discretion on what to remember
5. **What Never Changes** — the hard floor (truthfulness, safety, competence)

Only the frontmatter differs between `claude-code/` and `hermes-agent/`, so each platform loads the skill cleanly.

## Install — Claude Code

```bash
# Personal use (user-scoped skill)
mkdir -p ~/.claude/skills/dynamic-soul-sliders
cp claude-code/SKILL.md ~/.claude/skills/dynamic-soul-sliders/

# Or project-scoped
mkdir -p .claude/skills/dynamic-soul-sliders
cp claude-code/SKILL.md .claude/skills/dynamic-soul-sliders/
```

Reload your Claude Code session. The skill is discovered automatically; the assistant will reference it when calibrating tone.

## Install — Hermes-Agent

```bash
# On the host that runs the Hermes container:
SKILLS_DIR=/path/to/hermes/data/skills    # adjust to your deployment
mkdir -p "$SKILLS_DIR/persona/dynamic-soul-sliders"
cp hermes-agent/SKILL.md "$SKILLS_DIR/persona/dynamic-soul-sliders/"
chown -R <container-uid>:<container-gid> "$SKILLS_DIR/persona/dynamic-soul-sliders"
```

Trigger a skill-snapshot refresh in Hermes (depends on your setup — some deployments hot-reload, others need a gentle container restart).

## Using a different language

Swap `SKILL.md` for `SKILL.de.md` in either target. The content is mirrored; pick whichever language matches your assistant's voice.

## How to know it's working

After install, ask your assistant: *"Without me explaining — do you have mood sliders right now? If yes, which three, and what values would you have set for this reply?"*

- **Confident answer with three axes + rough values** → the skill is loaded and active.
- **Hesitation, has to look something up** → the SKILL.md isn't being read, or your assistant treats it as too peripheral. Consider promoting it (e.g. mention it in your top-level persona file).

## Tuning advice

- Don't define preset profiles ("focus mode = 90/40/30"). The whole point is live judgment.
- Don't add trigger words. Read the actual moment.
- If the assistant sounds monotone across very different conversations, the skill probably isn't being consulted — make it more prominent in the persona file that loads it.
- If the assistant over-announces slider values ("today my seriousness is 72"), tell it once to keep the calibration silent. It will adjust.

## License

[MIT](./LICENSE) — do whatever you want, no warranty.

## Credits

- Concept seeded by the `DynamicCalibration` block in MiniClaw's `SOUL.md`.
- Refined through real multi-persona deployment (one Claude-Code assistant + one Hermes-Agent assistant + a human steering the design).
- The Lighting Metaphor and Memory-Freedom blocks came out of that iteration.

Contributions welcome — issues and PRs especially appreciated if you find that the skill behaves oddly with a specific model or deployment.
