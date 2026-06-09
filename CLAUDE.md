# English Learning Project — Persistent Context

## Who I am
Native Russian speaker. Level: B1–B2. Goal: C1 within 12 months.
I want to enrich vocabulary across 5 domains simultaneously,
memorize literary passages, and master complex English grammar through retelling.
I am serious about this — do not simplify, do not be condescending, treat me as an intelligent adult.
I am also a developer (frontend + backend) preparing for technical interviews.

---

## Daily session structure (DO NOT change without being asked)

### 1. Vocabulary — 40 words
- Categories: 5 economics / 5 art & architecture / 5 engineering / 5 programming & CS / 10 mathematics / 10 literary
- Language: Russian-first. Word in bold + (English in parentheses)
- Format: plain numbered list. NO flashcard widgets, NO tables, NO cards.
- Each entry must include:
  - 3–5 sentences: definition in Russian + context of use + what makes this word distinct from close synonyms
  - 1–2 example sentences in Russian showing the word used naturally in context
  - Etymology note where illuminating (origin language, root meaning)

### 2. English retelling passage — 300–400 words
- **Theme:** a painting or artwork — focus on what makes it visually and compositionally beautiful, the techniques the artist used, why specific choices were made. NOT a biography. Think of it as a guided close-looking: the reader should understand the painting without seeing it.
- Style: literary, descriptive, aesthetically ambitious — model is Nabokov, Woolf, Sebald, late Henry James. No journalistic flatness. No simple sentences where a complex one can do the work more beautifully.
- The passage must be genuinely beautiful — not a grammar exercise dressed as literature, but literature that happens to contain grammar. Each sentence should be worth reading for its own sake.
- Sentences must be layered: a single sentence may contain 2–4 constructions simultaneously. Avoid one-construction-per-sentence schoolbook style.
- Required constructions — use ALL of these across the passage, rotating which appear in which sentence:
  - Negative inversion (*Seldom had…, Rarely does…, Never before had…, Not until…, So dense was…*)
  - Passive voice for deliberate rhetorical effect — foregrounding the receiver, suppressing the agent
  - Reduced past participial clauses (*Draped in…, Pressed against…, Held together by…*)
  - Reduced present participial clauses (*Stretching from…, Arriving without…, Carrying with it…*)
  - Absolute clauses — own subject + participle (*its head tilted, the light failing, the windows dark*)
  - Complex noun phrases: stacked post-modifiers (relative + participial + infinitive + prepositional)
  - Nominal relative clauses as subject (*What remained…, What no one had anticipated…*)
  - *so [adj] as to [infinitive]* — degree/result structure
  - *not so much X as Y* — correlative reframing
  - *as though* + participle — suppressed subjunctive
  - Fronted object-complement (*to make visible the…, to render permanent the…*)
  - Tricolon — three parallel noun phrases or clauses, third element longest or most complex
  - Appositive noun phrase expanding on the previous noun (*— that moment when…, — the particular shade of…*)
- After the passage: full **Russian translation** — literary, not word-for-word. Must read as good Russian prose. Comes BEFORE the grammar breakdown.
- After the translation: break down EVERY grammatical construction using this exact format:
  - **Правило / Rule:** название по-русски — English name
  - **Время / Форма:** явное название времени или формы + краткая структура
  - **Полная форма:** без сокращения, без инверсии
  - **Изменённая форма:** как написано в тексте
  - **Применение:** когда и почему — на русском
  - **Почему так построено это предложение:** риторический и смысловой эффект выбранной конструкции
- End the breakdown with a summary table of all constructions used that day

### 3. Russian literary excerpt — 200–300 words
- Canonical authors only: Tolstoy, Bulgakov, Bunin, Gogol, Chekhov, Turgenev, Nabokov, Pasternak, Akhmatova, Tsvetaeva
- Never repeat the same author two days in a row
- Commentary must cover at minimum:
  - Rhythmic or syntactic device used
  - Why THIS passage specifically (not just why this author)
  - One thing in this passage that would be difficult to render in English

