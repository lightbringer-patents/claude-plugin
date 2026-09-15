# Lightbringer Plugin for Claude

Work with [Lightbringer's patent service](https://lightbringer.com) from your AI assistant. Lightbringer has qualified patent attorneys on its team providing advice, strategy assessment, novelty searches, FTO, drafting, filing and prosecution through professional engagements.

Automated feedback and professional service requests have separate lifecycles. `start_innovation_feedback` returns one `task_id`; `get_task_status(task_id)` returns the same status/progress/results contract. Poll while `queued` or `running`; stop at `succeeded`, `partially_succeeded` or `failed`, preserving successful findings and explaining per-analysis errors. `request_patent_preparation` returns an innovation ID/link and `outcome: requested | already_requested`, with no task ID. It does not confirm completed preparation or filing. There is currently no MCP endpoint for tracking professional-service milestones.

Tasks and findings expire 30 days after creation; reading does not consume them or extend retention. Use `list_tasks`, optionally filtered by `invention_id`, to recover a lost task ID in the connected organisation. Follow `next_cursor` even if access filtering returns an empty page; listing reports recorded status without polling. `delete_task` permanently removes the user’s task and findings when requested, in any execution state, with write consent. Deletion does not cancel the analysis, delete the innovation or withdraw a service request.

## Included

Version 1.1.0 targets the released innovation interface in Altair 4.7.0 and Phaenix 12.5.0. The live MCP tool catalog and prompts were verified on 2026-09-15. Update existing installations to use the renamed tools and these three workflow skills. Host-directory publication is separate from the repository release; see [distribution and validation](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md).

- **MCP connector:** `https://mcp.lightbringer.com/mcp`, with OAuth and organisation-scoped access.
- **innovation-capture:** identify, register and enrich innovations from a conversation, inventor interview or authorised source exploration.
- **patent-preparation:** request preparation of a selected innovation for patent filing and report the confirmed status and next steps. Refinement is optional; an explicit request does not require an automated feedback or revision cycle.
- **patent-review:** read and respond to Lightbringer report and patent-draft reviews, including comments, discussion and formal responses.

Registration completes when `register_innovation` saves an innovation description. The separate `request_patent_preparation` action requests patent preparation only when the user explicitly wants Lightbringer to patent that innovation. Ordinary capture never submits automatically. Registration uses the server's structured template and validates before saving in the same request. Validation errors leave nothing registered; successful saves return the ID/link and non-blocking warnings. Blocked saves are reported as pending registration. Agents cannot make payments.

## Install in Claude Code

```text
/plugin marketplace add lightbringer-patents/claude-plugin
/plugin install lightbringer@lightbringer
```

The connector uses OAuth 2.1 (Authorization Code + PKCE, S256) with Dynamic Client Registration. The server advertises its authorization server via RFC 9728 protected-resource metadata (`/.well-known/oauth-protected-resource`); on first use, clients prompt you to sign in to Lightbringer. Supported scopes are `mcp:read` and `mcp:write`.

Complete OAuth when prompted and select the organisation to connect. Existing record permissions apply. For local validation, run `claude plugin validate . --strict`, then `claude --plugin-dir .` to exercise the package.

This repository is Lightbringer's own marketplace. Inclusion in Anthropic's reviewed community catalog is a separate submission; inclusion in its curated official marketplace is Anthropic's decision. Claude Code installation does not itself establish availability in every Claude chat or managed workspace. Verify the three skills and connector in each target host.

## Usage

“Register the innovation we just discussed.” “Explore this project for potential innovations.” “Update our existing innovation description with this detail.” “I want Lightbringer to patent this innovation.” “Help me reply to the patent team's review.”

## Source and release

The `skills/` tree is a verbatim mirror of [agent-plugin](https://github.com/lightbringer-patents/agent-plugin). Make shared changes there first, then copy the entire tree here. Keep `.claude-plugin/` and `.mcp.json` metadata separate. See [the distribution guide](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md).
