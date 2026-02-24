# Ralph Agent Instructions — Splova MVP v2.0

You are an autonomous coding agent implementing the Splova mobile app.
The project root is the current working directory (SPLOVA/).
Read CLAUDE.md in the project root — it contains all architectural rules, stack constraints, and conventions you MUST follow.

## Your Task

1. Read the PRD at `ralph/prd-current.json` (lean file: contains only the next story to implement + metadata)
   - `completedStoryIds`: list of already-done stories (for context only)
   - `remainingCount`: how many stories are left
   - `currentStory`: the single story you must implement now
2. Read the progress log at `ralph/progress.txt` (check Codebase Patterns section first)
3. Check you're on the correct branch from PRD `branchName`. If not, check it out or create from main.
4. Implement `currentStory` — this is already the highest-priority incomplete story
5. Do NOT read `ralph/prd.json` directly — it is large and redundant with `prd-current.json`
6. Run quality checks (see below)
7. Update CLAUDE.md files if you discover reusable patterns (see below)
8. If checks pass, commit ALL changes with message: `feat: [Story ID] - [Story Title]`
9. Update `ralph/prd.json` (the master file) to set `passes: true` for the completed story — NOT prd-current.json (that is regenerated automatically)
10. Append your progress to `ralph/progress.txt`

## Quality Checks (Splova)

Run these in order. ALL must pass before committing:

```bash
npx tsc --noEmit          # TypeScript strict check — zero errori tollerati
npm test -- --passWithNoTests  # Jest unit tests (se esistono)
```

Se TypeScript fallisce: correggi TUTTI gli errori prima di procedere. MAI usare `// @ts-ignore` o `any` esplicito.

## Reference Repositories

Quando implementi una feature, consulta i repo in `"repository github"/<nome-repo>/` (nota: il path ha uno spazio, usa virgolette).

| Feature | Repo | File chiave da leggere |
|---|---|---|
| Setup Expo + Supabase + Auth | `expo-supabase-starter` | `app/_layout.tsx`, `providers/supabase-provider.tsx`, `hooks/useSupabase.ts` |
| Logica split spese + AI receipt | `spliit` | `src/lib/sharing/expense.ts`, `src/app/groups/[groupId]/expenses/` |
| Auth + multi-tenant coppia | `basejump` | `supabase/migrations/`, `src/lib/` |
| OCR scontrini con GPT-4o | `receipt-ocr` | `app/api/`, `lib/ocr.ts` |
| Algoritmo minimizzazione debiti | `simplify-debts` | `src/simplify.ts` |
| Pattern RLS TypeScript-safe | `drizzle-supabase-rls` | `src/db/schema.ts`, `src/db/rls.ts` |

Leggi i file rilevanti di questi repo PRIMA di implementare, per seguire pattern già collaudati.

## Regole Architetturali ASSOLUTE (da CLAUDE.md)

- **NO StyleSheet.create** — solo classi NativeWind
- **NO `any` TypeScript** — strict sempre
- **NO Firebase** — solo Supabase
- **RLS obbligatoria** su ogni tabella sensibile
- **React Hook Form + Zod** su tutti i form
- **TanStack Query** per tutti i dati Supabase
- **Componenti funzionali** con hooks, mai classi React

## Progress Report Format

APPEND to `ralph/progress.txt` (never replace, always append):
```
## [Date/Time] - [Story ID]
- What was implemented
- Files changed
- **Learnings for future iterations:**
  - Patterns discovered (e.g., "this codebase uses X for Y")
  - Gotchas encountered (e.g., "don't forget to update Z when changing W")
  - Useful context (e.g., "the evaluation panel is in component X")
---
```

The learnings section is critical - it helps future iterations avoid repeating mistakes and understand the codebase better.

## Consolidate Patterns

If you discover a **reusable pattern** that future iterations should know, add it to the `## Codebase Patterns` section at the TOP of progress.txt (create it if it doesn't exist). This section should consolidate the most important learnings:

```
## Codebase Patterns
- Example: Use `sql<number>` template for aggregations
- Example: Always use `IF NOT EXISTS` for migrations
- Example: Export types from actions.ts for UI components
```

Only add patterns that are **general and reusable**, not story-specific details.

## Update CLAUDE.md Files

Before committing, check if any edited files have learnings worth preserving in nearby CLAUDE.md files:

1. **Identify directories with edited files** - Look at which directories you modified
2. **Check for existing CLAUDE.md** - Look for CLAUDE.md in those directories or parent directories
3. **Add valuable learnings** - If you discovered something future developers/agents should know:
   - API patterns or conventions specific to that module
   - Gotchas or non-obvious requirements
   - Dependencies between files
   - Testing approaches for that area
   - Configuration or environment requirements

**Examples of good CLAUDE.md additions:**
- "When modifying X, also update Y to keep them in sync"
- "This module uses pattern Z for all API calls"
- "Tests require the dev server running on PORT 3000"
- "Field names must match the template exactly"

**Do NOT add:**
- Story-specific implementation details
- Temporary debugging notes
- Information already in progress.txt

Only update CLAUDE.md if you have **genuinely reusable knowledge** that would help future work in that directory.

## Quality Requirements

- ALL commits must pass your project's quality checks (typecheck, lint, test)
- Do NOT commit broken code
- Keep changes focused and minimal
- Follow existing code patterns

## Browser Testing (If Available)

For any story that changes UI, verify it works in the browser if you have browser testing tools configured (e.g., via MCP):

1. Navigate to the relevant page
2. Verify the UI changes work as expected
3. Take a screenshot if helpful for the progress log

If no browser tools are available, note in your progress report that manual browser verification is needed.

## Stop Condition

After completing a user story, check if ALL stories have `passes: true`.

If ALL stories are complete and passing, reply with:
<promise>COMPLETE</promise>

If there are still stories with `passes: false`, end your response normally (another iteration will pick up the next story).

## Important

- Work on ONE story per iteration
- Commit frequently
- Keep CI green
- Read the Codebase Patterns section in progress.txt before starting
