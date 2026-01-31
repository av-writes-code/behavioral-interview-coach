# Acceptance Criteria — v3.2 Audit Fixes

Last updated: 2025-01-31

## How to Verify

Each fix has a **PASS condition** — a concrete check that can be performed by reading the code (grep/search). No browser testing required. Each criterion is binary: PASS or FAIL.

---

## CRITICAL (1-6): Company-Agnostic + Silent Failures

### FIX-1: Replace 4 hardcoded Amazon LP arrays

**What:** 4 locations define `const allLPs = ['Dive Deep', 'Ownership', ...]` or `const defaultLPs = [...]` in active code outside of `COMPANY_PRESETS` and `QUESTION_BANK`.

**PASS when:**
- [ ] Grep for `const allLPs = \[` returns 0 matches (outside COMPANY_PRESETS/QUESTION_BANK)
- [ ] Grep for `const defaultLPs = \[` returns 0 matches (outside COMPANY_PRESETS/QUESTION_BANK)
- [ ] All 4 locations now use `getCompetencies()` or `CURRENT_COMPETENCIES`
- [ ] Locations: node detail modal (~3613), generateDrillFollowUp (~7589), startLPSimulation (~7941), generateSimulatorFollowUp (~8070)

### FIX-2: QUESTION_BANK company-aware fallback

**What:** When a selected competency has no entry in QUESTION_BANK, the app generates a meaningful question via AI instead of the weak generic `"Tell me about a time you demonstrated X"`.

**PASS when:**
- [ ] `generateQuestion()` function checks `QUESTION_BANK[lp]` — if missing/empty, calls Claude API to generate a company-specific behavioral question
- [ ] The AI prompt includes `CURRENT_COMPANY` name and the competency name
- [ ] Fallback to the generic string only if API call also fails (no API key or network error)
- [ ] Non-Amazon companies get substantive first questions, not one-liners

### FIX-3: Evaluation prompt uses CURRENT_COMPETENCIES

**What:** `submitDrillResponse()` sends an evaluation prompt with hardcoded Amazon LP names in the expected JSON schema.

**PASS when:**
- [ ] The evaluation prompt in `submitDrillResponse()` references `CURRENT_COMPETENCIES` or `getCompetencies()` for the LP demonstration fields
- [ ] Grep for `"Dive Deep"` inside `submitDrillResponse` returns 0 matches (outside of user-provided content)
- [ ] The evaluation JSON schema keys are dynamically built from current competencies

### FIX-4: Error handling in generateNextFollowUp() and submitDrillResponse()

**What:** Both functions catch errors silently — `console.error` only, or generic `alert('Error evaluating response.')`.

**PASS when:**
- [ ] `generateNextFollowUp()` has a `response.ok` check before parsing
- [ ] `generateNextFollowUp()` catch block shows user-facing feedback (alert or DOM message), not just console.error
- [ ] `submitDrillResponse()` has a `response.ok` check before parsing
- [ ] `submitDrillResponse()` shows specific error messages for 401, 429, and generic API errors
- [ ] Both functions check `data.content && data.content[0] && data.content[0].text` before accessing

### FIX-5: API key check in loadFromQueue()

**What:** `loadFromQueue()` loads queued questions without checking for API key — user can start practicing but submission will fail.

**PASS when:**
- [ ] `loadFromQueue()` calls `hasApiKey()` at the top and returns with a clear alert if no key
- [ ] The alert message directs user to Settings

### FIX-6: Resume/job context loading in Q&A Review

**What:** `storage.load('user_resume')` returns `{text: "...", savedAt: "..."}` but code checks `typeof resume === 'string'`, which always fails for object storage.

**PASS when:**
- [ ] Resume extraction handles both string and object: `const resumeText = typeof resume === 'string' ? resume : (resume?.text || '')`
- [ ] Job posting extraction handles both: `const jobText = typeof jobPosting === 'string' ? jobPosting : (jobPosting?.description || jobPosting?.text || '')`
- [ ] The prompt includes actual resume/job content when available (not empty string)

---

## HIGH (7-11): Visible UX Gaps

### FIX-7: Preserve competencies_demonstrated in Q&A

**What:** The prompt asks Claude to return `competencies_demonstrated` per Q&A pair, but the validation normalizer drops it and display ignores it.

