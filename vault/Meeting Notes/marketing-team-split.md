# Marketing Team Split

## Overview

ב-2026-05-06, הסוכן המאוחד "שיווק, תוכן וקריאייטיב" (שהיה אחד מ-4 הסוכנים הכפופים ל-CEO לפי [[ceo-agent]]) פוצל ל-**4 סוכנים שטוחים** מתחת ל-CEO:

| Slug | תפקיד | תחומי ליבה |
|---|---|---|
| `content-strategist` | אסטרטג תוכן | תכנון direction: pillars, calendar, audiences, journey, sales angles |
| `content-writer` | כותב תוכן | ארוך וערכי: בלוגים, מאמרים, אימיילים, case studies |
| `copywriter` | קופירייטר | קצר ומכירתי: headlines, ads, CTAs, sales pages |
| `graphic-designer` | מעצב גרפי | design briefs (לא מייצר קבצים — brief למבצע אנושי/AI) |

**הירארכיה:** שטוחה. ה-CEO מאציל ישירות לכל אחד מהארבעה. אין CMO ביניים — נשאר פשוט כל זמן שהיקף הצוות מצדיק.

**הבחנה קריטית בין copywriter ל-content-writer:**
- copy = קצר (≤150 תווים בד"כ), מכירתי, מטרה: לחיצה/המרה.
- content = ארוך (300+ מילים), ערכי, מטרה: חינוך/אמון/SEO.
ה-CEO מחליט ביניהם לפי אורך ומטרה, לא לפי "אקראי".

**מתי להפעיל את content-strategist:** רק כשהמשימה היא תכנון רחב. למשימה נקודתית ("כתוב פוסט", "תן 3 כותרות") ה-CEO מדלג עליו ועובר ישר למבצע.

המבנה הכולל: **CEO + 7 סוכנים כפופים** = 8 קבצי agent.

## Open Questions

- **חיכוך בין copywriter ל-content-writer במשימות גבוליות** — מה קורה כשמשימה היא "אימייל מכירתי באורך 200 מילים"? לא קצר מספיק לקופי טהור, לא ארוך מספיק ל-content. כרגע ההכרעה אצל ה-CEO, ייתכן שיתחדד עם הרצות אמיתיות.
- **שיתוף פעולה בין content-strategist למבצעים** — האם strategist צריך לחזור לבדוק את התוצרים שלהם, או רק קובע direction בתחילה? כרגע הוגדר רק כ-direction setter; ה-CEO אחראי על QA.
- **graphic-designer ללא יכולת ייצור קבצים** — ב-PRD הוגדר במפורש שהוא brief בלבד. מתישהו ייתכן שנרצה לחבר אותו לכלי AI לדיזיין (Midjourney / Canva API). לא בשלב הזה.
- **3 הסוכנים האחרים (הנה"ח, קמפיינים, אופרציה)** — האם גם הם יפוצלו? לא בעבודה הזו; יוחלט בנפרד אם וכאשר.

## Session Log

### 2026-05-06 — פיצול הסוכן השיווקי לארבעה תפקידים שטוחים [shipped]
- **What was done:** נוצרו 4 קבצי agent חדשים תחת `.claude/agents/`: `content-writer.md`, `copywriter.md`, `graphic-designer.md`, `content-strategist.md`. כל אחד עם frontmatter (`name` + `description` ל-routing) וגוף בעברית בפורמט אחיד: זהות, תחומי אחריות, מתי להפעיל / מתי לא, Input מ-CEO, Output ל-CEO, איסורים מוחלטים.
- **Decisions:**
  - **שטוח ולא היררכי** — אין CMO ביניים. ה-CEO מאציל ישירות.
  - **החלפה ולא תוספת** — הסוכן המאוחד "שיווק, תוכן וקריאייטיב" הוסר מ-`ceo.md` ולא הושאר כ-fallback. החלטה זו מבוססת על ההנחה שהקטגוריזציה החדשה כיסויית.
  - **graphic-designer מחזיר brief בלבד**, לא קבצי דיזיין. הוא מוגבל ל-spec מפורט שיוצא לפועל ע"י אדם או כלי AI חיצוני.
  - **content-strategist הוא אופציונלי** — לא חייב לעבור דרכו במשימה נקודתית.
- **Notes / Caveats:**
  - לא נגעתי ב-CLAUDE.md.
  - אף אחד מארבעת הסוכנים לא הורץ עדיין.
  - 3 הסוכנים האחרים (הנה"ח, קמפיינים, אופרציה) עדיין לא קיימים כקבצים.
- **Related:** [[ceo-agent]], [[agents-team-design]], [[claude-config-structure]], [[project-file-map]]
