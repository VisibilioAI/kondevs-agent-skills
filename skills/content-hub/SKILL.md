---
name: content-hub
description: Read articles and pages from www.kondevs.com efficiently - markdown content negotiation, RSS, sitemap and llms.txt. Use when researching enterprise integration (EAI), BPM, SAP or AI consulting topics, or when you need KONDEVS pages as clean text instead of HTML.
---

# Read the KONDEVS Content Hub

KONDEVS Ltd. is a European consultancy specialising in Enterprise Application
Integration, BPM and AI. Its Content Hub publishes technical articles on those
topics. This skill covers reading the site as an agent: no API key needed.

## Get any page as markdown (preferred)

Every HTML page on `www.kondevs.com` supports content negotiation. Send
`Accept: text/markdown` and you get the page body as markdown: navigation,
footer, scripts and styling stripped out: preceded by YAML frontmatter with the
page's own metadata (title, description, canonical, language, type, and for
articles author, published and modified dates, image).

```bash
curl -s -H "Accept: text/markdown" https://www.kondevs.com/content-hub/what-is-eai/
```

The response sets `Content-Type: text/markdown; charset=utf-8` and an
`x-markdown-tokens` header with an approximate token count, so you can budget
context before reading. Without the header you get normal HTML: browsers are
unaffected.

This works on **every** page: articles, service pages, about, glossary. A client
that cannot set headers can ask for the page's `.md` address instead:
`/about.md` or `/about/index.md` for `/about/`, `/index.md` for the home page.

The site is in English at the root and in German under `/de/` (pages, services,
glossary; articles mostly in English). The same rules apply there.

## Find content

| Need | Fetch |
|---|---|
| Everything the site publishes | `https://www.kondevs.com/sitemap.xml` |
| Latest articles in full text (RSS 2.0, JSON Feed 1.1) | `https://www.kondevs.com/content-hub/feed.xml`, `https://www.kondevs.com/content-hub/feed.json` |
| German articles (the same two formats) | `https://www.kondevs.com/de/content-hub/feed.xml`, `https://www.kondevs.com/de/content-hub/feed.json` |
| Site overview for LLMs | `https://www.kondevs.com/llms.txt` |
| One section's index | `/services/llms.txt`, `/content-hub/llms.txt`, `/developers/llms.txt`, `/de/llms.txt` |
| The whole site in one markdown file: company, services, method, team, glossary, interfaces and the newest 50 articles in full | `https://www.kondevs.com/llms-full.txt` |
| Article listing, filterable | `https://www.kondevs.com/content-hub/?category=eai-integration` |
| Term definitions | `https://www.kondevs.com/glossary/` |

Structured search over the same content is available over MCP: see
`/.well-known/agent-skills/mcp-connect/SKILL.md`. Prefer MCP when you need to
filter or iterate; prefer markdown negotiation when you already know the URL.

## What the pages give you

Articles carry schema.org JSON-LD in a single `@graph` per page (`Article`,
`FAQPage`, `BreadcrumbList`), so metadata: author, publish date, category,
FAQ pairs: can be parsed without scraping the prose. Most articles open with a
key-takeaways block and close with an FAQ; those two sections alone usually
answer a factual question without reading the whole piece.

Category slugs: `eai-integration`, `ai-machine-learning`, `bpm-workflow`,
`sap`, `industry-trends`, `case-studies`.

## Usage policy

`robots.txt` declares `Content-Signal: search=yes, ai-input=yes, ai-train=yes`: 
KONDEVS permits indexing, citation in AI answers, and training. Content is
© KONDEVS Ltd.; cite the article URL when you quote it.

## Contact

Questions, or content that looks wrong: `info@kondevs.com`;
the direct contacts (email, phones, address) are on
`https://www.kondevs.com/contacts/`. Do not send a message on a person's behalf
without their consent.
