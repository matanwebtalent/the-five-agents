# Superpowers Skills

## Overview

ב-2026-05-06 הותקנו 14 סקילים מ-[obra/superpowers](https://github.com/obra/superpowers) לתוך `.claude/skills/`. הסקילים מסופקים כתיקיות עם `SKILL.md` (ולעיתים קבצי עזר). Claude Code סורק את התיאורים ב-frontmatter וטוען את הסקיל הרלוונטי כשהמשימה מתאימה.

הסקילים נחלקים לחמש קבוצות:

### תכנון וביצוע
- **`writing-plans`** — איך לכתוב מסמכי תכנון לפני קוד.
- **`executing-plans`** — איך לבצע תכנית כתובה צעד-צעד.
- **`brainstorming`** — תהליך מובנה לסיעור מוחות (כולל visual companion).

### פיתוח ובדיקות
- **`test-driven-development`** — TDD נכון: red-green-refactor + anti-patterns.
- **`systematic-debugging`** — root-cause tracing + defense-in-depth + condition-based-waiting + `find-polluter.sh` לטסטים נגועים.
- **`verification-before-completion`** — checklist לפני סימון כ-"גמור".

### Code review
- **`requesting-code-review`** — איך לבקש ביקורת על קוד שכתבת.
- **`receiving-code-review`** — איך להגיב לביקורת ולתקן בלי להתגונן.

### עבודה עם subagents (ה**הכי רלוונטי** ל-The Five Agents)
- **`dispatching-parallel-agents`** — מתי ואיך להריץ subagents במקביל.
- **`subagent-driven-development`** — pattern של מנהל + subagents מבצעים. כולל `implementer-prompt.md`, `spec-reviewer-prompt.md`, `code-quality-reviewer-prompt.md`. **המודל הקרוב ביותר למבנה ה"מנכ"ל + צוות" שהפרויקט מתכנן.**

### Git workflow
- **`using-git-worktrees`** — להריץ כמה משימות במקביל בלי להחליף ברנצ'ים.
- **`finishing-a-development-branch`** — איך לסגור ברנץ': squash, PR, merge.

### מטא
- **`using-superpowers`** — איך להשתמש בחבילה הזו עצמה.
- **`writing-skills`** — איך לכתוב סקיל חדש משלך (כולל anthropic-best-practices, persuasion-principles, testing-skills-with-subagents).

## בנוסף: סקילי Obsidian פנימיים

מעבר ל-superpowers, יש שני סקילים פנימיים נוספים תחת `.claude/skills/` שמשרתים את ה-vault:

- **`obsidian-vault-workflow`** — פרוטוקול קריאה/כתיבה ל-vault. ראה [[obsidian-vault-workflow]].
- **`obsidian-bases`** — יצירה ועריכה של `.base` files (database views ב-Obsidian). יופעל אם נחליט לבנות tabular views של ה-vault.
- **`obsidian-markdown`** — כתיבת Obsidian Flavored Markdown (wikilinks, embeds, callouts, properties). תמיכה תחבירית למה שכבר עושים בקבצי ה-topic.

## בנוסף: `skill-creator` הרשמי של Anthropic

הותקן ב-2026-05-06 מ-[anthropics/skills](https://github.com/anthropics/skills) — סקיל רשמי שעוזר ליצור, לערוך, ולמדוד ביצועים של סקילים. כולל `agents/` ו-`scripts/` ל-evals ו-benchmarks. שימושי כשנרצה לבנות סקילים מותאמים לסוכני הצוות (ראה [[agents-team-design]]).

## Open Questions

- אילו מהסקילים נכניס ל-flow קבוע של המנכ"ל-סוכן (ראה [[agents-team-design]])? ההימור הראשוני: `subagent-driven-development` + `dispatching-parallel-agents` + `writing-plans`.
- האם לעדכן את הסקילים מתישהו מ-upstream של obra/superpowers? אין כרגע מנגנון אוטומטי — נעשה ידנית בעת הצורך.

## Session Log

### 2026-05-06 — התקנה ראשונית של 14 סקילים [shipped]
- **What was done:** clone של `obra/superpowers` ל-`/tmp` והעתקה ב-`rsync --ignore-existing` ל-`.claude/skills/`. 46 קבצים נוספו, 0 נדרסו.
- **Decisions:** התקנה ידנית בלבד — לא דרך `/plugin` של Claude Code (לא זמין אצל המשתמש).
- **Notes / Caveats:** אין `commands/` או `agents/` במאגר המקור.
- **Related:** [[claude-config-structure]], [[agents-team-design]], [[project-file-map]]

### 2026-05-06 — הוספת `skill-creator` הרשמי [shipped]
- **What was done:** clone של `anthropics/skills` ל-`/tmp` והעתקת `skills/skill-creator/` בלבד אל `.claude/skills/skill-creator/`. 18 קבצים (SKILL.md + agents/ + scripts/ + references/ + assets/ + eval-viewer/ + LICENSE.txt).
- **Decisions:** ניסיון ראשון היה דרך `claude plugin install` כפי שהמשתמש ביקש, אבל ה-CLI לא ב-PATH; fallback להתקנה ידנית באישור המשתמש. אותו pattern של Superpowers — `rsync --ignore-existing`.
- **Notes / Caveats:** הסקיל מביא איתו `eval-viewer/` (כנראה React/HTML עם נכסים) ו-`scripts/` להרצת evals — ייבחנו אם נשתמש בהם בעת יצירת סקילים מותאמים.
- **Related:** [[claude-config-structure]], [[agents-team-design]], [[project-file-map]]
