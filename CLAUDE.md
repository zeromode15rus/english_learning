# English Learning Project — Persistent Context

## Who I am
Native Russian speaker. Level: B1–B2. Goal: C1 within 12 months.
I want to enrich vocabulary across 5 domains simultaneously,
memorize literary passages, and master complex English grammar through retelling.
I am serious about this — do not simplify, do not be condescending, treat me as an intelligent adult.

---

## Daily session structure (DO NOT change without being asked)

### 1. Vocabulary — 40 words
- Categories: 5 economics / 5 art & architecture / 10 engineering / 10 mathematics / 10 literary
- Language: Russian-first. Word in bold + (English in parentheses)
- Format: plain numbered list. NO flashcard widgets, NO tables, NO cards.
- Each entry must include:
  - 3–5 sentences: definition in Russian + context of use + what makes this word distinct from close synonyms
  - 1–2 example sentences in Russian showing the word used naturally in context
  - Etymology note where illuminating (origin language, root meaning)

### 2. English retelling passage — 250–350 words
- Theme: a place, a moment in time, a natural phenomenon, a human scene, or an artwork
- Style: literary, descriptive, precise — no journalistic flatness
- Required constructions (rotate and vary each day, never repeat the same pattern twice in a row):
  - Negative inversion (Seldom had…, Rarely does…, Never before had…)
  - Passive voice used for deliberate rhetorical effect, not just grammar exercise
  - Reduced participial clauses (past and present)
  - Absolute clauses (subject + participle, detached from main clause)
  - Complex noun phrases with post-modification (embedded relative, participial, infinitive)
  - Nominal relative clauses as subject (What remained…, What struck him most…)
- After the passage: break down EVERY sentence — rule name / full form / reduced form / when to use

### 3. Russian literary excerpt — 200–300 words
- Canonical authors only: Tolstoy, Bulgakov, Bunin, Gogol, Chekhov, Turgenev, Nabokov, Pasternak, Akhmatova, Tsvetaeva
- Never repeat the same author two days in a row
- Commentary must cover at minimum:
  - Rhythmic or syntactic device used
  - Why THIS passage specifically (not just why this author)
  - One thing in this passage that would be difficult to render in English

### 4. Algorithm theory block
- One algorithm or data structure per day, in depth
- Include: intuitive explanation → step-by-step trace on a small concrete example → JS implementation (clean, minimal comments) → Big O for time and space (best/average/worst) → when to use vs. when not to → 1–2 common interview questions about this algorithm
- Sources: trekhleb/javascript-algorithms, TheAlgorithms/JavaScript, Hashir15432/learn-algorithm-in-js

### 5. Math or physics law block
- One concept per day — intuitive explanation first, then formal
- Geometric or physical meaning, worked examples, common mistakes
- Formulated so the user can explain it from memory after one careful reading
- Source: standard textbook material + internet references

### 6. Comprehension questions
- 3–5 questions at the end of every session
- Cover: vocabulary (usage, distinction from synonyms), grammar (produce a sentence using today's rule), algorithm (trace or complexity), math (apply or explain)
- Questions appear inside the session file — user answers in conversation

---

## What I have already studied
@progress/words-log.md

---

## Rules for the agent

**Content:**
- Never repeat words already listed in words-log.md
- Grammar explanations: name the rule precisely, show full ↔ reduced form, explain when and why to use
- English passages must feel like literature, not textbook examples
- Russian excerpts: the longer and syntactically richer the better — avoid short lyric fragments
- Algorithm theory: always include a concrete trace with actual values, not just pseudocode
- Math: always include at least one worked example with numbers

**Format:**
- No headers inside vocabulary entries — plain numbered prose
- No bullet points inside definitions — flowing sentences
- No «---» dividers between individual words
- Use «---» only between major sections (Vocabulary / English Passage / Russian Excerpt / Algorithm / Math / Questions)

**Memory and continuity:**
- Track current day number in words-log.md
- If I say "продолжай" or "следующий день" — increment day, generate new content
- If I say "повтори слова дня N" — pull from sessions/day-N.md
- If I name an artist, writer, mathematician, or place as a theme — use it as the theme for the English passage that day

**On demand (user can request at any time):**
- "Пересказ о [теме]" — generate a standalone English retelling with full grammar breakdown, not attached to a daily session. Save to retellings/[slug].md
- "Теория [алгоритм/концепт]" — generate a standalone theory block. Save to theory/algorithms/ or theory/math/
- "Повтори вопросы дня N" — reprint the questions from sessions/day-N.md without the full session

---

## File structure
```
english-learning/
├── CLAUDE.md                        ← this file, auto-loaded
├── .claude/commands/new-day.md      ← /new-day command
├── progress/words-log.md            ← all studied words, single source of truth
├── sessions/day-NNN.md              ← full daily sessions
├── retellings/[slug].md             ← standalone English passages (on demand)
└── theory/
    ├── algorithms/NN-name.md
    └── math/NN-name.md
```
