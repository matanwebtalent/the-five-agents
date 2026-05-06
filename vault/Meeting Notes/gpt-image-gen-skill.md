# gpt-image-gen Skill

## Overview

`.claude/skills/gpt-image-gen/SKILL.md` — סקיל שעוטף את **OpenAI Images API** (`POST /v1/images/generations`) ומחזיר קובץ PNG בנתיב שביקש הקורא. הסקיל הוא **thin wrapper בלבד**: בלי החלטות קריאייטיביות, בלי זיכרון סגנון — אלה תפקידיו של הקורא (בד"כ הסוכן [[yuval-creative-agent|yuval]]).

**Inputs (env vars):**
- `PROMPT` (חובה) — באנגלית מומלץ.
- `OUT` (חובה) — absolute path ל-`*.png`.
- `SIZE` — default `1024x1024`. גם `1024x1536` (פורטרט), `1536x1024` (לנדסקייפ).
- `QUALITY` — default `medium`. גם `low` / `high`.
- `MODEL` — default `gpt-image-2`. אפשר override (למשל `gpt-image-1`).

**שני מסלולי ביצוע:**
1. **Primary** — `curl` + `jq` (`jq -nc --arg` בונה JSON בטוח מול quotes/newlines/unicode).
2. **Fallback** — `python3` + `urllib.request` (חשוב ב-Git Bash על Windows שלרוב חסר `jq`).

**Auth:** `OPENAI_API_KEY` נטען מ-`.env` בתוך subshell (`( set -a; . ./.env; set +a; ... )`) — לא דולף לסביבת ההורה. **אסור** להדפיס את המפתח לאף לוג.

**Verification (חובה לפני success):** `[ -s "$OUT" ]` + `file "$OUT" | grep PNG`. אם אחת נכשלת — error, לא success.

**Error handling:** טבלת error codes מתועדת ב-SKILL.md (`model_not_found`, `invalid_api_key`, `billing_hard_limit_reached`, `jq missing`, empty file).

**הצרכן הראשי:** `yuval`. עקרונית — כל סוכן יכול לקרוא לסקיל אם המשימה מצדיקה PNG.

## Open Questions

- **Image edit / variations / inpainting** — לא נתמך כרגע. ב-OpenAI יש endpoint נפרד (`/v1/images/edits`). אם תידרש תמיכה — נרחיב את אותו סקיל ולא נכתוב סקיל חדש.
- **Rate limiting / cost guardrails** — אין כרגע. כל קריאה עולה קרדיטים ב-OpenAI. אם הצוות יתחיל לקרוא בכמויות, נצטרך soft cap (למשל "אסור > N קריאות ביום").
- **בחירת `output_format`** — כרגע hardcoded ל-`png`. אם נצטרך JPEG/WEBP נוסיף param.

## Session Log

### 2026-05-06 — יצירת הסקיל + הגדרת fallback פייתון [shipped]
- **What was done:** נוצר `.claude/skills/gpt-image-gen/SKILL.md` עם frontmatter (`name: "GPT Image Gen"`, description ארוך הכולל triggers וסיגנטורת קריאה), גוף בעברית עם בלוקי קוד מוכנים להעתקה: primary (`curl` + `jq`), fallback (`python3` + `urllib`), verification, error table, סעיף איסורים.
- **Decisions:**
  - **`MODEL=gpt-image-2`** כברירת מחדל (לפי בקשת המשתמש), עם override אפשרי ל-`gpt-image-1`.
  - **`jq -nc --arg`** במקום string-concat ל-JSON — בטוח מול prompts עם תווים מיוחדים.
  - **Subshell ל-source `.env`** כדי שמפתחות לא ידלפו לסביבת ההורה.
  - **שני מסלולים** (curl + python) כדי לתמוך גם ב-Git Bash על Windows שאין בו `jq`.
  - **Verification חובה** (`[ -s ]` + `file | grep PNG`) — בלי זה אסור לדווח success.
  - **לא להוסיף scripts נפרדים** — בלוקי הקוד inline ב-SKILL.md, פשוטים להעתקה.
  - **לא retry אוטומטי** על שגיאות billing/quota — שגיאה למשתמש, הוא מחליט.
- **Notes / Caveats:**
  - הסקיל לא הורץ אפילו פעם אחת ב-API אמיתי. ההרצה הראשונה תאמת אם המודל `gpt-image-2` קיים. אם לא — fix מהיר ל-`gpt-image-1` ו-Session Log חדש כאן.
  - `OPENAI_API_KEY` כבר קיים ב-`.env` (אומת ב-`.env:5`); המשתמש מילא ידנית. ב-`.env.example` יש placeholder ריק.
  - הסקיל לא יודע על קיום `yuval/outputs/` — הקורא אחראי על ה-path.
- **Related:** [[yuval-creative-agent]], [[env-setup]], [[ceo-agent]], [[project-file-map]], [[claude-config-structure]]

### 2026-05-06 — הרצה ראשונה: gpt-image-2 עובד [shipped]
- **What was done:** הסקיל הופעל בפעם הראשונה ב-API אמיתי. נוצרה תמונה ראשונה (`yuval/outputs/2026-05-06-webtalent-ai-for-business-feed.png`, 1024x1024, 1.4MB, PNG תקין) ב-~15 שניות. ה-fallback `gpt-image-1` לא הופעל — `gpt-image-2` חזר עם `data[0].b64_json` מלא.
- **Decisions:** משאירים `gpt-image-2` כ-default. מסירים את ה-Open Question על המודל. ה-fallback ל-`gpt-image-1` נשאר ב-bash code-block כסעד להמשך (חשבונות אחרים, downgrade עתידי).
- **Notes / Caveats:**
  - ההפעלה הראשונה רצה **מהסשן הראשי, לא מתוך הסוכן `yuval`** — ראה [[yuval-creative-agent]] לסיבה (ה-Agent tool של הסשן הראשי לא טען את yuval כי הסוכן נוצר אחרי תחילת הסשן). בקריאות הבאות (סשנים חדשים) yuval יקרא לסקיל ישירות כמתוכנן.
  - ה-`.env` של המשתמש מתחיל את שורת `OPENAI_API_KEY` ברווח מוביל (` OPENAI_API_KEY=sk-...`). bash sourcing התמודד עם זה ללא בעיה — עדיין שווה לתעד שזה לא מצב סטנדרטי.
  - jq זמין מקומית, ה-Python fallback לא נדרש בהרצה הזו.
- **Related:** [[yuval-creative-agent]], [[env-setup]]
