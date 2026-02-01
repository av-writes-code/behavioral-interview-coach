# Acceptance Criteria — v3.2 Audit Fixes

Last updated: 2025-01-31 (audited)

## How to Verify

Each fix has a **PASS condition** — a concrete check that can be performed by reading the code (grep/search). No browser testing required. Each criterion is binary: PASS or FAIL.

**Verification method:** All criteria use function-name references (not line numbers) since edits shift lines.

---

## CRITICAL (1-6): Company-Agnostic + Silent Failures

### FIX-1: Replace hardcoded Amazon LP arrays

**What:** Multiple locations define `const allLPs = ['Dive Deep', ...]` or `const defaultLPs = ['Customer Obsession', ...]` in active code outside of `COMPANY_PRESETS` and `QUESTION_BANK`.

**PASS when:**
- [ ] Grep for `const allLPs = \[` in functions outside COMPANY_PRESETS returns 0 matches with Amazon LP strings
- [ ] Grep for `const defaultLPs = \[` in functions outside COMPANY_PRESETS returns 0 matches with Amazon LP strings
- [ ] All locations now use `getCompetencies()` or `CURRENT_COMPETENCIES`
- [ ] Affected functions: node detail modal, `generateDrillFollowUp`, `startLPSimulation`, `generateSimulatorFollowUp`

### FIX-2: QUESTION_BANK company-aware fallback

**What:** When a selected competency has no entry in QUESTION_BANK, `generateQuestion()` returns the weak generic `"Tell me about a time you demonstrated X"`.

**PASS when:**
- [ ] `generateQuestion()` checks `QUESTION_BANK[lp]` — if missing/empty, calls `callClaudeAPI()` to generate a company-specific behavioral question
- [ ] The AI prompt includes `CURRENT_COMPANY` and the competency name
- [ ] Fallback to the generic string only if API call also fails (no key or network error)
- [ ] The generic fallback string includes `CURRENT_COMPANY`: e.g., `"Tell me about a time at ${CURRENT_COMPANY} you demonstrated ${lp}"`

### FIX-3: Evaluation prompt uses CURRENT_COMPETENCIES

**What:** `submitDrillResponse()` sends an evaluation prompt with hardcoded Amazon LP name `"Dive Deep"` in the expected JSON schema example.

**PASS when:**
- [ ] The evaluation prompt in `submitDrillResponse()` builds the `lpDemonstration` example keys from `getCompetencies()` (e.g., first 2 competencies)
- [ ] Grep for literal `"Dive Deep"` inside `submitDrillResponse` function body returns 0 matches
- [ ] The prompt instructs Claude to evaluate against `CURRENT_COMPETENCIES` (listed in the prompt text)

### FIX-4: Error handling in generateNextFollowUp() and submitDrillResponse()

**What:** Both functions swallow errors — `console.error` only, or generic `alert('Error evaluating response.')`.

**PASS when:**
- [ ] `generateNextFollowUp()`: has `if (!response.ok)` check before parsing response body
- [ ] `generateNextFollowUp()`: catch block calls `alert()` with error details (not just console.error)
- [ ] `generateNextFollowUp()`: checks `data.content && data.content[0] && data.content[0].text` before accessing
- [ ] `submitDrillResponse()`: has `if (!response.ok)` check before parsing response body
- [ ] `submitDrillResponse()`: 401 errors show "Invalid API key" message, 429 errors show "Rate limit" message
- [ ] `submitDrillResponse()`: checks `data.content && data.content[0] && data.content[0].text` before accessing

### FIX-5: API key check in loadFromQueue()

**What:** `loadFromQueue()` loads queued questions without checking for API key — user can start practicing but submission will fail.

**PASS when:**
- [ ] The `async function loadFromQueue()` implementation (the one with `spacedRepetition.getQueuedQuestions()`) calls `hasApiKey()` at the top
- [ ] If no key, returns with `alert('API key required...')` directing user to Settings

### FIX-6: Resume/job context loading in Q&A Review

**What:** `storage.load('user_resume')` returns `{text: "...", savedAt: "..."}` (object) but `analyzeQA()` checks `typeof resume === 'string'`, which always fails for objects.

**PASS when:**
- [ ] Resume extraction: `typeof resume === 'string' ? resume : (resume?.text || resume?.content || '')`
- [ ] Job extraction: `typeof jobPosting === 'string' ? jobPosting : (jobPosting?.description || jobPosting?.text || '')`
- [ ] The prompt template uses these extracted strings (not the raw storage objects)
- [ ] When resume is stored as `{text: "My resume...", savedAt: "..."}`, the prompt receives `"My resume..."` not `""`

---

## HIGH (7-11): Visible UX Gaps

### FIX-7: Preserve competencies_demonstrated in Q&A

**What:** The prompt asks Claude to return `competencies_demonstrated` per Q&A pair, but the validation normalizer drops it and display ignores it.

**PASS when:**
- [ ] Q&A pair normalizer includes: `competencies_demonstrated: Array.isArray(pair.competencies_demonstrated) ? pair.competencies_demonstrated : []`
- [ ] `displayQAResults()` renders competencies for each pair as colored tags/badges
- [ ] Tags use `getCompetencyColor()` (from FIX-9) for coloring

### FIX-8: Competency Map header text

**What:** `displayCoverageMap()` renders hardcoded "Leadership Principle" as the heatmap grid row header.

**PASS when:**
- [ ] Grep for the string `Leadership Principle` inside `displayCoverageMap` function returns 0 matches
- [ ] Header reads `"Competency"` or `"${CURRENT_COMPANY} Competency"`

### FIX-9: Dynamic competency tag colors for non-Amazon companies

