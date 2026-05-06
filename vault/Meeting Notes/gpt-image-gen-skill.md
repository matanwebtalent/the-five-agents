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

- **`gpt-image-2` vs `gpt-image-1`** — המשתמש ביקש מפורשות `gpt-image-2`; ב-2026-05 לא בטוח שהמודל זמין בכל החשבונות. אם הקריאה הראשונה תחזיר `model_not_found`, התיקון: לקרוא לסקיל עם `MODEL=gpt-image-1` override (או לעדכן את ברירת המחדל בקובץ הסקיל). תיעוד מלא של ההחלטה כשתתבצע.
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
