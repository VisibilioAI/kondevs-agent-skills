---
name: publish-article
description: Publish, update or delete an article on the KONDEVS Content Hub through the authenticated Publishing API. Use only when acting for an authorized KONDEVS content partner that already holds a bearer token.
---

# Publish to the KONDEVS Content Hub

The Publishing API creates and maintains Content Hub articles. It is **not
public**: every write requires a bearer token issued by KONDEVS. If you do not
already hold one, stop: request access at `info@kondevs.com` rather than
attempting calls.

- **Base:** `https://www.kondevs.com/api/content/`
- **Auth:** `Authorization: Bearer <API_KEY>` on every request
- **Machine-readable description:** `https://www.kondevs.com/openapi.json`

Two scopes exist. The write key permits create/update/delete and image
uploads (`POST /api/content/images`); a separate read-only key permits only
`GET /api/content/catalog` and `GET /api/content/tags`. Use the narrowest key
that does the job.

## Endpoints

```bash
# Publish
curl -X POST https://www.kondevs.com/api/content/ \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @article.json

# Update (replaces the article; see "What an update keeps")
curl -X PUT https://www.kondevs.com/api/content/my-article-slug \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @article.json

# Delete
curl -X DELETE https://www.kondevs.com/api/content/my-article-slug \
  -H "Authorization: Bearer $API_KEY"

# List what already exists
curl https://www.kondevs.com/api/content/catalog \
  -H "Authorization: Bearer $API_KEY_READ"
```

## Payload

```json
{
  "slug": "my-article-slug",
  "title": "Article Title",
  "author": { "name": "Author Name", "type": "Person" },
  "published_at": "2026-06-01T10:00:00Z",
  "updated_at": "2026-06-01T10:00:00Z",
  "status": "published",
  "content": {
    "body_html": "<p>Article body…</p>",
    "excerpt": "Short description (50-500 chars)",
    "reading_time_minutes": 5,
    "word_count": 1200,
    "key_takeaways": ["…"],
    "faq": [{ "q": "…", "a": "…" }]
  },
  "categorization": {
    "primary_category": "EAI & Integration",
    "tags": ["integration", "api"],
    "content_type": "article"
  },
  "seo": {
    "meta_title": "Article Title | KONDEVS",
    "meta_description": "50-200 characters for search engines",
    "canonical_url": "https://www.kondevs.com/content-hub/my-article-slug"
  },
  "social": { "og_title": "…", "og_description": "…" },
  "images": {
    "featured": { "data": "<base64>", "format": "png", "alt": "…", "width": 1200, "height": 630 },
    "og":       { "data": "<base64>", "format": "png", "alt": "…", "width": 1200, "height": 630 },
    "thumbnail":{ "data": "<base64>", "format": "png", "alt": "…", "width": 400,  "height": 300 }
  }
}
```

**Allowed `primary_category`:** `EAI & Integration`, `AI & Machine Learning`,
`BPM & Workflow`, `SAP`, `Industry Trends`, `Case Studies`.
**Allowed `content_type`:** `article`, `opinion`, `interview`, `analysis`, `comparison`.

## Rules that reject payloads

- `slug`: lowercase, hyphen-separated. It is the live URL and cannot be changed
  by an update; publishing a new slug creates a new article.
- `excerpt` 50-500 chars, `meta_description` 50-200 chars. Both are enforced.
- `body_html` is **sanitized server-side** against a tag and attribute allowlist.
  Scripts, event handlers and `javascript:` URLs are stripped: do not rely on
  them surviving. Semantic HTML only (`h2`: `h4`, `p`, `ul`, `ol`, `table`, `a`,
  `strong`, `em`, `code`, `pre`, `blockquote`, `img`). Send real HTML elements,
  never Markdown syntax inside paragraphs (`<p>## Heading</p>`, `<p>- item</p>`).
- Images: `data` is **raw base64 with no `data:` prefix**. Formats `png` or
  `jpeg` only (the bytes must match). Supply all three types, each at its own
  size: `featured` (article header), `og` (1200×630 social card), `thumbnail`
  (400×300 listing card). Sending one large image as all three makes every
  listing page heavy. They are stored in R2 and served from
  `/assets/images/content/{slug}/{type}.{format}`.
- POST to an existing slug conflicts: use PUT to change a published article.
- **What an update keeps.** A PUT replaces the stored article with your payload,
  with one exception: when the payload carries no machine-readable projection
  (`extensions.machine_readable.structured`), the stored `content.faq`,
  `content.key_takeaways` and `content.sources` stay wherever you leave that
  field out. Send the field to replace it, or an empty list to clear it. With a
  projection nothing stored is kept: each of the three is your own field when it
  is not empty, else what the projection's `aeo_assets` list.
- `POST /api/content/?dry_run=true` validates a payload and stores nothing.

## Quality expectations

Articles that pass validation can still be poor content. KONDEVS publishes for
practitioners: open with a concrete claim rather than a definition, include
`key_takeaways` (3-5 specific statements, not summaries of the summary) and an
`faq` (real questions a reader would search for). Both feed schema.org markup: 
`key_takeaways` renders as the article TL;DR, `faq` becomes `FAQPage` structured
data. Omitting them costs search and AI visibility.

## After publishing

Verify the live URL: `https://www.kondevs.com/content-hub/{slug}/`. Re-publishing
a deleted slug can serve stale images until the CDN cache expires. Within about
a minute (KV is eventually consistent) the article appears in `/sitemap.xml`,
the RSS feed, the catalog and the MCP `search_articles` tool.
