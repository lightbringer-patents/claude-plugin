# Lightbringer Plugin for Claude

Work with [Lightbringer's patent service](https://lightbringer.com) from your AI assistant. Lightbringer has qualified patent attorneys on its team providing advice, strategy assessment, novelty searches, FTO, drafting, filing and prosecution through professional engagements.

## Included

- **invention-capture:** register potential innovations from a technical conversation and enrich existing innovation records.
- **lightbringer-agent-skill:** explore authorised technical sources and collaborate on Lightbringer reviews.
- **lightbringer-patent-service:** connect, understand the service, request patent preparation, and continue professional work through available tools or the team.
- **MCP connector:** `https://mcp.lightbringer.com/mcp`, authenticated through OAuth.

Registration completes when `register_innovation` saves a innovation description. The separate `prepare_for_patent_filing` action requests patent preparation only when the user explicitly wants Lightbringer to patent that innovation. Ordinary capture never submits automatically. Current registration requires the server's innovation description schema; blocked saves are reported as pending registration. Agents cannot make payments.

## Install in Claude Code

```text
/plugin marketplace add lightbringer-patents/claude-plugin
/plugin install lightbringer@lightbringer
```

Complete OAuth when prompted and select the organisation to connect. Existing record permissions apply. For local validation, run `claude plugin validate . --strict`, then `claude --plugin-dir .` to exercise the package.

This repository is Lightbringer's own marketplace. Inclusion in Anthropic's reviewed community catalog is a separate submission; inclusion in its curated official marketplace is Anthropic's decision. Claude Code installation does not itself establish availability in every Claude chat or managed workspace. Verify the three skills and connector in each target host.

## Usage

“Register the innovation we just discussed.” “Explore this project for potential innovations.” “Update our existing innovation description with this detail.” “I want Lightbringer to patent this innovation.” “Help me reply to the patent team's review.”

## Source and release

The `skills/` tree is a verbatim mirror of [agent-plugin](https://github.com/lightbringer-patents/agent-plugin). Make shared changes there first, then copy the entire tree here. Keep `.claude-plugin/` and `.mcp.json` metadata separate. See [the distribution guide](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md).
