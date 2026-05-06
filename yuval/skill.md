# gpt-image-gen — Pointer Doc

> **זה לא קובץ הגדרת סקיל.** ההגדרה הקנונית יושבת ב-`.claude/skills/gpt-image-gen/SKILL.md`. הקובץ הזה הוא תיעוד לבני אדם.

## מה הסקיל עושה

מעטפת לקריאת **OpenAI Images API** (`POST /v1/images/generations`):

1. בונה payload JSON עם `model`, `prompt`, `size`, `quality`, `output_format=png`.
2. שולח ל-API עם `OPENAI_API_KEY` שנטען מ-`.env`.
3. מפענח את ה-`b64_json` חזרה ל-PNG בנתיב שביקש הקורא.
4. מאמת שהקובץ קיים, גודלו > 0, וזה אכן PNG.

## למה זה סקיל ולא קוד בתוך הסוכן

כדי שכל סוכן יוכל לצרוך — בעיקר יובל, אבל גם `copywriter` / `graphic-designer` / `content-strategist` כשהמשימה מצדיקה. הסקיל הוא thin wrapper; כל ההיגיון הקריאייטיבי נשאר אצל יובל.

## Inputs (env vars)

| שם | חובה | ברירת מחדל |
|---|---|---|
| `PROMPT` | ✓ | — |
| `OUT` | ✓ | — (absolute path) |
| `SIZE` | ✗ | `1024x1024` |
| `QUALITY` | ✗ | `medium` |
| `MODEL` | ✗ | `gpt-image-2` (עם fallback ל-`gpt-image-1` אם נדרש) |

## תיעוד מלא

- `.claude/skills/gpt-image-gen/SKILL.md` — הגדרת הסקיל המלאה (primary curl path + Python fallback + verification + error table).
- [[vault/Meeting Notes/gpt-image-gen-skill.md]] — Overview + Session Log.
