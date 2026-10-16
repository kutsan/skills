---
name: goodlinks-tag-untagged-articles
description: Tag untagged GoodLinks articles with one tag each from the closed taxonomy in tags.json.
disable-model-invocation: true
---

# goodlinks-tag-untagged-articles

Tag links in the GoodLinks `untagged` list against [tags.json](tags.json), applying **exactly one** tag per article from a closed taxonomy.

## Preconditions

Stop and report which check failed. Do not attempt partial work.

1. **GoodLinks MCP tools are callable**: the official `goodlinks` server's tools for listing links in a list, reading article content, listing tags, and editing a link. If they are missing, tell the user to add the server (`/Applications/GoodLinks.app/Contents/MacOS/CLI/goodlinks mcp-server`) and confirm GoodLinks is running with **Settings > API** turned on.
2. **[tags.json](tags.json) is valid, non-empty JSON.** If not, point the user to the contract in [REFERENCE.md](REFERENCE.md). Never invent a taxonomy.

## Workflow

1. **Load tags**: parse `tags.json`, the closed allowed set. Optionally list live GoodLinks tags and note `tags.json` entries not used yet (GoodLinks creates them on first use).
2. **Fetch untagged**: list links in the `untagged` list, at most 20 per run. If more remain, report the count and tell the user to run again.
3. **Classify each link**:
   - Read its article content. If that fails, classify on title plus summary and note the fallback for the report.
   - Pick **exactly one** tag whose description best matches, preferring the **most specific** tag the hierarchy allows. See [REFERENCE.md](REFERENCE.md) for tiebreakers.
4. **No fit**: if nothing fits without stretching a description, either propose a new `{name, description}` and get explicit approval, or skip the article. Never force-fit. See the new-tag flow in [REFERENCE.md](REFERENCE.md).
5. **Confirm**: present the full mapping (title, URL, chosen tag or `skip` with a one-line reason) plus any approved new tags. Wait for explicit confirmation.
6. **Apply**: edit each agreed link to add its tag.
7. **Report**: tagged count by tag, skips with reasons, body-fetch fallbacks, failures, and untagged links remaining. End with any approved new tags as a JSON snippet for the user to add to `tags.json` in the `kutsan/skills` repository.

## Safety rails

- Apply only tags from `tags.json` or approved in step 4.
- Edit links only after explicit confirmation in step 5.
- One tag per article. Work only on the `untagged` list.
