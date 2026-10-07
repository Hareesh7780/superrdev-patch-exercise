# NOTES

## What I fixed (6 changes, one commit each)
1. **Search wildcards:** `%` and `_` typed by a user matched everything. I escape them in TaskController and added `ESCAPE` to the LIKE clauses.
2. **Query precedence:** a missing bracket meant `AND`/`OR` mixed up, so the status filter was ignored and archived tasks leaked into results. Added brackets in the SQL.
3. **Artificial delay:** a `Thread.sleep` made short searches slow. Removed it.
4. **Paging crash:** `page=0` caused a 500 error. I clamp page (>= 1) and pageSize (1 to 100).
5. **Stale page number:** changing the search or filter on page 3 gave an empty page. Page now resets to 1.
6. **Stuck loading:** on a failed request the UI showed "Loading..." forever. Fixed in useTasks.js, which also ignores outdated responses.

## How I found them
Manual testing in the browser first, then reading the code at the point where each symptom appeared. Details are in my handwritten notes.

## What I did not change
- An invalid `status` value (like `BANANA`) probably still returns a 500; not tested or fixed.
- I did not review `db/oracle/` or `db/queries/`.
- No automated tests added.

## Biggest remaining risk
Paging is done in memory after loading all matching rows, so it will not scale to large data. It should move into the database query.

## Assumptions
- Archived tasks should never appear in search results.
- A page size above 100 is capped at 100.

## AI tools
I used Claude to guide me step by step, and I verified each fix myself by testing it in the browser.