**PASS when:**
- [ ] Q&A validation normalizer preserves `competencies_demonstrated` as an array (default to `[]`)
- [ ] `displayQAResults()` renders competencies for each pair (e.g., as tags/badges)

### FIX-8: Competency Map header text

**What:** `displayCoverageMap()` renders hardcoded "Leadership Principle" as the grid row header.

**PASS when:**
- [ ] Grep for `Leadership Principle` inside `displayCoverageMap` returns 0 matches
- [ ] Header now reads "Competency" or dynamically uses `${CURRENT_COMPANY} Competency`

### FIX-9: Dynamic LP tag colors for non-Amazon competencies

**What:** CSS only defines colors for Amazon LP class names. All other competencies render as identical gray.

**PASS when:**
- [ ] A `getCompetencyColor(name)` or similar function exists that generates a deterministic color from any competency string
- [ ] Non-Amazon competency tags render with distinct, non-gray colors
- [ ] The function uses a hash or index-based approach so colors are consistent across page loads

### FIX-10: Timeline fake dates + decorative legend

**What:** Timeline uses fabricated `2000-01-XX` dates. Legend colors don't match Mermaid's actual bar colors.

**PASS when:**
- [ ] Grep for `2000-01-` in generateMermaidTimeline returns 0 matches
- [ ] Mermaid gantt chart uses day-index labels (e.g., `day 1`, `day 2`) or sequential labels without calendar dates
- [ ] The color legend is either removed OR replaced with text explaining what Mermaid's built-in colors (crit=red, done=green, default=blue) represent

### FIX-11: Add saved_research and review_queue to export/import

**What:** `exportAllData()` and `handleDataImport()` don't include `saved_research` or `review_queue`, silently losing them.

**PASS when:**
- [ ] `exportAllData()` includes `saved_research: await storage.load('saved_research') || []`
- [ ] `exportAllData()` includes `review_queue: await storage.load('review_queue') || []`
- [ ] `handleDataImport()` restores both keys if present in import file

---

## MEDIUM (12-16): Polish + Safety

### FIX-12: Delete dead updateSTARDisplay()

**What:** `updateSTARDisplay()` references non-existent DOM elements (`timer-s`, `timer-t`, `timer-a`, `timer-r`). It's never called but is dead code.

**PASS when:**
- [ ] `function updateSTARDisplay` is deleted from the file
- [ ] `starTimings` variable declaration is deleted
- [ ] `currentComponent` variable declaration is deleted (if only used for STAR timing)
- [ ] Grep for `updateSTARDisplay` returns 0 matches
- [ ] Grep for `starTimings` returns 0 matches in active code

### FIX-13: Disable analyze button during analyzeStory()

**What:** No mutex/button disable — double-clicking fires two API calls and causes race condition.

**PASS when:**
- [ ] The "Analyze Story Depth" button is disabled at the start of `analyzeStory()`
- [ ] The button is re-enabled in the `finally` block
- [ ] A second click while analyzing does nothing (button is disabled)

### FIX-14: HTML-escape AI response fields before innerHTML

**What:** `analysis.title` and `analysis.overall_feedback` are inserted into innerHTML without escaping — XSS risk.

**PASS when:**
- [ ] A `escapeHTML(str)` utility function exists that escapes `<`, `>`, `&`, `"`, `'`
- [ ] `analysis.title` is escaped before innerHTML insertion in `displayStoryAnalysis()`
- [ ] `analysis.overall_feedback` is escaped before innerHTML insertion
- [ ] All AI-generated string fields displayed via innerHTML go through escapeHTML

### FIX-15: Version compatibility check on import

**What:** `handleDataImport()` only checks `if (!data.version)` — accepts any version without warning.

**PASS when:**
- [ ] Import compares `data.version` against current `APP_VERSION`
- [ ] If versions don't match, user sees a warning with both versions displayed
- [ ] User can still proceed with import after acknowledging the warning (confirm dialog)

### FIX-16: Guard against empty CURRENT_COMPETENCIES

**What:** If `CURRENT_COMPETENCIES` is empty array, AI prompts receive blank competency lists and hallucinate.

**PASS when:**
- [ ] `analyzeCoverage()` checks `CURRENT_COMPETENCIES.length > 0` and alerts if empty
- [ ] `analyzeQA()` checks `CURRENT_COMPETENCIES.length > 0` and alerts if empty
- [ ] Alert directs user to Settings to configure competencies
