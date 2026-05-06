# Repo and GitHub

## Overview

הריפו: [matanwebtalent/the-five-agents](https://github.com/matanwebtalent/the-five-agents) (פרטי). חיבור דרך HTTPS עם credentials של `gh` CLI. ברנץ' יחיד עד כה: `main`.

ה-flow הנוכחי: עריכה לוקאלית → `git add` ספציפי → `git commit -m "..."` → `git push origin main`. אין עדיין PRs, branches, או CI — הכל נדחף ישירות ל-main בשלב השלד.

## Open Questions

- מתי עוברים לעבודה דרך feature branches + PRs? כנראה עם תחילת כתיבת קוד אמיתי.
- האם ננהל releases / tags? לא רלוונטי כרגע.
- האם להפעיל GitHub Actions לבדיקה אוטומטית של ה-vault (למשל לוודא שכל קובץ ב-`vault/` מקושר מ-`_index.md`)? אופציה לעתיד.

## Session Log

### 2026-05-06 — `git init` ראשוני וחיבור ל-GitHub [shipped]
- **What was done:** `git init -b main`, יצירת ריפו פרטי דרך `gh repo create the-five-agents --private --source=. --push`, push ראשון של `CLAUDE.md`.
- **Decisions:** פרטי, לא ציבורי. שם הריפו מותאם לשם הפרויקט.
- **Related:** [[claude-md]], [[env-setup]]

### 2026-05-06 — Push של שלד `.claude/` [shipped]
- **What was done:** commit שכלל `.claude/agents/.gitkeep`, `.claude/commands/.gitkeep`, `.claude/skills/.gitkeep`, `.gitignore`, ועדכון `CLAUDE.md`.
- **Related:** [[claude-config-structure]]

### 2026-05-06 — Push של `.env.example` [shipped]
- **What was done:** הוספת `.env.example` לריפו והוספת `.env` ל-`.gitignore`.
- **Related:** [[env-setup]]

### 2026-05-06 — Push של 14 סקילי Superpowers [shipped]
- **What was done:** הועתקו והודחפו 14 סקילים מ-`obra/superpowers`. commit `2dc9c95`.
- **Related:** [[superpowers-skills]]
