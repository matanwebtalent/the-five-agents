# Yuval — Pointer Doc

> **זה לא קובץ הגדרת סוכן.** Claude Code מגלה רק קבצים flat ב-`.claude/agents/`, לא תיקיות. ההגדרה הקנונית של יובל יושבת ב-`.claude/agents/yuval.md`. הקובץ הזה הוא תיעוד לבני אדם.

## מה יובל עושה

יובל הוא הסוכן הקריאייטיב של הצוות "The Five Agents" — הוא מייצר תמונות בפועל (קבצי PNG) באמצעות OpenAI Images API. בשונה מ-`graphic-designer` שמחזיר design briefs בלבד, יובל מפיק את הקובץ עצמו.

## איפה הקבצים

| מה | איפה |
|---|---|
| הגדרת הסוכן הקנונית (Claude Code reads this) | `.claude/agents/yuval.md` |
| הסקיל שיובל מפעיל | `.claude/skills/gpt-image-gen/SKILL.md` |
| תמונות השראה (לעקביות) | `yuval/reference/` |
| תמונות מוגמרות + prompts ששימשו | `yuval/outputs/<YYYY-MM-DD>-<slug>.{png,txt}` |

## איך משתמשים

המנכ"ל (`שוקי`) מאציל ליובל בקשות לתמונה. יובל סורק את `reference/`, מנסח prompt באנגלית שמשלב את הסגנון, קורא לסקיל `gpt-image-gen`, ושומר את התוצר תחת `outputs/`.

## איך מוסיפים reference

פשוט גוררים תמונות לתיקייה `yuval/reference/`. בקשה הבאה — יובל יקרא אותן וישתמש בסגנון.

## תיעוד מלא

- [[vault/Meeting Notes/yuval-creative-agent.md]] — Overview + Open Questions + Session Log.
- [[vault/Meeting Notes/gpt-image-gen-skill.md]] — מסמך הסקיל.
