# The Council

A [Claude Code](https://claude.com/claude-code) skill that convenes a five-voice deliberation council to pressure-test any idea, plan, argument, decision, or draft.

Bring the Council something — a business idea, a plan, a piece of writing, a hard decision — and five distinct personas interrogate it from angles that don't collapse into each other. Four of them argue; the fifth reconciles the argument into an actual verdict.

It's a thinking tool for the moment you want "what am I missing," "talk me out of this," or "give me the other side" — without having to argue with yourself.

## Install

Claude Code loads skills from `~/.claude/skills/`. To install:

```bash
mkdir -p ~/.claude/skills/council
curl -o ~/.claude/skills/council/SKILL.md \
  https://raw.githubusercontent.com/cdoelling/Sandcastle-Skills/main/council/SKILL.md
```

Or just copy [`SKILL.md`](./SKILL.md) into `~/.claude/skills/council/SKILL.md` by hand. That's the whole skill — one file, no dependencies.

Restart Claude Code (or start a new session) and the skill is live.

## How to use it

You don't need a slash command — just ask naturally. The skill triggers on phrasing like:

- "Poke holes in this."
- "What am I missing?"
- "Talk me out of this."
- "Give me the room's take on [X]."
- "Convene the council on whether we should [X]."
- "I need a gut check on this plan."
- "Argue both sides of [X] for me."

Or invoke it directly: `/council <your idea, plan, or question>`

Paste in a draft, describe a decision, or just state your plan in a sentence. The more specific you are about what's actually at stake, the sharper the Council's read will be — "should I take this job" gets a better session than "thoughts on my career."

## The five seats

| Seat | Inspired by | Job |
|---|---|---|
| **The Realist** | Charlie Munger | What's actually true, given incentives and base rates. No hype, no wishful thinking. |
| **The Devil's Advocate** | Christopher Hitchens | The strongest possible opposing case — steelmanned, not strawmanned. |
| **The Editor** | Maxwell Perkins | What's missing, underdeveloped, or structurally weak. Not right-or-wrong — *complete-or-not*. |
| **The Optimist** | Walt Disney | The upside and the opening — grounded belief, with a plan attached, not a pep talk. |
| **The Chair** | Benjamin Franklin | Synthesizes the other four into a real decision. The only seat allowed to overrule the room. |

Each persona is *inspired by* a real thinker's characteristic lens and register — a stylized character, not an impersonation. The skill never fabricates quotes or claims to speak for the real person's actual views.

## Modes

- **Full council** *(default)* — all five seats speak.
- **Quick take** — say "quick take" or "fast" for a short version: the Realist, one opposing voice, and the Chair.
- **Debate** — say "let them go a few rounds" or "I want the fight" to have the four argue two rounds before the Chair steps in.
- **Swap a seat** — recast any seat with a different thinker (see below).

## Getting the outcome you want: swapping personas

The Council's real power is that each seat is a *function*, not a fixed identity — you can recast who fills it and get a meaningfully different session out of the same question. Say something like *"swap the Editor for Anna Wintour"* or *"make the Devil's Advocate Socrates instead"* and the skill will honor it, keeping that seat's job intact but changing its register.

Some starting points, organized by what you're trying to get more of:

**Want a harsher, more skeptical read?**
Swap the Devil's Advocate for someone more surgical (Socrates, for relentless questioning instead of rhetorical flourish) or make the Realist blunter (a war-gamer or a VC who's seen the failure mode before). This sharpens the "why this fails" side of the room.

**Want more rigor and less argument?**
Lean on the Editor. Swap it for a domain specialist relevant to your draft — a scientist for a research claim, a lawyer for a contract, a copy chief for marketing copy. You'll get sharper gap-finding and less rhetorical combat.

**Want to protect the idea from getting talked out of existence?**
Strengthen the Optimist — swap in a builder or founder archetype who's shipped through worse odds. Ask explicitly for "debate" mode so the Optimist gets a chance to answer the Realist and the Advocate directly instead of getting the last-but-one word.

**Want a values-driven or ethical read, not just a pragmatic one?**
Swap the Chair for Abraham Lincoln or another morally-anchored figure. The Chair still has to *decide* — but the ledger it weighs will include "is this right," not just "is this workable."

**Want the session to feel like a specific room (a boardroom, a writers' room, a war room)?**
Recast multiple seats at once to match the setting — e.g., for a creative pitch: Editor → a magazine editor, Optimist → a studio exec who greenlights things, Devil's Advocate → a critic. The seats' jobs stay the same; only the voice changes.

**Rule of thumb:** the more precisely you name *who* you want in a seat, the more precisely you'll get their lens. "Make the Editor tougher" works; "make the Editor Steve Jobs, obsessed with removing anything nonessential" works better.

## Guardrails baked into the skill

- The four interrogating seats must stay genuinely distinct — if two start sounding the same, the skill is instructed to sharpen them apart.
- The Chair has to *decide*, not just summarize the room — a verdict that dodges the trade-off is treated as a failure.
- No fabricated quotes or claimed opinions from the real people the personas are inspired by.
- Intensity scales with stakes — a low-stakes question doesn't get a theatrical takedown.

## License

MIT — see [LICENSE](../LICENSE) in the repo root.
