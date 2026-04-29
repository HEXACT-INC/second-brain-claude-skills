# Second Brain Skills

Ready-to-use skills that extend what Claude can do with your [Second Brain](https://brain.hexact.io).

A skill is a `SKILL.md` file that gives Claude specific instructions for a task. Drop it into your skills folder and Claude picks it up automatically. No code, no config.

## What's a skill?

Think of it as a prompt template with structure. Instead of re-explaining what you want every time, a skill tells Claude:

- What the task is
- What rules to follow
- What data to use from your KB
- How to format the output

Example: a "weekly review" skill that pulls your recent notes, meetings, and saved ideas, then writes a concise summary every Monday.

## How to use

1. Browse the `/skills` folder in this repo
2. Download the skill folder you want
3. Paste it into `~/.claude/skills/` (each skill gets its own subfolder with a `SKILL.md` inside)
4. Restart Claude. It will pick up the new skill automatically.

Each skill has its own README explaining what it does, example prompts, and any KB structure it expects.

## Available skills



*More coming. See [Contributing](#contributing) to add yours.*

## Skill structure

Each skill lives in its own folder:

```
skills/
  weekly-review/
    SKILL.md          # The skill itself (Claude reads this)
    README.md         # Human-readable docs, examples, setup
    examples/         # Optional sample prompts and outputs
```

The `SKILL.md` is what Claude actually uses. The `README.md` is for you.

## Contributing

Built a useful skill? We want it here.

1. Fork the repo
2. Create a new folder under `/skills` with your skill name
3. Include a `SKILL.md` and a `README.md` with example prompts
4. Open a pull request

Keep skills focused on one job. If it tries to do five things, split it into five skills.

## Requirements

- [Second Brain](https://brain.hexact.io) installed (macOS or Windows)
- Claude Desktop with the Second Brain MCP server connected

## License

MIT

---

Built by [Hexact](https://hexact.io)
