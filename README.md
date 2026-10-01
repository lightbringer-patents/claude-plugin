# Lightbringer Plugin for Claude

Work with [Lightbringer's patent service](https://lightbringer.com) from your AI assistant. Lightbringer has qualified patent attorneys on its team providing advice, strategy assessment, novelty searches, FTO, drafting, filing and prosecution through professional engagements.

Automated feedback and professional service requests have separate lifecycles. `start_innovation_feedback` returns one `task_id`; `get_task_status(task_id)` returns the same status/progress/results contract. Poll while `queued` or `running`; stop at `succeeded`, `partially_succeeded` or `failed`, preserving successful findings and explaining per-analysis errors. `request_patent_preparation` returns an innovation ID/link and `outcome: requested | already_requested`, with no task ID. It does not confirm completed preparation or filing. There is currently no MCP endpoint for tracking professional-service milestones.

Tasks and findings expire 30 days after creation; reading does not consume them or extend retention. Use `list_tasks`, optionally filtered by `invention_id`, to recover a lost task ID in the connected organisation. Follow `next_cursor` even if access filtering returns an empty page; listing reports recorded status without polling. `delete_task` permanently removes the user’s task and findings when requested, in any execution state, with write consent. Deletion does not cancel the analysis, delete the innovation or withdraw a service request.

## Included

The package includes five workflow skills. Patent discovery uses `search_public_patents`; `import_patent` saves publications and automatically attempts family grouping for own patents. `refresh_patent_family` supports targeted updates to saved families when needed. Use the tools available in the connected MCP catalog.

- **MCP connector:** `https://mcp.lightbringer.com/mcp`, with OAuth and organisation-scoped access.
- **innovation-capture:** identify, register and enrich innovations from a conversation, inventor interview or authorised source exploration.
- **patent-preparation:** request preparation of a selected innovation for patent filing and report the confirmed status and next steps. Refinement is optional; an explicit request does not require an automated feedback or revision cycle.
- **patent-review:** read and respond to Lightbringer report and patent-draft reviews, including comments, discussion and formal responses.
- **patent-portfolio:** review saved applications and patent families, compare with public publications, carry out authorised imports or family updates, and read back the results. Structured family overviews depend on the connected service; family grouping does not update patent text or legal status.
- **ip-strategy:** develop and revise an actionable company, product or technology strategy, connecting business objectives, evidence, protection options and next actions. Save and maintain a Strategy through the connected tools when available; publication is a separate explicit action.

Strategy authoring requires the Strategy tools in the connected service. The assistant follows the current `get_strategy_template` guide, authors the document and uses revision-aware edits to preserve collaborators' changes. Importing the existing patent portfolio into Lightbringer is recommended to provide strategy context. Engaging Lightbringer to manage the portfolio is a separate service. See [distribution and validation](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md) for package release status.

Registration completes when `register_innovation` saves an innovation description. The separate `request_patent_preparation` action requests patent preparation only when the user explicitly wants Lightbringer to patent that innovation. Ordinary capture never submits automatically. Registration uses the server's structured template and validates before saving in the same request. Validation errors leave nothing registered; successful saves return the ID/link and non-blocking warnings. Blocked saves are reported as pending registration. Agents cannot make payments.

## Install

You need a Lightbringer account with access to the organisation you want to connect.

### Claude web, Desktop Chat and Cowork

Plugins are available on Claude Pro, Max, Team and Enterprise plans. To install from this repository:

1. Open **Customize → Plugins**. In Cowork, open the Cowork tab first.
2. Under **Personal plugins**, select **+ → Add marketplace → Add from a repository**.
3. Enter `https://github.com/lightbringer-patents/claude-plugin` and add the marketplace.
4. Select **Lightbringer** and install it, then complete the connection steps below.

You can also upload a complete Claude plugin package through the custom-plugin upload option. Team and Enterprise owners can distribute the plugin through an organisation marketplace. Availability depends on organisation policy. See Anthropic's [plugin installation guide](https://support.claude.com/en/articles/13837440-use-plugins-in-claude).

### Claude Code

```text
/plugin marketplace add lightbringer-patents/claude-plugin
/plugin install lightbringer@lightbringer
```

If the installation summary asks you to activate changes, run `/reload-plugins`. Use `/mcp` to inspect the Lightbringer connection and start authentication if needed.

### Connect to Lightbringer

The connector uses OAuth 2.1 (Authorization Code + PKCE, S256) with Dynamic Client Registration. The server advertises its authorization server via RFC 9728 protected-resource metadata (`/.well-known/oauth-protected-resource`); on first use, clients prompt you to sign in to Lightbringer. Supported scopes are `mcp:read` and `mcp:write`.

Complete OAuth when prompted and select the organisation to connect. Existing record permissions apply. Confirm that the Lightbringer connector and the skills included in your installed package appear in that host.

This repository is Lightbringer's own marketplace. Inclusion in Anthropic's reviewed community catalog is a separate submission; inclusion in its curated official marketplace is Anthropic's decision. Claude Code installation does not itself establish availability in every Claude chat or managed workspace. Verify the installed skills and connector in each target host.

## Usage

Ask naturally, or select an installed skill from the `/` or **+** menu in Claude chat and Cowork. In Claude Code, use the namespaced commands below:

| Workflow | Claude Code command | Example request |
| --- | --- | --- |
| Capture innovations | `/lightbringer:innovation-capture` | “Help me identify and register innovations in the work we’ve been discussing.” |
| Request patent preparation | `/lightbringer:patent-preparation` | “I want Lightbringer to prepare this innovation for patent filing.” |
| Respond to reviews | `/lightbringer:patent-review` | “Show the reviews awaiting my response and help me act on them.” |
| Import and refresh patents | `/lightbringer:patent-portfolio` | “Find the names our company files patents under and import our portfolio.” / “Import this publication.” / “Group these saved patents into families.” |
| Build or update an IP strategy | `/lightbringer:ip-strategy` | “Use our launch brief and saved innovations to build and save an IP strategy.” / “Update our existing strategy for the changed target market.” |

## Source and release

Version 1.3.1 adds directory metadata to the five workflow skills, including IP strategy authoring and saved-portfolio family reviews. Package preparation does not establish host publication or authenticated workflow verification; see [distribution and validation](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md).

The `skills/` tree is a verbatim mirror of [agent-plugin](https://github.com/lightbringer-patents/agent-plugin). Make shared changes there first, then copy the entire tree here. Keep `.claude-plugin/` and `.mcp.json` metadata separate. See [the distribution guide](https://github.com/lightbringer-patents/agent-plugin/blob/main/DISTRIBUTION.md).

Before release, validate both manifests and the skills explicitly:

```sh
claude plugin validate .claude-plugin/plugin.json --strict
claude plugin validate .claude-plugin/marketplace.json --strict
claude plugin validate skills --strict
```

Then run `claude --plugin-dir .` and exercise the bundled skills and OAuth connection. Verify installation and the workflows in each target Claude app before claiming compatibility; structural validation alone does not test authenticated behaviour.

For each package release, bump `version` in `.claude-plugin/plugin.json` and add an entry to [CHANGELOG.md](CHANGELOG.md). Claude Code uses this explicit version to detect updates; a new commit with the same version does not update existing installations. See [Anthropic's version management reference](https://code.claude.com/docs/en/plugins-reference#version-management). Claude package versions can advance independently for packaging changes while the shared skills remain identical to `agent-plugin`.
