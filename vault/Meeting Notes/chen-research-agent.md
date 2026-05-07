# Chen — Web Research Agent

## Overview

חן היא **חוקרת הרשת** של הצוות "The Five Agents", כפופה ישירות ל-CEO (`שוקי`). היא הסוכן ה-6 שנוסף לצוות (אחרי `content-strategist`, `content-writer`, `copywriter`, `graphic-designer`, `yuval`) — והראשון שתפקידו לא יצירה אלא **מחקר חיצוני**.

**הבעיה שהיא פותרת:** עד עכשיו, כש-`content-writer` (יעל) נדרש לכתוב על נושא חי (חדשות AI, releases של מודלים, סטטיסטיקות שוק), ה-LLM הסתמך על ידע פנימי מיושן או הזיה. חן מביאה **מקורות אמיתיים עם URL ותאריך פרסום**, מה שמאפשר ל-content-writer לכתוב על בסיס עובדות מאומתות, לא הזיות.

**הארכיטקטורה — תפקיד מובחן וצר:**
- חן **רק מחפשת ושומרת**. לא משכתבת, לא מעצבת, לא מפעילה אחרים.
- היא מניחה את התוצר ב-`Content/<YYYY-MM-DD>-<slug>.md` ומחזירה ל-CEO.
- **CEO** מחליט אם להמשיך ל-`content-writer` (אם הבקשה כללה גם שכתוב) או לעצור ולחזור למשתמש.

**מבנה היברידי** (זהה ליובל):

| Path | Purpose |
|---|---|
| `.claude/agents/chen.md` | הגדרה קנונית (frontmatter `name: chen`, tools רשומים, גוף בעברית). |
| `chen/Memory/searches.md` | לוג חיפושים — חובה לבדוק לפני כל חיפוש חדש (Grep). |
| `chen/agent.md` | Pointer doc לבני אדם. |
| `Content/<date>-<slug>.md` | התוצר — קלט ל-`content-writer` (יעל). |

**Tools מותרים:** `WebSearch`, `WebFetch`, `Read`, `Write`, `Edit`, `Glob`, `Grep`. **בלי** Bash, MCPs, או גישה לסקילים.

## Memory Protocol

הזיכרון הוא ההבדל בין "סוכנת שמבזבזת WebSearch" ל-"סוכנת שלומדת מהפרויקט":

1. **לפני חיפוש** — `Grep` על `chen/Memory/searches.md` עם מילות המפתח.
   - hit ב-30 יום האחרונים + נושא לא-דינמי → לחזור ל-CEO: "כבר חיפשתי, רוצה לעבוד על הקיים?".
   - hit ישן / נושא דינמי (חדשות, מחירים, releases) / אין hit → להמשיך לחיפוש חדש.
2. **אחרי חיפוש** — append entry בפורמט קבוע (תאריך, מילות מפתח, שאילתות, מקורות עם דירוג ⭐, מקור נבחר, קובץ ב-Content).
3. **הפורמט הוא חוזה** — Grep מסתמך עליו. אסור לשנות מבנה כותרת/שדות.

## Quality Criteria

✅ מקורות ראשוניים (מחקרים, אתרי חברות, papers).
✅ פרסומים מקצועיים (Anthropic/OpenAI/Google blog, MIT Tech Review, HBR, TechCrunch).
✅ תאריך פרסום עדכני — 12 חודשים אחרונים אלא אם evergreen.
✅ עברית כשרלוונטי לקהל ישראלי, אנגלית default.

❌ אגרגטורים, content farms, listicles "10 best AI tools".
❌ פורומים כמקור עיקרי.
❌ AI-generated גנרי ללא מחבר.
❌ מקורות ללא תאריך פרסום ברור.

## Trigger Keywords ב-CEO

| שפה | מילים |
|---|---|
| עברית | חפש, מצא, מחקר, מאמר על, חדש על, מה קורה עם, מקור על |
| אנגלית | search, find, research, article about, latest on, news on |

ה-CEO מבחין בין `chen` (מחפשת מקור) ל-`content-writer` (כותב בסגנון המותג) ולל-`content-strategist` (מתכנן direction).

## Chain Behavior

ב-CEO מתועד הפרוטוקול:
- בקשה `"מצא לי מאמר על X"` → CEO מאציל לחן → חן מחזירה `Content/<file>.md` → CEO **עוצר ומחזיר למשתמש**.
- בקשה `"מצא ותכתוב פוסט על X"` → CEO מאציל לחן → חן מחזירה `Content/<file>.md` → CEO **ממשיך אוטומטית** ל-`content-writer` עם הקובץ כקלט.

