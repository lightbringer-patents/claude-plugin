# Lightbringer Plugin

Patent and invention-management workflows for the Lightbringer platform.

## What it includes

- **lightbringer-agent-skill** — mines company data for patentable problem-solution pairs, authors and submits invention disclosures, and handles Lightbringer review work (Report comments and priority-draft reviews).
- **Lightbringer MCP connector** — remote server at `https://mcp.lightbringer.com/mcp` providing tools to create, read, search, validate, update, and submit inventions, and to comment on and respond to reviews.

## Source of truth

The `skills/` tree is a verbatim mirror of [agent-plugin](https://github.com/lightbringer-patents/agent-plugin), the portable [Agent Plugins](https://agent-plugins.org) package for OpenAI and other registries. Make skill changes there first, then copy them here.

## Setup

Install the plugin. On first use you will be prompted to authenticate with Lightbringer via the MCP connector.

## Usage

Ask things like "run patent mining on our recent work", "turn this design doc into a disclosure", or "reply to the comments on the priority draft".