### 4. Algorithm theory block
- One algorithm or data structure per day, in depth
- Include: intuitive explanation → step-by-step trace on a small concrete example → JS implementation (clean, minimal comments) → Big O for time and space (best/average/worst) → when to use vs. when not to
- Priority topics: algorithms actively used in frontend frameworks (React reconciliation / Fiber, virtual DOM diffing, scheduler, reactivity systems in Vue/Solid), and classical CS algorithms (sorting, search, graph, DP)
- Sources: trekhleb/javascript-algorithms, TheAlgorithms/JavaScript, Hashir15432/learn-algorithm-in-js

### 5. Math or physics law block
- One concept per day — intuitive explanation first, then formal
- Geometric or physical meaning, worked examples, common mistakes
- Formulated so the user can explain it from memory after one careful reading

### 6. Engineering interview theory — questions and answers
Three questions per day from these three tracks, one question per track:

**Track A — System Design**
Cover: scalability, load balancing, caching (Redis, CDN, browser cache), message queues (Kafka, RabbitMQ), CAP theorem, consistency models, API design (REST vs GraphQL vs gRPC), rate limiting, database sharding, microservices vs monolith, observability (logs, metrics, traces).
Format: question → answer (3–5 paragraphs, concrete, with trade-offs named explicitly).

**Track B — Frontend internals and algorithms**
Cover: React Fiber reconciliation, virtual DOM diffing algorithm, React scheduler (priority lanes, time-slicing), how reactivity works in Vue 3 / Solid / Svelte, browser rendering pipeline, CSS layout algorithms (flexbox, grid internals), event loop and microtask queue, service workers, web vitals and how they are measured.
Format: question → answer with a JS/pseudocode snippet where helpful.

**Track C — Backend and databases**
Cover: SQL query execution (how indexes work, query planner, EXPLAIN), B-tree vs hash index, N+1 problem, transaction isolation levels, ACID vs BASE, PostgreSQL vs MongoDB trade-offs, connection pooling, database migrations in production, background jobs and queues, REST API versioning, authentication patterns (JWT, sessions, OAuth).
Format: question → answer with SQL/code snippet where helpful.

### 7. Comprehension questions
- 2–3 questions at the end covering vocabulary, grammar, and any of today's theory tracks
- Saved in session file — user answers in conversation

---

## What I have already studied
@progress/words-log.md

---

## Rules for the agent

**Content:**
- Never repeat words already listed in words-log.md
- Grammar explanations: name the rule precisely, show full ↔ reduced form, explain when and why to use
- Passages must feel like literature AND explain art — the reader should understand the painting without seeing it
- Russian excerpts: syntactically rich, long — avoid short lyric fragments
- Algorithm theory: always include a concrete trace with actual values
- Math: always at least one worked example with numbers
- Engineering questions: always name trade-offs explicitly — there is no "best" answer, only trade-offs

**Format:**
- No headers inside vocabulary entries — plain numbered prose
- No bullet points inside definitions — flowing sentences
- No «---» dividers between individual words
- Use «---» only between major sections

**Memory and continuity:**
- Track current day number in words-log.md
- "продолжай" or "следующий день" → increment day, generate new content
- "повтори слова дня N" → pull from sessions/day-N/vocabulary.md
- If I name an artist or painting → use it as the passage theme that day
- If I name a system design topic, algorithm, or DB concept → use it as the engineering question that day

**On demand:**
- "Пересказ о [теме]" → standalone retelling saved to retellings/[slug].md (EN + RU translation + grammar breakdown)
- "Теория [концепт]" → standalone theory block saved to theory/
- "System design [тема]" → full system design answer saved to theory/system-design/

---

## File structure
```
english-learning/
├── CLAUDE.md                              ← this file, auto-loaded every session
├── .claude/commands/new-day.md            ← /new-day command
├── progress/words-log.md                  ← all studied words, never repeat
├── sessions/
│   └── day-NNN/
│       ├── vocabulary.md
│       ├── passage.md                    ← EN painting retelling + RU translation + grammar
│       ├── excerpt.md                    ← Russian literary excerpt
│       ├── algorithm.md
│       ├── math.md
│       ├── engineering.md                ← system design + frontend + backend questions
│       └── questions.md
├── retellings/[slug].md                  ← standalone retellings (EN + RU)
└── theory/
    ├── algorithms/
    ├── math/
    └── system-design/
```
