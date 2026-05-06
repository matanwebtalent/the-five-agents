# Yael — Rewriter Agent

## Overview

יעל היא **הסוכנת ה-6 בצוות שיווק/תוכן/קריאייטיב** של "The Five Agents", כפופה ישירות ל-CEO (`שוקי`). תפקידה: לקחת מאמרי גלם בסגנון "אחר" מתיקיית `Content/` ולשכתב אותם בסגנון המותג. בשונה מ-[[content-writer]] שכותב from scratch לפי בריף — יעל **משכתבת** קובץ קיים.

**סוג הסוכנת:** LLM-only. בפרונטמטר היא מוגבלת ל-`tools: Read, Write, Edit, Glob, Grep` — אין לה Bash, WebSearch, או גישת API. זה מאכף את החוזה: יעל היא טקסט-אל-טקסט בלבד.

**מבנה היברידי** (כמו yuval):
- `.claude/agents/yael.md` — קובץ ההגדרה הקנוני (frontmatter `name: yael` + `tools:` + גוף בעברית).
- `yael/` בשורש הפרויקט — תיקיית עבודה:
  - `yael/style-guide.md` — מדריך הסגנון (בני אדם מתחזקים).
  - `yael/reference/*.md` — דוגמאות לטקסטים בסגנון שלנו (יעל קוראת 2–3 בתחילת סשן).
  - `yael/agent.md` — pointer doc לבני אדם.
- `Content/` (root) — מאמרי גלם ממתינים. `Content/Ready/` — ארכיון raw של מה שעובד. `Output/` — תוצרים משוכתבים.

**Workflow לכל בקשה:**
1. טוענת style (פעם בסשן): `yael/style-guide.md` + 2–3 קבצים מ-`yael/reference/`.
2. מוצאת מאמר ב-`Content/<name>.md`.
3. משכתבת בסגנון, שומרת על המסר/עובדות, מחליפה ניסוח/מבנה/מילון.
4. בכל מקום שמתאים תמונה — מכניסה inline `{{IMAGE_NEEDED: "<English prompt ready-to-use ליובל>"}}`.
5. כותבת `Output/<name>.md` (משוכתב, עם placeholders) + `Content/Ready/<name>.md` (ארכיון raw).
6. **לא** מוחקת את המקור ב-`Content/` — אין לה Bash. שוקי מוחק אחרי וידוא.
7. מדווחת לשוקי: path, שינויים, רשימת placeholders עם prompts.