**What:** CSS only defines colors for Amazon LP class names (`.lp-tag.dive-deep`, etc.). All non-Amazon competencies render as identical slate gray.

**PASS when:**
- [ ] A `getCompetencyColor(name)` function exists that returns a hex color string
- [ ] Function uses a deterministic hash so the same competency always gets the same color
- [ ] The function is used wherever LP tags are rendered (story nodes, Q&A results, etc.)
- [ ] Calling `getCompetencyColor('Googleyness')` returns a non-gray color different from `getCompetencyColor('Move Fast')`

### FIX-10: Timeline: remove fake calendar dates, fix legend

**What:** Timeline uses fabricated `2000-01-XX` dates. Legend shows colors that don't match Mermaid's actual bar colors.

**PASS when:**
- [ ] Grep for `2000-01-` in `generateMermaidTimeline` returns 0 matches
- [ ] Mermaid gantt syntax uses relative durations or sequential IDs (not calendar dates)
- [ ] The old color legend (showing pastel STAR colors) is replaced with an accurate legend showing: Mermaid `crit` style = Situation (red), `done` style = Result (green), default style = Task/Action (blue)

### FIX-11: Add saved_research and review_queue to export/import

**What:** `exportAllData()` and `handleDataImport()` don't include `saved_research` or `review_queue`, silently losing them.

**PASS when:**
- [ ] `exportAllData()` data object includes key `saved_research` loaded from storage
- [ ] `exportAllData()` data object includes key `review_queue` loaded from storage
- [ ] `handleDataImport()` restores `saved_research` if present in import data
- [ ] `handleDataImport()` restores `review_queue` if present in import data

---

## MEDIUM (12-16): Polish + Safety

### FIX-12: Delete dead STAR timing code

**What:** `updateSTARDisplay()` references non-existent DOM elements (`timer-s` etc.). `starTimings` and `currentComponent` variables are unused. All are dead code.

**PASS when:**
- [ ] Grep for `function updateSTARDisplay` returns 0 matches
- [ ] Grep for `let starTimings` returns 0 matches
- [ ] Grep for `let currentComponent` returns 0 matches
- [ ] Grep for `starTimings\[` returns 0 matches in active code (reset in `resetDrill` is OK to remove too)

### FIX-13: Disable analyze button during analyzeStory()

**What:** No button disable — double-clicking fires two API calls, causing a race condition.

**PASS when:**
- [ ] The `onclick="analyzeStory()"` button element has an `id` attribute (e.g., `id="analyze-btn"`)
- [ ] First line of `analyzeStory()` disables the button: `document.getElementById('analyze-btn').disabled = true`
- [ ] `finally` block re-enables: `document.getElementById('analyze-btn').disabled = false`

### FIX-14: HTML-escape AI response fields before innerHTML

**What:** `analysis.title`, `analysis.overall_feedback`, Q&A `pair.question`, `pair.answer`, `pair.improved_answer` are inserted into innerHTML without escaping — XSS risk via prompt injection.

**PASS when:**
- [ ] A `function escapeHTML(str)` exists that replaces `&` with `&amp;`, `<` with `&lt;`, `>` with `&gt;`, `"` with `&quot;`, `'` with `&#039;`
- [ ] `displayStoryAnalysis()`: `analysis.title` passed through `escapeHTML()` before innerHTML
- [ ] `displayStoryAnalysis()`: `analysis.overall_feedback` passed through `escapeHTML()` before innerHTML
- [ ] `displayQAResults()`: `pair.question`, `pair.answer`, `pair.improved_answer` passed through `escapeHTML()`

### FIX-15: Version compatibility check on import

**What:** `handleDataImport()` only checks `if (!data.version)` — accepts any version without warning.

**PASS when:**
- [ ] After version existence check, code compares `data.version !== APP_VERSION`
- [ ] If mismatch: `confirm('This backup is version ${data.version} but app is version ${APP_VERSION}. Import anyway?')`
- [ ] If user cancels confirm, import aborts
- [ ] If user confirms, import proceeds normally

### FIX-16: Guard against empty CURRENT_COMPETENCIES

**What:** If `CURRENT_COMPETENCIES` is an empty array, AI prompts receive blank competency lists and hallucinate.

**PASS when:**
- [ ] `analyzeCoverage()`: checks `CURRENT_COMPETENCIES.length === 0` before API call; if empty, shows alert directing to Settings
- [ ] `analyzeQA()`: same check and alert
- [ ] Alert text includes: "Go to Settings to configure competencies"

---

## ADDITIONAL (17-18): Discovered During Audit

### FIX-17: AI-generated data disclaimer in Company Research

**What:** Company Research results come from Claude's training data, which may be outdated or inaccurate. No disclaimer warns users.

**PASS when:**
- [ ] A visible disclaimer appears above the research results container (before `id="research-results"`)
- [ ] Text includes: "AI-generated information based on training data. May be outdated or inaccurate — verify independently."
- [ ] Disclaimer is styled as a warning (yellow/orange background)

### FIX-18: Validate coverage_matrix competencies match CURRENT_COMPETENCIES

**What:** `analyzeCoverage()` can return AI-hallucinated competency names that don't match `CURRENT_COMPETENCIES`.

**PASS when:**
- [ ] After JSON parse in `analyzeCoverage()`, code filters `coverage_matrix` keys to only include those in `CURRENT_COMPETENCIES`
- [ ] Hallucinated competencies (keys not in `CURRENT_COMPETENCIES`) are removed from the matrix
- [ ] If any keys were removed, `console.warn()` logs which ones
- [ ] Display still works correctly with the filtered matrix
