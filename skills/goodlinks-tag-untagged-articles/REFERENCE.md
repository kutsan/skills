# goodlinks-tag-untagged-articles: Reference

## `tags.json` contract

A single JSON object mapping each allowed tag to a description of what content it holds:

```json
{
  "tag-name": "Description of what this tag is for…",
  "parent/child": "Description…",
  "parent/child/grandchild": "Description…"
}
```

- Tag names may be hierarchical, using `/` as the path separator. Treat the hierarchy generically and honor whatever structure the descriptions imply. Do not assume nesting exists.
- Descriptions are the source of truth for what each tag means. Classify strictly against them.
- `tags.json` is owned by the user and versioned in the `kutsan/skills` repository. The installed copy of this skill is read-only, so new tags reach it only when the user commits them there.

## Classification rules

- **Closed set**: apply only tags from `tags.json` or approved through the new-tag flow.
- **Most specific wins**: when the hierarchy implies it, prefer the deepest tag that fits, and fall back to a parent only when no child applies.
- **Tiebreaker**: when two tags both fit, pick the one whose description most narrowly matches the article's primary topic. If still tied, prefer the more specific child.

## Body fetch and fallback

- Classify against the article body whenever its content can be read.
- If reading fails or returns no readable body, classify on the title plus any summary. Record the fallback against that link in the report so the user can sanity-check it.

## New-tag flow

"No clear fit" means the best candidate would need **meaningful stretching** of its description, not just imperfect overlap.

1. Decide whether a sensible new tag exists. If you cannot write a non-overlapping description for it, skip the article.
2. Present the proposed `{name, description}` with the article that motivated it, and wait for explicit approval.
3. On approval, use the new tag as that article's mapping in the confirmation step, and include the entry in the final report's JSON snippet.
4. Without approval, leave the article untagged and list it in the report.