**אילוץ ארכיטקטוני שהכריע את התכנון:** ב-Claude Code סאב-אייג'נטים לא יכולים להפעיל סאב-אייג'נטים אחרים — רק הסשן הראשי. לכן יעל לא קוראת ליובל ישירות. במקום זה היא משאירה placeholders, ושוקי (המנכ"ל, סשן ראשי, יש לו את כל הכלים) מטפל בשרשרת: יעל → שוקי → yuval → שוקי Edit → תוצר סופי. ראה את "פרוטוקול: עיבוד פלט מ-yael" ב-`.claude/agents/ceo.md`.

**טריגר ב-CEO:** trigger keywords בעברית ("שכתב", "ערוך", "נסח מחדש", "תרגם", "סכם", "מאמר", "תוכן", "פוסט") ובאנגלית ("rewrite", "edit", "rephrase", "translate", "summarize", "article", "content", "post"). מופיעות גם ב-`yael.md` description וגם בטבלת ההחלטה ב-`ceo.md`.

## Open Questions

- **זיהוי שפת מקור** — האם יעל אמורה לזהות אוטומטית HE/EN ולתרגם, או רק לשכתב באותה שפה? כרגע: לפי הוראת המנכ"ל. צריך להחליט default behavior.
- **גבול עם `content-writer`** — מה ההבדל בין "שכתוב כבד" לבין "כתיבה מחדש מאפס"? אם יעל מחליפה 90% מהטקסט, האם זה עדיין "שכתוב"? אין כרגע סף ברור.
- **Style extraction אוטומטי** — האם להוסיף סקיל שיקרא את `yael/reference/` וייצר/יעדכן את `style-guide.md` אוטומטית? כרגע סטייל-גייד הוא ידני.
- **מבנה `yael/reference/`** — תת-תיקיות לפי ערוץ (blog/email/social)? כרגע flat. אם יצברו עשרות דוגמאות, אולי צריך מבנה.
- **הרצה ראשונה** — הסוכנת עוד לא הופעלה. style-guide.md הוא שלד ריק. הריצה הראשונה תיתקל ב-"ללא reference; משכתבת לפי style-guide בלבד" (וגם הוא ריק) — צריך למלא את style-guide.md לפני שיעל באמת תהיה שימושית.
- **🐛 Agent-availability bug** — באותו סשן שיוצר את הסוכן, ה-Agent tool עדיין לא רואה אותו (system prompt קבוע ב-session start). פתרון: סשן חדש אחרי הוספה. ראה Open Questions ב-[[yuval-creative-agent]].

## Session Log

### 2026-05-06 — bootstrap של style-guide + reference README [shipped]
- **What was done:**
  - מולא `yael/style-guide.md` עם תוכן ממשי (לא TBD), על בסיס התבוננות בסגנון הכתיבה הקיים בפרויקט עצמו (vault notes, agent files, commit messages). 11 סעיפים: טון, אורך משפט, מבנה פסקה, אוצר מילים מועדף/נמנע, איסורי שפה, hooks/CTAs, מבנה כותרות, עיצוב Markdown, עקרונות-על, הערות.
  - נוצר `yael/reference/README.md` — מסביר איך למלא את התיקייה (אילו קבצים לדרופ, מה לא, כמה). הוחלף ה-`.gitkeep` שכבר לא נדרש.
  - עודכן `.claude/agents/yael.md` להורות ליעל לדלג על `README.md` ב-`yael/reference/` (meta-doc, לא sample).
- **Decisions:**
  - **style-guide הוא draft שנוצר אוטומטית מסגנון הפרויקט הפנימי** — הוא הקול התיעודי-יבש של הקבצים הקיימים, **לא בהכרח** הקול הרצוי לתוכן שיווקי-חיצוני. סעיפים שטעונים ביקורת/החלפה ראשונה (טון, hooks, CTAs) מסומנים `[DRAFT]`.
  - **יעל לא קוראת README.md ב-reference/** — הוראה מפורשת ב-Workflow שלה. README הוא meta-doc לבני אדם.
  - **`yael/reference/` עדיין ריקה מ-samples בפועל** — המשתמש צריך לדרופ 2-3 קבצי תוכן אמיתיים בסגנון הרצוי. עד אז יעל תרוץ ב-fallback ("ללא reference; משכתבת לפי style-guide בלבד").
- **Notes / Caveats:**
  - הריצה הראשונה תהיה תקפה רק לתוכן ב-domain של מסמכים פנימיים-תיעודיים. לתוכן שיווקי, יש לעדכן את `[DRAFT]` או להוסיף samples ל-`reference/` לפני הריצה.
  - לא נוצר test article ב-`Content/` — נשאר באחריות המשתמש להציב מאמר אמיתי לפני הריצה הראשונה.
- **Related:** [[yuval-creative-agent]], [[ceo-agent]], [[marketing-team-split]]

### 2026-05-06 — יצירת הסוכנת + פרוטוקול IMAGE_NEEDED ב-CEO [shipped]
- **What was done:**
  - נוצר `.claude/agents/yael.md` (הגדרה קנונית, frontmatter עם `tools: Read, Write, Edit, Glob, Grep`, גוף בעברית עם זהות / מתי להפעיל / Workflow / Input / Output / איסורים / הערות תפעוליות).
  - נוצרו `yael/agent.md` (pointer doc), `yael/style-guide.md` (שלד עם סעיפים ריקים למילוי), `yael/reference/.gitkeep` (תיקייה ריקה לדוגמאות).
  - נוצרו 3 תיקיות בשורש הפרויקט: `Content/`, `Content/Ready/`, `Output/` (כל אחת עם `.gitkeep` שמסביר את התפקיד).
  - עודכן `.claude/agents/ceo.md`: שוקי מנהל עכשיו 9 סוכנים (לא 8); yael נוסף לרשימה כ-#6 בצוות שיווק; שורה חדשה בטבלת ההחלטה; **סעיף פרוטוקול חדש** "עיבוד פלט מ-yael עם {{IMAGE_NEEDED}} placeholders" שמתאר את שרשרת yael → CEO → yuval → CEO Edit; תוספת ל-"הערות לעתיד" עם הבחנה content-writer ↔ yael והאילוץ הארכיטקטוני.
  - עודכן `vault/Meeting Notes/_index.md` ו-`vault/Meeting Notes/project-file-map.md`.
- **Decisions:**
  - **`tools:` field בפרונטמטר** (לא רק תיאורי בגוף) — אכיפה טכנית של "בלי Bash/WebSearch/API". המשתמש בחר במפורש.
  - **שם המנכ"ל נשאר שוקי** (לא "ראובן" שהופיע בבקשה המקורית). הקובץ נשאר `ceo.md`.
  - **העברת קובץ Content/→Content/Ready/**: יעל **כותבת עותק** ל-`Content/Ready/` (כי בלי Bash אין `mv`), משאירה את המקור ב-`Content/`. שוקי מוחק את המקור אחרי וידוא ש-Output/ תקין. שומר את אכיפת "בלי Bash" ועדיין מקיים את החוזה הלוגי.
  - **`{{IMAGE_NEEDED: "<prompt>"}}` באנגלית, ready-to-use ליובל** — יובל ממילא מתרגם פנימית; חוסך שלב.
  - **שרשרת yael→yuval דרך שוקי** — לא דרך קריאה ישירה. אילוץ של Claude Code: סאב-אייג'נטים לא מפעילים סאב-אייג'נטים. תועד מפורשות גם ב-yael.md (איסורים) וגם ב-ceo.md (פרוטוקול + "הבחנה נוספת").
  - **Style guide הוא שלד** — לא מילאתי תוכן בסעיפים, כי המשתמש אמר "אצור בנפרד". הריצה הראשונה תהיה limited עד שהמשתמש ימלא את הסטייל.
- **Notes / Caveats:**
  - הסוכנת עדיין לא הופעלה אפילו פעם אחת. הריצה הראשונה תאמת את הזרימה (כולל את ה-Agent-availability bug — צריך סשן חדש כדי שהיא תהיה זמינה).
  - `yael/reference/` ריקה כרגע → הריצה הראשונה תדווח "ללא reference; משכתבת לפי style-guide בלבד".
  - `Content/.gitkeep`, `Content/Ready/.gitkeep`, `Output/.gitkeep` נוספו כדי לשמר את התיקיות ב-git כשהן ריקות.
- **Related:** [[yuval-creative-agent]], [[ceo-agent]], [[marketing-team-split]], [[agents-team-design]], [[project-file-map]]
