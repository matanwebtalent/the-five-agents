# Yael — Pointer Doc

> **זה לא קובץ הגדרת סוכן.** Claude Code מגלה רק קבצים flat ב-`.claude/agents/`, לא תיקיות. ההגדרה הקנונית של יעל יושבת ב-`.claude/agents/yael.md`. הקובץ הזה הוא תיעוד לבני אדם.

## מה יעל עושה

יעל היא הסוכנת ה-6 בצוות שיווק/תוכן/קריאייטיב של "The Five Agents" — היא לוקחת מאמרי גלם בסגנון "אחר" מתיקיית `Content/` ומשכתבת אותם בסגנון הכתיבה של המותג. בשונה מ-`content-writer` שכותב from scratch לפי בריף, יעל **משכתבת** מאמר קיים.

## איפה הקבצים

| מה | איפה |
|---|---|
| הגדרת הסוכנת הקנונית (Claude Code reads this) | `.claude/agents/yael.md` |
| מדריך הסגנון | `yael/style-guide.md` |
| דוגמאות לטקסטים בסגנון שלנו | `yael/reference/*.md` |
| מאמרי גלם ממתינים לשכתוב | `Content/*.md` (root level) |
| מאמרים שיעל סיימה לעבד (ארכיון raw) | `Content/Ready/*.md` |
| תוצרים משוכתבים (עם `{{IMAGE_NEEDED}}` או תמונות סופיות) | `Output/*.md` |

## איך משתמשים

1. שמים מאמר גלם ב-`Content/<name>.md`.
2. אומרים ל-CEO (`שוקי`): "תשכתבי את `Content/<name>.md`" (או "המאמר הבא בתור").
3. שוקי מאציל ליעל. יעל קוראת את ה-style + ה-reference, משכתבת, ושומרת ב-`Output/`.
4. אם יעל זיהתה צרכי תמונה — היא משאירה `{{IMAGE_NEEDED: "..."}}` placeholders ב-output. **שוקי הוא זה שמפעיל את יובל** עבור כל placeholder ומחליף אותו ב-markdown של תמונה. יעל לא מפעילה את יובל ישירות (סאב-אייג'נטים ב-Claude Code לא יכולים להפעיל סאב-אייג'נטים אחרים).
5. אחרי הכל — שוקי מוחק את `Content/<name>.md` (יעל לא יכולה — אין לה Bash) ומחזיר תוצר סופי.

## איך מוסיפים reference

פשוט שומרים MD חדש ב-`yael/reference/<title>.md`. בקשה הבאה — יעל תקרא אותו ותלמד ממנו את הסגנון.

## איך מעדכנים את הסגנון

עורכים את `yael/style-guide.md`. השינויים נכנסים לתוקף בסשן הבא של יעל (היא קוראת אותו בתחילת כל סשן).

## תיעוד מלא

- [[vault/Meeting Notes/yael-rewriter-agent.md]] — Overview + Open Questions + Session Log.
- [[vault/Meeting Notes/yuval-creative-agent.md]] — הסוכן שמטפל ב-`{{IMAGE_NEEDED}}` placeholders (דרך המנכ"ל).
- [[vault/Meeting Notes/ceo-agent.md]] — הפרוטוקול שהמנכ"ל מפעיל לחיבור יעל ↔ יובל.
