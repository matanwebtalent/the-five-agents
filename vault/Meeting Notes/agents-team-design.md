# Agents Team Design

## Overview

הפרויקט "The Five Agents" בנוי כצוות סוכנים: סוכן ראשי (**CEO / מנכ"ל**) שמנהל **7 סוכני-משנה שטוחים**. PRD מפורט הוגדר ב-2026-05-06; CEO Agent הוא subagent ייעודי תחת `.claude/agents/ceo.md` שאליו מואצלת כל משימת משתמש לפני הפעלת סוכן ספציפי.

> **הערה לגבי השם:** "Five Agents" נשמע כמו 5 סוכנים סה"כ, אבל המבנה בפועל הוא CEO + 7 = 8. השם נשמר אסטרטית; ייתכן שיתעדכן בהמשך.

**שבעת הסוכנים הכפופים:**

צוות שיווק/תוכן/קריאייטיב — קיימים כקבצים נפרדים (ראה [[marketing-team-split]]):
1. `content-strategist` — אסטרטג תוכן (תכנון direction).
2. `content-writer` — כותב תוכן (ארוך, ערכי).
3. `copywriter` — קופירייטר (קצר, מכירתי).
4. `graphic-designer` — מעצב גרפי (briefs ויזואליים, לא מייצר קבצים).

שאר התחומים — עדיין רק כקטגוריות ב-`ceo.md`:
5. הנהלת חשבונות.
6. ניהול קמפיינים ממומנים.
7. ביצוע ואופרציה.

**עקרונות מרכזיים** (לפי [[ceo-agent]]):
- כל משימה עוברת קודם דרך CEO.
- חשיבה עסקית קודם, תפעולית אחר כך.
- אסור להחזיר תוצר ללא בקרת איכות.
- אסור להפעיל סוכן עם מידע חסר — קודם שואלים.

המודל הקרוב ביותר ב-[[superpowers-skills]] הוא `subagent-driven-development` + `dispatching-parallel-agents`.

## Open Questions

- **תוכן ארבעת קבצי הסוכנים הכפופים** — כרגע מוגדרים רק לפי קטגוריה ב-CEO agent. צריך להחליט מתי לכתוב את `marketing.md`, `accounting.md`, `paid-campaigns.md`, `operations.md`.
- **ערוצי תקשורת בין סוכנים** — האם CEO מקבל תוצר חזרה דרך subagent return value בלבד, או שצריך גם vault entry / הודעה אל המשתמש בין שלבים? ב-PRD זה לא נסגר במפורש.
- **קבצי `.claude/commands/`** — האם נרצה slash command קצר (כמו `/run-task`) שמפעיל את ה-CEO על משימה? יחליט המשתמש בהמשך.
- **אינטגרציה עם CLAUDE.md** — ה-PRD ציין במפורש שאופן הקריאה הקבועה מ-CLAUDE.md מחוץ להיקף לעת עתה. יוחזר לטיפול אחרי שיהיו 1-2 הרצות אמיתיות.

## Session Log

### 2026-05-06 — תכנון ראשוני, אין עדיין קוד [planned]
- **What was done:** הוגדר vision כללי בלבד: צוות סוכנים, מנכ"ל מנהל. הוקמו תיקיות ריקות `.claude/agents/` ו-`.claude/commands/`. הותקנו סקילים שיתמכו ב-pattern (`subagent-driven-development`, `dispatching-parallel-agents`).
- **Decisions:** עוד לא נסגרו תפקידים, מספר סוכנים, או stack. ממשיכים ב-design discovery.
- **Notes / Caveats:** ה-agents directory ריק כדי לא להזריק החלטות מוקדם מדי. הצעד הבא הוא Q&A עם המשתמש לסגירת ה-roles.
- **Related:** [[superpowers-skills]], [[claude-config-structure]], [[claude-md]]

### 2026-05-06 — פיצול הצוות השיווקי לארבעה סוכנים שטוחים [shipped]
- **What was done:** הסוכן המאוחד "שיווק, תוכן וקריאייטיב" פוצל ל-`content-strategist`, `content-writer`, `copywriter`, `graphic-designer`. נוצרו 4 קבצי agent חדשים, ועודכן `ceo.md` (זהות + טבלת routing).
- **Decisions:** מבנה שטוח (ללא CMO ביניים), החלפה מלאה (לא fallback), graphic-designer מחזיר briefs בלבד.
- **Notes / Caveats:** המבנה הסופי הוא 8 קבצי agent (CEO + 7), לא 5. השם "Five Agents" כבר לא תיאורי-מספרית — אסטרטית-בלבד.
- **Related:** [[marketing-team-split]], [[ceo-agent]], [[project-file-map]]

### 2026-05-06 — PRD ל-CEO Agent + יצירת `.claude/agents/ceo.md` [shipped]
- **What was done:** המשתמש סיפק PRD מלא ל-CEO Agent (זהות, תחומי אחריות, לוגיקת החלטה, מתי להפעיל איזה סוכן, תבנית שאלות הבהרה, checklist לבקרת איכות, פורמט פלט סופי, איסורים). תורגם לקובץ subagent רשמי ב-`.claude/agents/ceo.md` עם frontmatter (`name: ceo`, `description`) וגוף בעברית.
- **Decisions:**
  - שם הקובץ: `ceo.md` (קצר, אנגלית).
  - שפה: עברית בגוף הקובץ. frontmatter באנגלית כי Claude Code פותח אותו.
  - מודל הפעלה: CEO הוא **subagent ייעודי** — הסשן הראשי מאציל אליו כל משימה. לא חיבור ל-CLAUDE.md בשלב הזה (מחוץ להיקף לפי PRD).
  - שאלות הבהרה: עיקרון + תבנית קטגוריזציה בלבד — לא checklist נוקשה לכל משימה.
- **Notes / Caveats:**
  - ארבעת הסוכנים הכפופים **לא** הוגדרו עדיין בקבצים נפרדים — רק קטגוריזציה בתוך CEO. זה מכוון לפי הוראות ה-PRD.
  - לא נגעתי ב-CLAUDE.md — מחוץ להיקף לפי PRD.
- **Related:** [[ceo-agent]], [[claude-config-structure]], [[project-file-map]], [[superpowers-skills]]

## Session Log

### 2026-05-06 — תכנון ראשוני, אין עדיין קוד [planned]
- **What was done:** הוגדר vision כללי בלבד: צוות סוכנים, מנכ"ל מנהל. הוקמו תיקיות ריקות `.claude/agents/` ו-`.claude/commands/`. הותקנו סקילים שיתמכו ב-pattern (`subagent-driven-development`, `dispatching-parallel-agents`).
- **Decisions:** עוד לא נסגרו תפקידים, מספר סוכנים, או stack. ממשיכים ב-design discovery.
- **Notes / Caveats:** ה-agents directory ריק כדי לא להזריק החלטות מוקדם מדי. הצעד הבא הוא Q&A עם המשתמש לסגירת ה-roles.
- **Related:** [[superpowers-skills]], [[claude-config-structure]], [[claude-md]]
