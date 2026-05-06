---
name: "GPT Image Gen"
description: "Generate an actual PNG image via the OpenAI Images API (gpt-image-2). Use whenever any agent needs to produce a real image file from a text prompt — yuval is the primary consumer, but copywriter / graphic-designer / content-strategist may invoke directly when a brief calls for visual output. Reads OPENAI_API_KEY from .env. Inputs: prompt (required), output_path (required, absolute), size (default 1024x1024), quality (default medium). Output: a PNG file at output_path; the skill verifies the file exists and has size > 0 before reporting success."
---

# GPT Image Gen

עטיפה דקה לקריאת **OpenAI Images API** (`POST /v1/images/generations`) שמייצרת קובץ PNG בודד. הסקיל לא מנהל זיכרון של סגנון, לא מתאם בין תמונות, ולא מקבל החלטות קריאייטיביות — אלה תפקידיו של הקורא (בד"כ הסוכן [yuval](../../agents/yuval.md)).

## מתי להפעיל

- כשנדרש קובץ PNG ממשי מתוך text prompt.
- כל סוכן יכול לקרוא לסקיל; בפועל **yuval** הוא הצרכן הראשי.

## מתי **לא** להפעיל

- כשרק נדרש brief / direction ויזואלי → `graphic-designer`.
- ל-image edit / variations / inpainting — לא נתמך כאן בסקופ הנוכחי.

## Inputs

| שם | חובה | ברירת מחדל | הערות |
|---|---|---|---|
| `PROMPT` | ✓ | — | מומלץ אנגלית. מותר עברית, אבל איכות התוצאה יורדת. |
| `OUT` (output path) | ✓ | — | נתיב מוחלט ל-`*.png`. הקורא אחראי על שם ייחודי. |
| `SIZE` | ✗ | `1024x1024` | גם `1024x1536` (פורטרט) ו-`1536x1024` (לנדסקייפ). |
| `QUALITY` | ✗ | `medium` | `low` / `medium` / `high`. |
| `MODEL` | ✗ | `gpt-image-2` | אפשר להעביר override (למשל `gpt-image-1`) אם ה-default מחזיר `model_not_found`. |

`OPENAI_API_KEY` נטען מ-`.env` שבשורש הפרויקט. **אסור** להדפיס את המפתח ל-stdout, ל-logs, או ל-console messages.

## Primary path — `curl` + `jq`

```bash
# Run from project root. Loads .env into a subshell so the key isn't exported globally.
( set -a; . ./.env; set +a; \
  PROMPT="$PROMPT" OUT="$OUT" SIZE="${SIZE:-1024x1024}" QUALITY="${QUALITY:-medium}" MODEL="${MODEL:-gpt-image-2}" \
  bash -c '
    payload=$(jq -nc \
      --arg model "$MODEL" \
      --arg p "$PROMPT" \
      --arg s "$SIZE" \
      --arg q "$QUALITY" \
      "{model:\$model, prompt:\$p, size:\$s, quality:\$q, output_format:\"png\"}")
    resp=$(curl -sS -X POST "https://api.openai.com/v1/images/generations" \
      -H "Authorization: Bearer $OPENAI_API_KEY" \
      -H "Content-Type: application/json" \
      -d "$payload")
    b64=$(printf "%s" "$resp" | jq -r ".data[0].b64_json // empty")
    if [ -z "$b64" ]; then
      echo "OpenAI error:" >&2
      printf "%s" "$resp" | jq -r ".error.message // ." >&2
      exit 1
    fi
    printf "%s" "$b64" | base64 --decode > "$OUT"
  ' )
```

נקודות מפתח:

- שימוש ב-`jq -nc --arg` לבניית ה-JSON — בטוח מול prompts עם quotes / newlines / unicode.
- ה-API key נטען בתוך subshell (`( ... )`) כדי שלא ידלוף לסביבת ההורה.
- אם התגובה לא מכילה `data[0].b64_json`, הסקיל מדפיס את `error.message` ל-stderr ויוצא עם exit code 1 — בלי לכתוב קובץ ריק.

## Python fallback (אם `jq` לא מותקן — נפוץ ב-Git Bash על Windows)

```bash
( set -a; . ./.env; set +a; \
  PROMPT="$PROMPT" OUT="$OUT" SIZE="${SIZE:-1024x1024}" QUALITY="${QUALITY:-medium}" MODEL="${MODEL:-gpt-image-2}" \
  python3 - <<'PY'
import os, json, base64, sys, urllib.request, urllib.error
body = {
    "model": os.environ["MODEL"],
    "prompt": os.environ["PROMPT"],
    "size": os.environ["SIZE"],
    "quality": os.environ["QUALITY"],
    "output_format": "png",
}
req = urllib.request.Request(
    "https://api.openai.com/v1/images/generations",
    data=json.dumps(body).encode("utf-8"),
    headers={
        "Authorization": "Bearer " + os.environ["OPENAI_API_KEY"],
        "Content-Type": "application/json",
    },
)
try:
    with urllib.request.urlopen(req) as r:
        payload = json.loads(r.read().decode("utf-8"))
except urllib.error.HTTPError as e:
    print("OpenAI HTTP error:", e.read().decode("utf-8"), file=sys.stderr)
    sys.exit(1)
b64 = (payload.get("data") or [{}])[0].get("b64_json")
if not b64:
    print("OpenAI returned no image:", json.dumps(payload), file=sys.stderr)
    sys.exit(1)
with open(os.environ["OUT"], "wb") as f:
    f.write(base64.b64decode(b64))
PY
)
```

## Verification (חובה לפני שמסיימים)

```bash
[ -s "$OUT" ] || { echo "no file or empty: $OUT" >&2; exit 1; }
file "$OUT" | grep -q "PNG image data" || { echo "not a PNG: $OUT" >&2; exit 1; }
```

הסקיל מסיים בהצלחה רק אם **שתי** הבדיקות עוברות. אם אחת נכשלת — return error ולא להדפיס "done".

## Error handling — תרחישי שגיאה צפויים

| סימפטום | סיבה סבירה | תיקון |
|---|---|---|
| `model_not_found` ב-response | `gpt-image-2` עדיין לא זמין בחשבון | קרא לסקיל עם `MODEL=gpt-image-1` (override). תעד ב-vault. |
| `invalid_api_key` | `.env` ריק / מפתח לא תקין | המשתמש ממלא את `OPENAI_API_KEY` ב-`.env`. |
| `billing_hard_limit_reached` | חרגת מהקרדיט | המשתמש מעלה את ה-limit ב-OpenAI dashboard. אסור לנסות שוב אוטומטית. |
| `jq: command not found` | אין `jq` (Git Bash on Windows) | עבור ל-Python fallback. |
| קובץ נוצר אבל גודלו 0 | פרסור b64 נכשל / API החזיר ריק | error מ-`error.message`; אסור לטעון שהפעולה הצליחה. |

## איסורים מוחלטים

- ❌ להדפיס את `$OPENAI_API_KEY` ל-stdout, ל-logs, ל-comments בקוד, או לכל מקום אחר.
- ❌ לכתוב קובץ ריק כש-API נכשל (חובה למחוק / לא ליצור).
- ❌ לבחור slug / שם קובץ לבד — זו אחריות הקורא.
- ❌ להפעיל retry אוטומטי על שגיאות billing / quota.
- ❌ להוסיף תוספות ל-prompt (style, negative prompts, וכו'). הסקיל עובר prompt as-is.

## חתימת קריאה מומלצת לסוכן

```bash
PROMPT="A black cat sitting on a green velvet sofa, soft window light, photorealistic, shallow depth of field" \
OUT="/Users/.../yuval/outputs/2026-05-06-black-cat.png" \
SIZE="1024x1024" \
QUALITY="medium" \
bash <skill-script>
```

הסוכן אחראי לוודא שה-`OUT` הוא נתיב מוחלט ושתיקיית האם קיימת.
