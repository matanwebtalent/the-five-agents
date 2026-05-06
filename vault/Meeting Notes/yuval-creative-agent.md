# Yuval — Creative Agent

## Overview

יובל הוא **הסוכן ה-5 בצוות שיווק/תוכן/קריאייטיב** של "The Five Agents", כפוף ישירות ל-CEO (`שוקי`). בניגוד ל-`graphic-designer` שמחזיר design brief בלבד — יובל **מייצר את הקובץ עצמו** (PNG) באמצעות הסקיל [[gpt-image-gen-skill]] מעל OpenAI Images API.

**מבנה היברידי** (מאחר ו-Claude Code מגלה רק קבצי `.md` flat ב-`.claude/agents/`, לא תיקיות):

- `.claude/agents/yuval.md` — קובץ ההגדרה הקנוני (frontmatter `name: yuval`, גוף בעברית, trigger keywords ב-description).
- `yuval/` בשורש הפרויקט — תיקיית עבודה:
  - `yuval/reference/` — תמונות השראה (גוררים תמונות פנימה, יובל קורא ב-multimodal).
  - `yuval/outputs/` — תמונות מוגמרות + sibling `.txt` עם ה-prompt ששימש (לאיטרציה).
  - `yuval/agent.md`, `yuval/skill.md` — pointer docs לבני אדם בלבד; **לא** הגדרות שקלוד צורך.

**Workflow לכל בקשת תמונה:**
1. סורק `yuval/reference/` — אם יש תמונות, חולץ סגנון/פלטה/קומפוזיציה/lighting/mood.
2. בוחר אילו רכיבים מ-reference מתאימים לבקשה הספציפית (לפעמים reference סותר את הבקשה).
3. מנסח prompt באנגלית (האיכות טובה יותר) שמשלב בקשה + סגנון.
4. קורא לסקיל `gpt-image-gen` עם `PROMPT`/`OUT`/`SIZE`/`QUALITY`.
5. שומר `yuval/outputs/<YYYY-MM-DD>-<slug>.png` + sibling `.txt`.
6. מאמת `[ -s ]` + `file ... | grep PNG`.
7. מדווח ל-CEO: path, prompt summary, references used, style notes, caveats.

**עקרון על:** עקביות ויזואלית בין כל תמונות הפרויקט — לא ש"כל תמונה תרגיש מסטודיו אחר".

**טריגר ב-CEO:** המנכ"ל מבחין בין `graphic-designer` (brief) ל-`yuval` (PNG בפועל) דרך trigger keywords בעברית/אנגלית שמופיעים גם ב-`yuval.md` description וגם בטבלה ב-`ceo.md`.

## Open Questions

- **`yuval/reference/` — האם להגביל ל-N תמונות מקסימום?** קלט multimodal רב לקלוד יקר ואיטי. כרגע "2–3 הראשונות"; אם הצוות יצבור עשרות references, נצטרך לוגיקה לבחירה (random sample? semantic search?).
- **סתירה בין reference לבקשה** — מי מכריע? כרגע יובל מחליט לבד ומדווח. אם זה ייצר חיכוך, אפשר להוסיף escalation ל-CEO לאישור לפני generation.
- **Trigger overlap עם `graphic-designer`** — נראה בהרצות הראשונות אם ה-CEO באמת מבחין ביניהם או מתבלבל. לתעד מקרי ניסוי.
- **תמיכה ב-image edit / variations / inpainting** — לא בסקופ הנוכחי; ייתכן ויעלה כדרישה.
- **סיכום סגנון תקופתי** — אם reference יצבור הרבה תמונות, אולי כדאי `vault/Brand Guidelines/yuval-style.md` שיסכם את הסגנון הנפוץ פעם בחודש כדי לחסוך עיבוד חוזר בכל קריאה.
- **🐛 Agent-availability bug באותו סשן שיוצר את הסוכן** — אם yuval (או כל סוכן חדש) נוצר באמצע סשן, ה-Agent tool **של אותו סשן** לא טוען אותו לרשימה (ה-system prompt נקבע בתחילת הסשן). גם spawn של ה-CEO שכבר היה ברשימה ב-session start רץ עם הגדרת `ceo.md` ישנה (לא ראה את yuval ברשימת הכפיפים שלו). הפתרון לטווח קצר: סשן חדש אחרי הוספת סוכן. לטווח ארוך: לבדוק אם יש דרך לכפות reload, או שזה inherent בארכיטקטורה.

## Session Log

