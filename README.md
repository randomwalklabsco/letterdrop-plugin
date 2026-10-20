# Letterdrop plugin for Claude Code

Find the accounts that are in a competitor's sales cycle right now, see who at
those accounts is involved, and check what your CRM already says about them —
so outreach and ad spend go to the companies that are actually in market.

The plugin bundles the Letterdrop MCP connector with three commands and two
skills, so answers respect what the underlying fields actually mean instead of
guessing from field names.

## Install

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install letterdrop@claude-community
```

Or from a shell:

```bash
claude plugin marketplace add anthropics/claude-plugins-community
claude plugin install letterdrop@claude-community
```

Then authenticate:

```
/mcp
```

Pick `letterdrop` and complete the browser sign-in. You need a Letterdrop
account; the connector reads the workspace you choose during authorization.

## Commands

| Command | What it does |
|---|---|
| `/in-market-accounts [filter]` | Accounts showing competitor buying signals now, ranked for outreach |
| `/account-brief <company>` | One account in depth: signals, committee, outreach history, CRM state |
| `/buyer-filters [change]` | Show the buyer definition that decides who counts as a valid lead; update it only on explicit confirmation |

You do not have to use the commands — plain language works too:

- "Find contacts in the US at companies between 500-5k employees that are not
  already open opportunities or customers that may be evaluating competitors"
- "Which accounts are evaluating competitors this quarter?"
- "Who's on the buying committee at Acme, and have we contacted any of them?"
- "Give me a CSV of high-priority accounts with no outreach since the signal."
- "Set up signal-based outreach for us." / "Push today's leads to the sequence."

## Skills

`competitor-monitoring` loads automatically when the conversation touches this
data. It carries the rules that decide whether an answer is true rather than
merely plausible: buying committee members produced no signal of their own,
attribution is a claim rather than an ordering of dates, and a stale signal
keeps its original priority.

`signal-based-outreach` turns those signals into a daily outbound routine. A
short questionnaire and an approved, per-persona template library come first;
after that each run shortlists new contacts, finds their email, personalizes
the approved template from verified data only, and adds them to an existing
sequence in your outreach tool, then posts a digest. It needs an outreach tool
(Smartlead, Apollo, HubSpot or similar) connected separately — this plugin
bundles only the Letterdrop connector — and it never sends email itself:
sending stays with the sequence. `review` mode enrolls only into a sequence
the tool reports as paused, so every lead waits for a person to release it,
and the first scheduled run is a dry run that enrolls nobody.

## Tools

The MCP server provides five tools:

| Tool | Access |
|---|---|
| `get_competitor_monitoring_table` | read |
| `get_competitor_monitoring_account_details` | read |
| `get_knowledge_base_buyer_filters` | read |
| `update_knowledge_base_buyer_filters` | write |
| `list_workspaces` | read |

## Authentication

OAuth 2.1 with PKCE against `api.letterdrop.com`; no API key to paste and no
credentials stored by the plugin. Grants carry `mcp.read` or `mcp.read
mcp.write` — a read-only grant is never offered the write tool.

## Development

```bash
claude --plugin-dir .          # load this plugin in a session
claude plugin validate .       # manifest, commands, skills
claude plugin validate . --strict
```

Evals live in `evals/`. Run them with `claude plugin eval .`.

## Links

- [Letterdrop](https://letterdrop.com)
- [Documentation](https://help.letterdrop.com/)
- [Privacy policy](https://letterdrop.com/privacy)
- Support: support@letterdrop.com

## License

MIT
