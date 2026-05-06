# yael/reference/ — How to populate

This directory holds **examples of content written in your brand voice**. Yael reads 2–3 of these at the start of every session and extracts patterns from them: tone, sentence rhythm, vocabulary, paragraph structure.

## What to drop here

- Past blog posts you're proud of
- Newsletters / emails that performed well
- LinkedIn / social posts that "sound like you"
- Landing-page copy you've shipped
- Customer-facing docs in the right voice

**File format:** plain `.md` files. Filename can be anything (e.g., `2025-launch-blog-post.md`, `linkedin-post-ai-agents.md`). Yael reads them by `Glob *.md`.

## What NOT to drop here

- Internal documentation (architecture docs, vault notes) — these may have a different voice than your external content
- Drafts you're not happy with
- Translated content that doesn't represent your real voice
- Generic AI-generated content from other tools

## Quantity

- **0 files:** yael runs in fallback mode using `style-guide.md` only. Less accurate.
- **2–3 files:** sweet spot. Yael reads them all.
- **10+ files:** yael still only samples 2–3 at a time. Keep the strongest examples; archive the rest elsewhere if needed.

## Updating

When the brand voice evolves, replace the files. No need to update `style-guide.md` for every example shift — references override the abstract guide.

> This README is for humans. Yael skips it (she filters for content samples, not meta-docs).
