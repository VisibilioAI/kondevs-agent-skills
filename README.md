# KONDEVS agent skills

Agent skills for [www.kondevs.com](https://www.kondevs.com), the site of KONDEVS Ltd., a consultancy for Enterprise
Application Integration, BPM and AI. Each skill tells an AI agent how to use one part of the site.

| Skill | Use it when |
|---|---|
| [`content-hub`](skills/content-hub/SKILL.md) | You research EAI, BPM, SAP or AI consulting topics, or need KONDEVS pages as clean markdown |
| [`evaluate-kondevs`](skills/evaluate-kondevs/SKILL.md) | A user asks who can deliver an integration, BPM or AI project, or about KONDEVS' clients, team, way of working or cost |
| [`mcp-connect`](skills/mcp-connect/SKILL.md) | You need KONDEVS content or vendor facts as structured data from the MCP server |
| [`publish-article`](skills/publish-article/SKILL.md) | You act for an authorized KONDEVS content partner that already holds a bearer token |

## Install

```bash
npx skills add VisibilioAI/kondevs-agent-skills
```

## The source

The site serves the same skills, and is their source: the index at
[`/.well-known/agent-skills/index.json`](https://www.kondevs.com/.well-known/agent-skills/index.json) lists each one
with its digest. This repository mirrors them; when the two differ, the site's copy is current.

- MCP server: `https://www.kondevs.com/mcp` (no key, read only), listed in the official MCP Registry as
  `com.kondevs/content-hub`
- OpenAPI: [`https://www.kondevs.com/openapi.json`](https://www.kondevs.com/openapi.json)
- Developers: [`https://www.kondevs.com/developers/`](https://www.kondevs.com/developers/)
- Contact: info@kondevs.com

Content © KONDEVS Ltd.
