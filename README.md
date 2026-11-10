# token-saver

A Claude Code skill that makes Claude burn far less of your usage limit.

## Why it works

Every reply re-sends the whole conversation. In real usage logs, about **97% of
tokens were cache reads**: the same context read again on every turn. So the
real cost is context size × number of turns, not the text you type.

This skill tells Claude to:

- **Take fewer turns:** batch independent tool calls into one message
- **Keep context small:** grep first, read only needed lines, cap shell output
- **Offload wide searches:** send them to a cheap Haiku subagent that returns only the answer
- **Reply short:** no recaps, no pasting back code it just wrote
- **Start fresh sessions:** suggest one when a task ends or context gets big, with a handoff note
- **Use cheaper models:** suggest Sonnet / Haiku and low effort for routine work

## Install

**Option A: as a plugin** (in Claude Code):

```
/plugin marketplace add punyawatt51/token-saver
/plugin install token-saver@token-saver
```

**Option B: copy the skill**

```bash
mkdir -p ~/.claude/skills/token-saver && curl -fsSL https://raw.githubusercontent.com/punyawatt51/token-saver/main/skills/token-saver/SKILL.md -o ~/.claude/skills/token-saver/SKILL.md
```

## Use

It kicks in when you mention usage, limits or tokens, or when a session gets
long. Run it any time with `/token-saver`.

To make it always on, add this line to `~/.claude/CLAUDE.md`:

```
Always follow the token-saver skill.
```

## Biggest extra win (do once)

Tool definitions from every connected MCP server and plugin are re-sent with
every reply. Disable the ones you don't use (`/mcp`, `/plugin`) and each message
gets cheaper.

## Uninstall

```bash
rm -rf ~/.claude/skills/token-saver
```

## License

MIT
