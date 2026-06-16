# Project Review: GIFTS Training Manager

**Reviewed:** 2026-06-14  
**Updated:** 2026-06-14 (author feedback on review items 1–4)

## Summary

The project task is **substantially complete**. The marking scheme validates cleanly (21 points, 49 aspects, 0 errors). README, metadata, assets, and mark distribution are in good shape.

**Author decisions (accepted):**

- **Data model placement** — fixed: `### Data model` under *General Description*.
- **Business rules in modal editing** — intentional: rules apply to editing operations and stay in the day-details modal section.
- **Rule 8 (“at most twice”)** — sufficient: blocking a third scheduling is implicit in the limit; no extra wording required.
- **Type badges** — fixed in description (`T` / `E` / `O`); marking scheme to be updated separately by author.

**Remaining work:** grammar/typos in business rules and statistics, export/persistence polish, and marking-scheme badge notation alignment.

**Verdict:** Ready for integration after remaining **important** language fixes below (non-blocking structural disputes resolved).

---

## Issues Found

### Critical Issues

_None remaining from original review (items 1–4 resolved or accepted per author)._

### Important Issues

1. **Grammar and typos in validation rules** (modal section, lines 208–215)
   - “Event types may only `project task`, `evaluation`or `other`.” — broken grammar and missing space.
   - “Each day **have** at most” → **has**.
   - “may have **event**” → **events**.
   - Rule 3 should use `` `task` `` / `` `evaluation` `` / `` `other` `` consistently.

2. **Typos in Statistics** (lines 248–249)
   - **evaulation** → **evaluation** (twice).

3. **Export section incomplete** (optional polish)
   - Missing: `collection.json` is **not** exported.
   - Missing: event objects must **not** include runtime-only fields (e.g. UI `id`).
   - Missing: do not persist collection `displayName` / `estTime` in schedule JSON — resolve at runtime via `name`.
   - Typo: “provided`training.json`” → “provided `training.json`”.

4. **Reschedule constraints under-specified** (optional)
   - Reschedule bullet omits target date within **training period** and **122-day cap** when moving to a date without events.

5. **Persistence section gaps** (optional)
   - No explicit `localStorage` mention; dynamic day add behaviour not described (empty-day removal covered under Export).

6. **Responsive layout wording**
   - “Main features **might** be usable” is weaker than marking scheme (“usable on desktop resolution”).

7. **Marking scheme — type badges** (author to update)
   - Description now uses `T` / `E` / `O`; marking scheme still references `[T]`/`[E]`/`[O]` in calendar cell test.

### Minor Issues

1. Duplicate theme/fullscreen descriptions (header table + `####` subsections).
2. Kanban UI labels vs JSON `kanbanStatus` values — acceptable; optional one-line note.
3. WSOS S3/S4 balance warnings — expected for client-only module.

---

## Content Quality Issues

### Duplicate Content

- Header controls (theme, fullscreen) described in table and `####` subsections — optional consolidation.

### Language and Clarity

| Location | Issue |
| -------- | ----- |
| Validation rules 208–215 | Grammar errors |
| Statistics 248–249 | `evaulation` typo |
| Export 266 | Missing space before `` `training.json` `` |
| Layout 89 | “might be usable” → stronger requirement |

---

## Compliance Checklist

- [x] README.md structure
- [x] project-description.md header hierarchy (`### Data model` under General Description)
- [x] Required main sections present
- [x] metadata.json valid and complete
- [x] marking-scheme.json valid (21 points, 0 validator errors)
- [x] Assets properly organized
- [x] No broken image references
- [x] No working files (`projectplan.md`, `*.xlsx`)
- [ ] Marking scheme badge notation aligned with description (author pending)
- [ ] Grammar/typos in validation rules and statistics

---

## Marking Scheme Summary

| Section | Points |
| ------- | ------ |
| S1 | 2.5 |
| S2 | 1.75 |
| S3 | 5.0 |
| S4 | 11.75 |
| **Total** | **21** |

Validator: **0 errors**, 2 WSOS balance warnings (expected).

---

## Recommendations

**Before integration (language only)**

1. Fix grammar in validation rules (lines 208–215).
2. Fix `evaulation` typos in Statistics.
3. Fix `provided`training.json`` spacing in Export.

**Author follow-up**

4. Update marking scheme calendar aspect: `T`/`E`/`O` instead of `[T]`/`[E]`/`[O]`.

**Optional polish**

5. Strengthen responsive-layout wording.
6. Expand export/persistence notes if desired.
7. Export marking scheme to Excel (Process 2) if needed for organizers.
