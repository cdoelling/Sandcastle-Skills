# Terminal Lights

A [Claude Code](https://claude.com/claude-code) skill that turns your Ghostty
terminal background into a status light for Claude: one color while it's
working, another when it's done, another when it needs you.

No more tabbing through five sessions to see which one is waiting on you —
glance at the color.

| State | Default color |
|---|---|
| Working | deep blue |
| Done | deep green |
| Needs your attention (permission prompt, waiting on input) | deep red |
| Idle / between sessions | your normal background |

**Requires Ghostty.** It uses an escape sequence (OSC 11) that Ghostty
supports and Terminal.app doesn't. If your team is on a different terminal
(iTerm2, kitty, WezTerm), the same idea works but the codes in `SKILL.md`
would need adjusting — this skill as shipped targets Ghostty only.

## Install

Claude Code loads skills from `~/.claude/skills/`. To install:

```bash
mkdir -p ~/.claude/skills/terminal-lights
curl -o ~/.claude/skills/terminal-lights/SKILL.md \
  https://raw.githubusercontent.com/cdoelling/Sandcastle-Skills/main/terminal-lights/SKILL.md
```

Or just copy [`SKILL.md`](./SKILL.md) into `~/.claude/skills/terminal-lights/SKILL.md`
by hand.

Restart Claude Code (or start a new session), then invoke the skill:

```
/terminal-lights
```

or just ask in plain language — "set up the status lights," "install the
background color hook," "add the traffic-light thing to my terminal." Claude
runs the actual setup live in your session, since it needs to write to your
own `~/.claude/hooks/` and `~/.claude/settings.json` — there's nothing to
`curl` and run standalone beyond the skill file itself.

## What happens when you run it

1. Claude checks you're actually in Ghostty and backs up your current
   `~/.claude/settings.json`.
2. It writes a small hook script and wires it into six Claude Code hook
   events (session start/end, prompt submit, tool use, notification, stop).
3. **It asks you if you want to keep the default colors or pick your own.**
   This is a real step in the install, not something you have to dig for —
   say a hex code or "keep the defaults" and you're done.
4. It cycles all three colors plus the reset once so you can confirm it's
   actually working before moving on.

## Changing your colors later

Just ask — "change my terminal lights colors," "make the attention color
orange." Claude edits the one hook script; nothing else needs to change.

## If a color gets stuck

Killing a session hard can leave the background changed. Clear it by hand:

```bash
printf '\033]111\007'
```

## Uninstall

Ask Claude to remove it, or by hand:

```bash
rm ~/.claude/hooks/terminal-lights.sh
printf '\033]111\007'
```

Then remove the six `terminal-lights.sh` hook entries from
`~/.claude/settings.json` (or restore from the `.pre-terminal-lights` backup
Claude made, if nothing else has touched your hooks since).

## License

MIT — see [LICENSE](../LICENSE) in the repo root.
