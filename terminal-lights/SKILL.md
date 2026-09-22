---
name: terminal-lights
description: Installs a Claude Code hook that changes your Ghostty terminal background color based on Claude's state — a working color while it's running, a done color when it finishes, and an attention color when it needs a permission or input. Use when the user asks to set up terminal status lights, background-color hooks, a visual "traffic light" for Claude, or invokes this skill by name ("/terminal-lights", "set up the status lights", "install the background hook", "change my colors").
---

# Terminal Lights

Ghostty lets a program set its own tab/window background via an escape sequence
(OSC 11). This skill wires that into Claude Code's hook events so each session's
background tells you its state at a glance:

| State | Event(s) | Default color |
|---|---|---|
| Working | `UserPromptSubmit`, `PostToolUse` | deep blue `#0b1f3a` |
| Done | `Stop` | deep green `#0b2e1a` |
| Needs attention | `Notification` | deep red `#3a0b12` |
| Idle / session boundary | `SessionStart`, `SessionEnd` | reset to your normal background |

This is **Ghostty-only** — it relies on OSC 11/111 support Ghostty has and
Terminal.app doesn't. iTerm2/kitty/WezTerm users can adapt the escape codes
below, but that's out of scope for this skill as written.

## Before you install: check the terminal

Run `echo $TERM_PROGRAM`. If it doesn't say `ghostty`, tell the user this skill
only supports Ghostty and stop — don't install it against a terminal it can't
control.

## Install steps

### 1. Back up existing settings

```bash
mkdir -p ~/.claude/hooks ~/.claude/backups
cp ~/.claude/settings.json ~/.claude/backups/settings.json.pre-terminal-lights 2>/dev/null || true
```

If `~/.claude/settings.json` doesn't exist yet, that's fine — the merge step
below creates it.

### 2. Write the hook script

Hooks run without a controlling terminal, so `/dev/tty` isn't reachable from
inside one. Walk up the process tree from the hook to the first ancestor that
owns a real tty (that's the `claude` process itself) and write the escape
sequence to that device instead. Run this exactly — it writes the file with
the factory-default colors baked in and makes it executable:

```bash
mkdir -p ~/.claude/hooks
cat > ~/.claude/hooks/terminal-lights.sh <<'SCRIPT_EOF'
#!/bin/sh
# Set the Ghostty background by Claude state: working | done | attention | reset
# Hooks have no controlling tty, so walk up to the first ancestor that owns
# one (the claude process) and write OSC 11 (set) / OSC 111 (reset) there.
# Silent no-op if none is found, or if the terminal isn't Ghostty.
case "$1" in
  working)   seq='\033]11;#0b1f3a\007' ;;
  done)      seq='\033]11;#0b2e1a\007' ;;
  attention) seq='\033]11;#3a0b12\007' ;;
  reset)     seq='\033]111\007' ;;
  *) exit 0 ;;
esac
cat >/dev/null 2>&1 || true   # drain hook JSON on stdin

pid=$$
while [ -n "$pid" ] && [ "$pid" -gt 1 ]; do
  t=$(ps -o tty= -p "$pid" 2>/dev/null | tr -d ' ')
  if [ -n "$t" ] && [ "$t" != "??" ]; then
    { printf "$seq" > "/dev/$t"; } 2>/dev/null
    exit 0
  fi
  pid=$(ps -o ppid= -p "$pid" 2>/dev/null | tr -d ' ')
done
exit 0
SCRIPT_EOF
chmod +x ~/.claude/hooks/terminal-lights.sh
```

The three `seq=` lines are the only thing that ever needs to change — see the
onboarding and reconfiguring steps below.

### 3. Merge the hooks into `~/.claude/settings.json`

Merge — never overwrite — so any existing keys (theme, permissions, other
hooks) survive. Read the file first, then merge in Python (or any JSON-safe
method) and write it back:

```python
import json
p = "/Users/<user>/.claude/settings.json"   # expand ~ for the actual user
try:
    s = json.load(open(p))
except FileNotFoundError:
    s = {}

def h(state):
    return [{"hooks": [{"type": "command", "command": f"$HOME/.claude/hooks/terminal-lights.sh {state}"}]}]

s.setdefault("hooks", {})
s["hooks"]["SessionStart"] = h("reset")
s["hooks"]["UserPromptSubmit"] = h("working")
s["hooks"]["PostToolUse"] = h("working")
s["hooks"]["Notification"] = h("attention")
s["hooks"]["Stop"] = h("done")
s["hooks"]["SessionEnd"] = h("reset")

json.dump(s, open(p, "w"), indent=2)
open(p, "a").write("\n")
```

If the user already has hooks bound to any of these six events for something
else, don't clobber them — append this command into that event's existing
hook array instead of replacing it, and say so.

### 4. Onboarding: ask about colors

This is a required step on first install, not optional flavor text. After
installing with the factory defaults, ask the user directly:

> Installed with the default colors — blue while working (`#0b1f3a`), green
> when done (`#0b2e1a`), red when I need your attention (`#3a0b12`). Want to
> keep these, or pick your own for any of the three?

If they want to change any, edit the corresponding `seq=` line in
`~/.claude/hooks/terminal-lights.sh` with their hex value(s) — no other file
needs to change. Keep colors dark enough that light terminal text stays
readable; if they give a light or saturated color, say so before applying it
and let them confirm or adjust.

### 5. Test

Cycle through all four states with a couple seconds between each so the user
can watch it change, then reset:

```bash
S=~/.claude/hooks/terminal-lights.sh
for st in working done attention reset; do echo '{}' | "$S" "$st"; sleep 2; done
```

Ask the user to confirm they saw the background cycle blue → green → red →
back to normal. If they didn't, the tty-walk in step 2 likely isn't finding
the right process — check `ps -o pid=,ppid=,tty=,comm= -p $$` up the chain and
adjust.

## Reconfiguring later

If the hook is already installed and the user just wants different colors,
skip straight to step 4 — don't touch settings.json or reinstall the hooks.

## Resetting a stuck background

If a session is killed hard and the color sticks, running this by hand clears
it:

```bash
printf '\033]111\007'
```

## Uninstalling

Remove the six hook entries this skill added from `~/.claude/settings.json`
(restore from `~/.claude/backups/settings.json.pre-terminal-lights` if that's
simpler and nothing else has touched hooks since), then:

```bash
rm ~/.claude/hooks/terminal-lights.sh
printf '\033]111\007'
```
