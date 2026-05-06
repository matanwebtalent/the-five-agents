# Obsidian Vault Workflow

## Overview

`obsidian-vault-workflow` הוא הסקיל שמגדיר את הפרוטוקול של ה-vault הזה. הוא יושב ב-`.claude/skills/obsidian-vault-workflow/SKILL.md` ומכתיב:

1. **לפני כל משימה** — לזהות את הנושא (topic), לאתר קובץ topic קיים תחת `vault/Meeting Notes/` (או תיקייה רלוונטית אחרת), לקרוא אותו במלואו (Overview + Open Questions + Session Log), ולקרוא 2–3 ה-Meeting Notes האחרונים.
2. **אחרי כל משימה** — להוסיף `### YYYY-MM-DD — <כותרת> [status]` לתחתית `## Session Log`, לעדכן את ה-Overview אם הסקופ/הסטטוס השתנה, ולעדכן את `## Open Questions`. חובה לכלול `[[wikilinks]]` לקבצים קשורים.

המבנה: תיקיית `vault/` עם ארבע תת-תיקיות (`Meeting Notes`, `Content Briefs`, `Publishing Log`, `Brand Guidelines`), כל אחת עם `_index.md` ועם קובץ-לכל-נושא.

## Open Questions

- האם לעדכן את `CLAUDE.md` כך שיורה במפורש להפעיל את הסקיל בכל סשן? (ייעשה ב-session הקרוב — חיוני להישרדות הפרוטוקול.)
- האם להוסיף hook ב-`settings.json` שיציג hint על הסקיל בכל פתיחת סשן? צריך לבדוק אם הסקיל לבדו מספיק.

## Session Log

### 2026-05-06 — ייבוא הסקיל לפרויקט [shipped]
- **What was done:** קובץ `SKILL.md` של `obsidian-vault-workflow` קיים תחת `.claude/skills/obsidian-vault-workflow/`. הוקם vault מלא עם ארבע תת-תיקיות וקבצי `_index.md`. נוצרו topic files ראשוניים שמתעדים את כל מצב הפרויקט הנוכחי.
- **Decisions:** הולכים עם הפרוטוקול הקיים בסקיל בדיוק כפי שהוא — `vault/` (לא `docs/`), חלוקה לפי סוג משימה (Meeting Notes / Content Briefs / Publishing Log / Brand Guidelines), פורמט topic file עם Overview + Open Questions + Session Log + status tags.
- **Notes / Caveats:** כדי שהפרוטוקול יעבוד אוטומטית בכל סשן, נדרש לעדכן את `CLAUDE.md` כך שיציין את הסקיל כחובה. זה בתור.
- **Related:** [[claude-config-structure]], [[claude-md]], [[project-file-map]]