### 2026-05-06 — יצירת הסוכן + מבנה היברידי [shipped]
- **What was done:** נוצרו `.claude/agents/yuval.md` (הגדרה קנונית), `yuval/agent.md` + `yuval/skill.md` (pointer docs לבני אדם), ותיקיות `yuval/reference/` + `yuval/outputs/` עם `.gitkeep`. הסוכן מאציל ייצור תמונה לסקיל [[gpt-image-gen-skill]].
- **Decisions:**
  - **מבנה היברידי**: הגדרה קנונית ב-`.claude/agents/`, תיקיית עבודה ב-`yuval/`. הוחלט בגלל ש-Claude Code לא מגלה תיקיות תחת `.claude/agents/`.
  - **Prompt באנגלית** גם כשהבקשה בעברית — איכות תוצאה טובה יותר.
  - **Slug ייחודי**: `<YYYY-MM-DD>-<slug>` (kebab-case, ≤40 תווים). אם התנגש — `-2`, `-3`. **לא לדרוס**.
  - **Sibling `.txt`** חובה — לאיטרציה ("שנה X בלי לאבד את שאר ה-prompt").
  - **trigger keywords ב-description**: עברית ("תמונה של", "ציור של", "תייצר תמונה", "צור תמונה", "תמונה ל") ואנגלית ("generate image", "create image", "make a picture", "render", "illustrate"). מופיעות גם ב-CEO table.
  - **חיבור ל-`graphic-designer`** בטור: brief → yuval (כש-CEO רוצה גם direction וגם תמונה).
- **Notes / Caveats:**
  - הסוכן עדיין לא הופעל אפילו פעם אחת. ההרצה הראשונה תאמת אם ה-workflow מסודר.
  - `yuval/reference/` ריק כרגע → התמונה הראשונה תיוצר ב-defaults עד שיועלו references.
  - לא בודקים שמודל `gpt-image-2` באמת זמין — סוגיה שתיפתר ב-runtime, ראה Open Questions ב-[[gpt-image-gen-skill]].
- **Related:** [[gpt-image-gen-skill]], [[ceo-agent]], [[marketing-team-split]], [[agents-team-design]], [[project-file-map]]

### 2026-05-06 — הרצה ראשונה של הסקיל; bug ב-Agent loading [shipped]
- **What was done:** נוצרה התמונה הראשונה: `yuval/outputs/2026-05-06-webtalent-ai-for-business-feed.png` (1024x1024, 1.4MB) + sibling `.txt` עם ה-prompt המלא. הבקשה: "פוסט אינסטגרם לשיווק שירותי AI לעסקים". המשתמש העלה 3 references (לוגו Webtalent + שני פורטרטים lifestyle של הצוות) → חולץ סגנון: photographic-realistic editorial portrait, warm cream/off-white palette, deep-navy-blue accent (~#0F2E5C מהלוגו), wood paneling background, casual-professional wardrobe, soft natural lighting. ה-prompt נוסח באנגלית עם כל אלה.
- **Decisions:**
  - **לא להעתיק פנים מ-references**: ה-references הם של הצוות האמיתי של Webtalent. תועד ב-`.txt` שלא יצרנו את אותם אנשים אלא subject "of-the-style" — gpt-image לא משחזר פנים אמיתיות בצורה מהימנה, ובוודאי לא להציב את הלקוח בתמונה generated בלי הסכמה מפורשת.
  - **סלוג**: `webtalent-ai-for-business-feed` — שילב את שם המותג כדי שיהיה קל לסנן את כל פלטי Webtalent בעתיד.
- **Notes / Caveats:**
  - 🐛 **Agent-availability bug**: הריצה הראשונה לא רצה דרך הסוכן `yuval` בפועל. הסשן הראשי שיצר את `.claude/agents/yuval.md` באמצע סשן לא ראה את yuval כ-Agent tool זמין (טעון ב-session start). spawn ל-`ceo` רץ עם הגדרה ישנה והודיע "אין לי yuval". פתרון: ביצעתי את ה-workflow של yuval **מהסשן הראשי** — sourcing reference, prompt construction באנגלית, קריאה לסקיל, שמירה, אימות. בסשן חדש זה אמור לעבוד דרך yuval באופן תקין.
  - **gpt-image-2 עובד בחשבון של המשתמש** — Open Question שנפתרה. ראה [[gpt-image-gen-skill]].
  - **Caveats בתמונה עצמה**: ה-laptop UI שב-AI dashboard נראה הגיוני אבל לא קריא במלואו (gpt-image מתקשה עם טקסט קטן). אין טקסט באמת שם, רק רמיזות ל-UI.
- **Related:** [[gpt-image-gen-skill]], [[ceo-agent]]
