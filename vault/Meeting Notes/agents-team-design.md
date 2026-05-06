# Agents Team Design

## Overview

הפרויקט "The Five Agents" יבנה כצוות סוכנים ליצירת תוכן: סוכן ראשי (**מנכ"ל**) שמנהל ארבעה (?) סוכני-משנה. השם "Five" רומז על מנכ"ל + 4 כפיפים, אבל המספר המדויק והתפקידים עוד לא נסגרו.

המודל הקרוב ביותר במונחי implementation הוא [[superpowers-skills]] → `subagent-driven-development`: מנהל שמפזר משימות לסוכנים עם פרומפטים ייעודיים (implementer / spec-reviewer / code-quality-reviewer). בנוסף, `dispatching-parallel-agents` נותן את הפרקטיקה של הרצה במקביל.

כרגע אין אף הגדרת agent בפועל ב-`.claude/agents/` — התיקייה ריקה.

## Open Questions

- **כמה סוכנים בדיוק?** המנכ"ל + 4 (לפי השם) או יותר/פחות. צריך החלטה.
- **מה הם תפקידי הצוות?** הצעות אפשריות: Strategist (מתכנן תוכן), Writer (מנסח), Editor (עורך), Brand Guardian (שומר טון/מותג), Publisher (מוציא לאוויר). צריך לסגור.
- **איך הסוכנים מתקשרים?** דרך subagent dispatch של Claude Code? דרך MCP? דרך קבצים ב-vault? ההמלצה הראשונית: subagent dispatch + vault כזיכרון משותף.
- **באיזה ערוצים הצוות יפרסם?** בלוג, אימייל, סושיאל? צריך להגדיר scope.
- **מי המנכ"ל?** סוכן מסוים ב-`.claude/agents/` או הסשן הראשי של Claude Code עצמו? מודל "הסשן הראשי הוא המנכ"ל" יותר פשוט וכנראה הנכון.

## Session Log

### 2026-05-06 — תכנון ראשוני, אין עדיין קוד [planned]
- **What was done:** הוגדר vision כללי בלבד: צוות סוכנים, מנכ"ל מנהל. הוקמו תיקיות ריקות `.claude/agents/` ו-`.claude/commands/`. הותקנו סקילים שיתמכו ב-pattern (`subagent-driven-development`, `dispatching-parallel-agents`).
- **Decisions:** עוד לא נסגרו תפקידים, מספר סוכנים, או stack. ממשיכים ב-design discovery.
- **Notes / Caveats:** ה-agents directory ריק כדי לא להזריק החלטות מוקדם מדי. הצעד הבא הוא Q&A עם המשתמש לסגירת ה-roles.
- **Related:** [[superpowers-skills]], [[claude-config-structure]], [[claude-md]]
