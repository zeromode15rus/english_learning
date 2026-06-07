Read CLAUDE.md and progress/words-log.md to find the current day number.
Increment it by one. Then do the following in order:

1. Generate 40 new Russian vocabulary words (never repeat from words-log.md):
   - 5 economics / 5 art & architecture / 10 engineering / 10 mathematics / 10 literary
   - Format: numbered list. Each entry = 3–5 sentence Russian definition + context of use
     + 1–2 example sentences showing the word naturally in Russian.
   - English translation in parentheses after the Russian word.

2. Generate one English retelling passage (250–350 words):
   - Theme: a place, a moment in time, a natural phenomenon, or a human scene
   - Style: literary, descriptive, precise
   - Must include: inversion, passive voice, participle clause, complex noun phrase
   Then: break down EVERY sentence — rule name, full form vs. reduced form, when to use.

3. Generate one Russian literary excerpt (200–300 words):
   - From canonical Russian literature only (Tolstoy, Bulgakov, Bunin, Gogol, Chekhov, Turgenev)
   - Commentary: rhythm / device / structure — why memorize this passage specifically

4. Generate one algorithm theory block:
   - One algorithm or data structure explained deeply
   - Include: intuitive explanation, step-by-step trace on a small example,
     JS implementation (clean, commented minimally), Big O for time and space,
     best/worst/average case, when to use vs when not to, common interview questions

5. Generate one math or physics law block:
   - One concept explained intuitively first, then formally
   - Geometric or physical meaning, worked examples, common mistakes
   - Formulated so the user can explain it from memory after reading once

6. Ask 3–5 comprehension questions testing today's algorithm and math content.
   Wait for the user's answers before giving feedback.

7. Save the full session to sessions/day-NNN.md
8. Append the 40 new words (English only, one per line) to progress/words-log.md
   under a new heading ## Day NNN
