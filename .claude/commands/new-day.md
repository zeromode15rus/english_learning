Read CLAUDE.md and progress/words-log.md to find the current day number.
Increment it by one. Create the folder sessions/day-NNN/ and generate all files below.

---

## 1. vocabulary.md — 40 words across 6 categories (never repeat from words-log.md)

Categories:
- 5 economics
- 5 art & architecture
- 5 engineering
- 5 programming / computer science
- 10 mathematics
- 10 literary / general

Each entry format:
- **Слово** (English) — 3–5 sentences: Russian definition + context + distinction from synonyms
- Etymology note where illuminating
- 1–2 example sentences in Russian showing the word used naturally

Append all 40 words (English only, one per line) to progress/words-log.md under ## Day NNN.

---

## 2. passage.md — English retelling about a painting or artwork

Theme: a specific painting, artist, or artwork — explain what makes it visually and compositionally
beautiful, the techniques used, why specific choices create specific effects.
NOT a biography. The reader should understand the painting without seeing it.

Length: 300–400 words.
Style: Nabokov, Woolf, Sebald, late Henry James. Genuinely literary — each sentence worth reading.
Each sentence must carry 2–4 grammatical constructions simultaneously.

Required constructions (rotate all across the passage):
negative inversion / passive (rhetorical) / reduced past participial clause /
reduced present participial clause / absolute clause / complex noun phrase (stacked) /
nominal relative clause as subject / so…as to / not so much X as Y /
as though + participle / fronted object-complement / tricolon / appositive noun phrase

After the passage:
1. Full Russian literary translation (NOT word-for-word — must read as good prose)
2. Breakdown of every construction:
   - Правило / Rule
   - Время / Форма
   - Полная форма
   - Изменённая форма
   - Применение (на русском)
   - Почему так построено это предложение
3. Summary table of all constructions used

---

## 3. excerpt.md — Russian literary excerpt

200–300 words from canonical authors only:
Tolstoy, Bulgakov, Bunin, Gogol, Chekhov, Turgenev, Nabokov, Pasternak, Akhmatova, Tsvetaeva
Never repeat the same author two days in a row.

Commentary must cover:
- Rhythmic or syntactic device used
- Why THIS specific passage (not just why this author)
- One thing in this passage that would be difficult to render in English

---

## 4. algorithm.md — Algorithm theory block

One algorithm or data structure per day.
Priority: algorithms used in frontend frameworks (React Fiber reconciler, virtual DOM diffing,
scheduler with priority lanes, time-slicing; Vue 3 reactivity proxy system; Solid signals).
Otherwise: classical CS algorithms (sorting, graphs, DP, trees).

Include:
- Intuitive explanation (no jargon first)
- Step-by-step trace with real values (not just pseudocode)
- JS implementation (clean, minimal comments)
- Big O: time and space, best / average / worst case
- When to use vs. when not to
- 1–2 interview questions about this algorithm

---

## 5. math.md — Math or physics concept

One concept per day — intuitive explanation first, then formal definition.
Geometric or physical meaning. At least one worked example with numbers. Common mistakes.
Formulated so the user can explain it from memory after one careful reading.

---

## 6. engineering.md — Engineering interview theory

Three questions, one from each track:

**Track A — System Design**
One question + full answer (3–5 paragraphs). Name trade-offs explicitly.
Topics: scalability, load balancing, caching (Redis / CDN / browser), message queues
(Kafka / RabbitMQ), CAP theorem, consistency models, API design (REST / GraphQL / gRPC),
rate limiting, database sharding, microservices vs monolith, observability.

**Track B — Frontend internals**
One question + answer with JS/pseudocode snippet where helpful.
Topics: React Fiber reconciliation algorithm, virtual DOM diffing (keys, heuristics),
React scheduler (priority lanes, time-slicing, concurrent mode), how reactivity works
in Vue 3 (Proxy-based) / Solid (fine-grained signals) / Svelte (compiled reactivity),
browser rendering pipeline (style → layout → paint → composite), event loop and
microtask queue, service workers and caching strategies, Core Web Vitals.

**Track C — Backend and databases**
One question + answer with SQL or code snippet where helpful.
Topics: how SQL indexes work (B-tree, hash), query planner and EXPLAIN,
N+1 problem and solutions, transaction isolation levels (READ COMMITTED / REPEATABLE READ /
SERIALIZABLE), ACID vs BASE, PostgreSQL vs MongoDB trade-offs, connection pooling,
database migrations in production (zero-downtime strategies), background jobs and queues,
JWT vs sessions, OAuth 2.0 flows, REST API versioning strategies.

---

## 7. questions.md — Comprehension questions

2–3 questions covering: vocabulary usage, one grammar production task, one theory question
from any of the engineering tracks. Save to questions.md. User answers in conversation.