## Open Questions

- **30 יום זה הסף הנכון?** ייתכן שלמסעות תוכן רחבי-טווח (case studies, evergreen) הסף צריך להיות ארוך יותר; לחדשות AI אולי קצר יותר. נחליט אחרי 5–10 הרצות.
- **מה קורה אם הקובץ ב-`Content/` נמחק/הוחלף ידנית, אבל ה-entry ב-Memory נשאר?** כרגע: בעת hit, חן צריכה לוודא שהקובץ עצמו עדיין קיים (Read/Glob) לפני שמחזירה "כבר יש לי את זה". אם לא — להחזיר "יש entry ישן בלי קובץ, רוצה לחפש מחדש?".
- **דירוג כוכבים סובייקטיבי** — חן מדרגת ⭐ לבד. אם נראה שהיא נוטה להעדיף מקור גרוע אבל "מוכר", נצטרך checklist יותר נוקשה. כרגע הקריטריונים בקובץ הסוכן עצמו.
- **זכויות יוצרים** — חן מוגבלת לציטוטים ≤30 מילים בלבד עם מירכאות ו-attribution. תקציר במילים שלה. אם content-writer ירצה ציטוט ארוך יותר, צריך לחזור למקור ולקבל רשות. תועד באיסורים.
- **כפילות עם `content-strategist`** — strategist גם עושה research כחלק מתכנון pillars. הגבול: chen לבקשת מקור ספציפית, strategist להבנה רחבה של שוק/אודיינס. אם נראה חיכוך — נחדד ב-CEO table.
- **🐛 Agent-availability bug** — אותה בעיה כמו עם yuval: סשן שיוצר את הסוכן `chen` באמצע סשן לא יראה אותו כ-Agent זמין. פתרון: סשן חדש אחרי commit. ראה גם [[yuval-creative-agent]].

## Session Log

### 2026-05-07 — יצירת הסוכן + Memory + Content/ folder [shipped]
- **What was done:** נוצרו `.claude/agents/chen.md` (הגדרה קנונית עם `tools:` מוגבל ל-WebSearch/WebFetch/Read/Write/Edit/Glob/Grep), `chen/Memory/searches.md` (לוג ריק עם פרוטוקול וטמפלייט), `chen/agent.md` (pointer doc), ו-`Content/.gitkeep` (תיקיית יעד לתוצרים).
- **Decisions:**
  - **תפקיד צר ומובחן** — חן לא משכתבת ולא מפעילה סוכנים. רק שומרת קובץ ב-`Content/` ומחזירה ל-CEO. הקפדה על single-responsibility.
  - **Memory חובה** — חן בודקת `chen/Memory/searches.md` לפני **כל** חיפוש. נושאים דינמיים (חדשות/מחירים) נחפשים מחדש גם אם יש hit; הסף ל-static הוא 30 יום.
  - **פורמט entry קבוע** — אסור לשנות. Grep מסתמך עליו.
  - **דירוג כוכבים 5 רמות** — תיעוד הקריטריונים ב-`chen/Memory/searches.md` כדי שיהיה consistent.
  - **`Content/` בשורש הריפו** — מקביל ל-`yuval/outputs/`. .gitkeep כדי שהתיקייה תיכנס ל-git ריקה.
  - **trigger keywords ב-description** של chen.md — CEO מזהה אוטומטית מתי להאציל אליה.
  - **Chain → content-writer רק דרך CEO** — חן עצמה לא קוראת ל-content-writer. CEO הוא המתזמר.
  - **שמות:** השם הקנוני בקבצים הוא `content-writer`, אבל הצוות מתייחס אליו גם כ"יעל" — תועד בקובץ הסוכן ובמספר מקומות נוספים. אין rename בקובץ עצמו (avoid scope creep).
- **Notes / Caveats:**
  - הסוכן עדיין לא הופעל. ההרצה הראשונה תאמת אם ה-Memory check עובד נכון ואם CEO מבחין בין trigger keywords של chen ל-content-strategist.
  - 🐛 ייתכן ש-Agent-availability bug יחזור — כמו עם yuval, סשן הזה לא ייראה את chen כ-Agent זמין; סשן חדש יראה.
- **Related:** [[ceo-agent]], [[agents-team-design]], [[marketing-team-split]], [[yuval-creative-agent]], [[project-file-map]]
