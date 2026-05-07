# Chen — Pointer Doc

> **זה לא קובץ הגדרת סוכן.** Claude Code מגלה רק קבצים flat ב-`.claude/agents/`, לא תיקיות. ההגדרה הקנונית של חן יושבת ב-`.claude/agents/chen.md`. הקובץ הזה הוא תיעוד לבני אדם.

## מה חן עושה

חן היא **חוקרת הרשת** של הצוות "The Five Agents" — היא מוצאת מאמרים, מקורות ראשוניים, סטטיסטיקות וציטוטים אמיתיים מהאינטרנט באמצעות `WebSearch` + `WebFetch`, מסננת לפי קריטריוני איכות, ושומרת את התוצר כקובץ MD ב-`Content/<YYYY-MM-DD>-<slug>.md`. הקובץ משמש כקלט ל-`content-writer` (יעל) שמשכתב אותו בסגנון המותג.

**היתרון על LLM רגיל:** מידע עכשווי ומעודכן, מקורות אמיתיים עם לינקים, **ללא הזיות**.

**מה היא לא עושה:** לא מייצרת תמונות, לא משכתבת, לא מפעילה סוכנים אחרים. היא רק מכינה את הקרקע ומחזירה ל-CEO.

## איפה הקבצים

| מה | איפה |
|---|---|
| הגדרת הסוכן הקנונית (Claude Code reads this) | `.claude/agents/chen.md` |
| לוג כל החיפושים (Memory) | `chen/Memory/searches.md` |
| תוצרי מחקר (קלט ל-content-writer) | `Content/<YYYY-MM-DD>-<slug>.md` |

## איך משתמשים

המנכ"ל (`שוקי`) מאציל לחן בקשות מחקר. trigger keywords: "חפש", "מצא", "מחקר", "מאמר על", "חדש על", "מה קורה עם", "מקור על" / "search", "find", "research", "article about", "latest on", "news on".

**Flow:**
1. CEO מעביר לחן נושא + מילות מפתח + סוג מאמר רצוי.
2. חן בודקת ב-`chen/Memory/searches.md` אם יש hit ב-30 יום האחרונים.
3. אם אין — מבצעת WebSearch + WebFetch, מסננת, בוחרת מקור.
4. שומרת ב-`Content/<date>-<slug>.md`, רושמת ב-`Memory/searches.md`.
5. מחזירה ל-CEO: path + source + why-this-one.
6. CEO מחליט אם להמשיך אוטומטית ל-`content-writer` (אם הבקשה כללה גם שכתוב) או לעצור ולחזור למשתמש.

## תיעוד מלא

- [[vault/Meeting Notes/chen-research-agent.md]] — Overview + Architecture + Memory Protocol + Open Questions + Session Log.
- [[vault/Meeting Notes/ceo-agent.md]] — איך ה-CEO מאציל לחן ומפעיל את ה-chain → content-writer.
