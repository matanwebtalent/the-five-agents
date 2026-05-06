# Env Setup

## Overview

ניהול משתני סביבה וסודות בפרויקט. הוקמו שלושה קבצים מתואמים:

- `.env.example` — תבנית מחויבת לריפו עם רשימת המפתחות הנדרשים (ערכים ריקים).
- `.env` — הקובץ המקומי של המפתח שמכיל את הסודות בפועל. **לא נכנס ל-git.**
- `.gitignore` — מסיר מ-git את `.env` וגם את `.claude/settings.local.json` (קובץ הרשאות מקומי של Claude Code).

המפתחות המוגדרים כרגע: `ANTHROPIC_API_KEY`, `OPENAI_API_KEY` (אופציונלי). יתווספו לפי הצורך.

## Open Questions

- האם נשתמש גם ב-`OPENAI_API_KEY` בפועל? יוחלט עם בחירת ה-stack של הסוכנים.
- האם נצטרך ניהול secrets מתקדם (1Password, Doppler) או `.env` מספיק לשלב הזה? ברירת מחדל — `.env` מקומי בלבד עד שיהיה צוות.

## Session Log

### 2026-05-06 — הקמת תבנית env ו-gitignore [shipped]
- **What was done:** נוצרו `.env` ו-`.env.example` עם `ANTHROPIC_API_KEY` ו-`OPENAI_API_KEY`. עודכן `.gitignore` כך ש-`.env` ו-`.claude/settings.local.json` לא נדחפים.
- **Decisions:** `.env.example` כן בריפו (תיעוד), `.env` לא (סודות). זה הסטנדרט.
- **Notes / Caveats:** המפתחות עוד ריקים מקומית — המשתמש יצטרך להזין `ANTHROPIC_API_KEY` כשיתחיל לכתוב קוד שצורך אותו.
- **Related:** [[repo-and-github]], [[project-file-map]]
