# Manual Testing Plan — Behavioral Interview Coach v3.2

Last updated: 2025-02-01

## Prerequisites: API Key Setup

Almost every feature in this app requires a Claude API key. Here's how to get one and where to put it.

### Step 1: Get an Anthropic API Key

1. Go to **https://console.anthropic.com/settings/keys**
2. Log in (or create an account if you don't have one)
3. Click **"Create Key"**
4. Give it a name (e.g., "interview-coach-testing")
5. Copy the key — it looks like: `sk-ant-api03-xxxxxxxxxxxx...`
6. **Important:** You need credits on your account. Check your balance at https://console.anthropic.com/settings/billing. Each test costs roughly $0.01–$0.10 per API call. Full test run ≈ $1–3.

### Step 2: Insert the API Key into the App

**Option A — On first launch (BYOK modal):**
1. Open `behavioral-interview-coach.html` in your browser
2. A modal popup appears: "Enter your Anthropic API Key"
3. Paste your `sk-ant-api03-...` key into the input field
4. Click **"Save Key"**
5. The modal closes — you're ready to test

**Option B — Via Settings (if you skipped the modal):**
1. Open the app → click the **Settings** tab (⚙️ gear icon, last tab)
2. Find the **"API Configuration"** section at the top
3. Paste your key into the password input field
4. Click **"Save API Key"**
5. A success message confirms the key is saved

**Option C — Via browser DevTools (manual):**
1. Open DevTools (F12 or Cmd+Option+I)
2. Go to Console tab
3. Run: `localStorage.setItem('anthropic_api_key', 'sk-ant-api03-YOUR_KEY_HERE')`
4. Refresh the page

### Where the Key is Stored

- The key lives in your browser's `localStorage` under the key `anthropic_api_key`
- It is **never** sent anywhere except `https://api.anthropic.com/v1/messages`
- To verify: DevTools → Application → Local Storage → look for `anthropic_api_key`
- To remove: Settings → "Clear API Key", or `localStorage.removeItem('anthropic_api_key')` in console

### Which Features Need the Key vs. Don't

| Needs API Key | Works Without Key |
|---------------|-------------------|
| Story Analyzer (Analyze tab) | Story Web viewing/editing |
| Practice Mode (all drills) | Timeline visualization |
| Interview Simulator | Data export/import |
| Q&A Review | Settings (company, export) |
| Competency Map | Story Manager |
| Company Research | Guide tab |
| AI node suggestions | Manual node/chip editing |

### API Cost Estimates Per Test Section

| Section | Approx. API Calls | Approx. Cost |
|---------|-------------------|--------------|
| B3: Story Analyzer | 1-2 | $0.05–$0.10 |
| B6: Practice Mode | 3-5 | $0.10–$0.20 |
| B7: Interview Simulator | 3-6 | $0.10–$0.25 |
| B8: Q&A Review | 1-2 | $0.05–$0.10 |
| B9: Competency Map | 1 | $0.05–$0.10 |
| B10: Company Research | 1 | $0.05–$0.10 |
| **Total full run** | **~15-20** | **$0.50–$1.00** |

---

## How to Use This Plan

1. Open `behavioral-interview-coach.html` in Chrome/Firefox
2. **Set up your API key first** (see Prerequisites above)
3. Work through each section in order — earlier tests set up data for later ones
4. Mark each checkbox as you go: `[x]` = PASS, `[ ]` = not tested, `[FAIL]` = broken
5. You can stop at any section boundary — the plan is ordered by priority

---

## PART A: HIGH-LEVEL TEST PLAN (Feature-Level)

| # | Feature Area | What to Verify | Priority |
|---|-------------|----------------|----------|
| A1 | First Launch & API Key | Modal appears, key saves, skip works | P0 |
| A2 | Settings: Company Selection | Presets load, custom works, competencies propagate | P0 |
| A3 | Story Analyzer | AI analysis, auto-save, score display | P0 |
| A4 | Story Web Builder | Nodes render, edit/delete, chips work | P0 |
| A5 | Timeline View | Mermaid chart renders, no fake dates | P1 |
| A6 | Practice Mode | Question generation, timer, evaluation, follow-ups | P0 |
| A7 | Interview Simulator | Both modes (story/LP), conversation flow | P1 |
| A8 | Q&A Review | Batch analysis, competency tags, escaping | P1 |
| A9 | Competency Map | Coverage heatmap, empty guard, matrix validation | P1 |
| A10 | Company Research | AI research, disclaimer, save/export | P1 |
| A11 | Data Export/Import | Full backup, version check, all keys present | P1 |
| A12 | Cross-Cutting: Company Switch | Changing company updates all tabs | P0 |
| A13 | Cross-Cutting: No API Key | All features degrade gracefully | P1 |
| A14 | Cross-Cutting: Error Handling | API failures show user-friendly messages | P2 |

---

## PART B: DETAILED STEP-BY-STEP TEST PLAN

---

### B1. First Launch & API Key Setup

**Precondition:** Clear all localStorage (`Settings → Clear All Data`, or DevTools → Application → Clear)

- [ ] **B1.1** Open the HTML file — BYOK modal appears automatically
- [ ] **B1.2** Click "Skip" — modal closes, app loads in limited mode
- [ ] **B1.3** Go to Settings tab → API Key section shows empty
- [ ] **B1.4** Enter a valid API key in Settings → click Save → success message shown
- [ ] **B1.5** Refresh the page — BYOK modal should NOT appear (key is saved)
- [ ] **B1.6** Go to Settings → Clear API Key → confirm key is removed
- [ ] **B1.7** Refresh — BYOK modal appears again

---

### B2. Settings: Company Selection

**Precondition:** API key is saved

- [ ] **B2.1** Settings tab → Company dropdown shows: Amazon, Google, Meta, Microsoft, Custom
- [ ] **B2.2** Select "Amazon" → competencies list shows 14 Leadership Principles
- [ ] **B2.3** Select "Google" → competencies list shows 7 items (Googleyness, etc.)
- [ ] **B2.4** Select "Meta" → competencies list shows 5 items (Move Fast, etc.)
- [ ] **B2.5** Select "Microsoft" → competencies list shows 5 items (Growth Mindset, etc.)
- [ ] **B2.6** Select "Custom" → company name field and competencies textarea appear
- [ ] **B2.7** Enter custom company "Stripe" with competencies: "Users First", "Move with urgency", "Think rigorously" → Save
- [ ] **B2.8** After save, current competencies display shows "Stripe" competencies
- [ ] **B2.9** Navigate to Practice tab → LP checkboxes show Stripe competencies (not Amazon)
- [ ] **B2.10** Navigate to Competency Map tab → verify header/labels don't say "Leadership Principle"
- [ ] **B2.11** Settings → Reset → confirm dialog → resets to Amazon
- [ ] **B2.12** Practice tab → checkboxes show Amazon LPs again

---

### B3. Story Analyzer (Tab: Analyze)

**Precondition:** API key saved, company set (Amazon or any)

**Setup — Save resume and job posting first:**
- [ ] **B3.1** Expand "Resume" section → paste any resume text → Save → "Resume saved" status shows
- [ ] **B3.2** Expand "Job Posting" section → enter Title, Description → Save → status shows saved
- [ ] **B3.3** Verify Guide tab (if applicable) shows job-specific section

**Story Analysis:**
- [ ] **B3.4** Paste a behavioral interview story (200+ words, STAR format) into the story textarea
- [ ] **B3.5** Click "Analyze Story Depth" → button becomes disabled (not clickable again)
- [ ] **B3.6** Loading spinner appears
- [ ] **B3.7** After API response: score (0-10), feedback, depth indicators, follow-up questions displayed
- [ ] **B3.8** "Saved: [title]" badge appears — story auto-saved
- [ ] **B3.9** Button re-enables after completion
- [ ] **B3.10** Click "Analyze Story Depth" again quickly 2x — only one request fires (button disabled during first)
- [ ] **B3.11** Open Story Manager modal → the analyzed story appears in the list

**Error case:**
- [ ] **B3.12** Clear API key → try Analyze → should show API key error alert
- [ ] **B3.13** Try analyzing with empty textarea → alert "Please paste your story first!"

---

### B4. Story Web Builder (Tab: Story Web)

**Precondition:** At least one story analyzed (from B3)

- [ ] **B4.1** Select the story from the dropdown → story web loads with nodes
- [ ] **B4.2** Nodes display with STAR component colors (Situation/Task/Action/Result)
- [ ] **B4.3** Each node shows emoji anchor, title, and STAR label
- [ ] **B4.4** Click a node → detail modal opens with dimensions, memory anchor, LP tags
- [ ] **B4.5** LP tags in the modal use the correct competencies (from current company, not hardcoded Amazon)
- [ ] **B4.6** Click "Edit" → fields become editable
- [ ] **B4.7** Change the event title → Save → title updates in the web
- [ ] **B4.8** Add a Detail Chip (type: Metric) with content "Reduced latency by 40%" → chip appears
- [ ] **B4.9** Delete the chip → chip removed
- [ ] **B4.10** Toggle to Practice Mode → details hidden, click to reveal
- [ ] **B4.11** Toggle back to Review Mode → all details visible

**Export:**
- [ ] **B4.12** Export → JSON → file downloads, open it, verify valid JSON with nodes
- [ ] **B4.13** Export → Markdown → file downloads with STAR sections
- [ ] **B4.14** Export → Interactive HTML → file downloads, open in browser, renders standalone

---

### B5. Timeline View (Tab: Timeline)

**Precondition:** At least one story with nodes exists

- [ ] **B5.1** Select the story from dropdown → Mermaid Gantt chart renders
- [ ] **B5.2** Chart shows sections: SITUATION, TASK, ACTION, RESULT
- [ ] **B5.3** Verify NO calendar dates (no "Jan 01", "Jan 02" etc.) — uses relative sequencing
- [ ] **B5.4** Legend shows: red = Situation (crit), blue = Task/Action (default), green = Result (done)
- [ ] **B5.5** Legend colors approximately match the actual bar colors in the Mermaid chart
- [ ] **B5.6** Event Details table below shows all nodes with phase, title, key details
- [ ] **B5.7** If Mermaid CDN fails (disconnect internet, reload) → fallback error message appears

---

### B6. Practice Mode (Tab: Practice)

**Precondition:** API key saved, at least one company selected

**Starting a drill:**
- [ ] **B6.1** Competency checkboxes reflect current company (not hardcoded Amazon LPs)
- [ ] **B6.2** Check 2-3 competencies → click "Start Practice"
- [ ] **B6.3** A question appears (AI-generated if competency not in QUESTION_BANK)
- [ ] **B6.4** Timer starts counting up, shows warning at 60s

**Submitting a response:**
- [ ] **B6.5** Type a STAR-formatted response → click Submit
- [ ] **B6.6** Evaluation appears with score/100, strengths, weaknesses
- [ ] **B6.7** Evaluation's `lpDemonstration` section uses current competencies (not "Dive Deep")
- [ ] **B6.8** "Generate Follow-Up" button appears → click it → follow-up question appears
- [ ] **B6.9** Follow-up shows depth indicator (Depth 1/2 or PIVOT)
- [ ] **B6.10** Submit follow-up answer → can generate another follow-up
- [ ] **B6.11** Click "End Session" → session saved to practice history

**Error handling:**
- [ ] **B6.12** If API returns error → alert shows specific message (not just "Error")
- [ ] **B6.13** For 401 error → message mentions "Invalid API key"

**Review Queue (Spaced Repetition):**
- [ ] **B6.14** Complete a drill with score < 80 → question added to review queue
- [ ] **B6.15** Click "Load from Queue" → loads the queued question
- [ ] **B6.16** Without API key, "Load from Queue" → alert about API key requirement

**Non-Amazon company test:**
- [ ] **B6.17** Switch to Google in Settings → Practice tab → checkboxes show Google competencies
- [ ] **B6.18** Start drill with "Googleyness" checked → question generated via AI (not from QUESTION_BANK)
- [ ] **B6.19** Question is company-specific (mentions Google context)

---

### B7. Interview Simulator (Tab: Simulator)

**Option A — Story-based:**
- [ ] **B7.1** Paste a story → click "Start Simulation" → first interviewer question appears
- [ ] **B7.2** Type response → submit → conversation thread grows
- [ ] **B7.3** Click "Next Question" → follow-up generated based on conversation
- [ ] **B7.4** "Suggest Response" → AI provides a model answer
- [ ] **B7.5** "Generate Node" → creates a Story Web node from conversation
- [ ] **B7.6** "Reset" → clears conversation

**Option B — LP-based:**
- [ ] **B7.7** Select a competency from dropdown → click "Start LP Simulation"
- [ ] **B7.8** LP options reflect current company competencies (not hardcoded Amazon)
- [ ] **B7.9** First question appears → conversation flow works
- [ ] **B7.10** After 2 deep questions, interviewer pivots to explore new competency

---

### B8. Q&A Review (Tab: Q&A Review)

**Precondition:** API key saved, competencies configured

- [ ] **B8.1** Paste Q&A pairs in format `Q: ... A: ...` → click Analyze
- [ ] **B8.2** Loading indicator shows
- [ ] **B8.3** Results show: overall score, per-pair scores, strengths, suggestions
- [ ] **B8.4** Each pair shows **competency tags** as colored badges (from FIX-7)
- [ ] **B8.5** Tag colors are non-gray and consistent (same competency = same color)
- [ ] **B8.6** Improved answers are displayed with escaping (no raw HTML renders)
- [ ] **B8.7** Clear button resets the form

**Empty competencies guard:**
- [ ] **B8.8** Set custom company with 0 competencies → try Analyze → alert about configuring competencies

**Resume/job context test:**
- [ ] **B8.9** Save a resume (from Analyze tab) → run Q&A analysis → check that AI prompt references resume context (visible in improved suggestions being more personalized)

---

### B9. Competency Map (Tab: Competency Map)

**Precondition:** 2+ stories available

- [ ] **B9.1** Paste 2-3 stories separated by `---` → click Analyze
- [ ] **B9.2** Heatmap grid appears with column header "Competency" (not "Leadership Principle")
- [ ] **B9.3** Title says "Competency Coverage Heat Map"
- [ ] **B9.4** Grid rows match `CURRENT_COMPETENCIES` — no hallucinated extra rows
- [ ] **B9.5** Strongest competencies and gaps sections display
- [ ] **B9.6** MECE score shows (0-10)

**Guards:**
- [ ] **B9.7** No API key → alert about API key
- [ ] **B9.8** Empty competencies → alert directing to Settings
- [ ] **B9.9** Empty input → alert "Please paste your stories"

---

### B10. Company Research (Tab: Research)

- [ ] **B10.1** Enter company name and role → click Research
- [ ] **B10.2** Loading indicator shows (30-60 seconds expected)
- [ ] **B10.3** Results display: Interview Process, Key Competencies, Common Questions, Tips
- [ ] **B10.4** **Yellow disclaimer** appears above results: "AI-generated information based on training data. May be outdated or inaccurate — verify independently."
- [ ] **B10.5** "Use These Competencies" button → updates app competencies to researched ones
- [ ] **B10.6** "Save Research" → saved to storage
- [ ] **B10.7** "Export as Text" → text file downloads
- [ ] **B10.8** Previously saved research appears in list below
- [ ] **B10.9** Can load and delete saved research items

---

### B11. Data Export/Import (Settings)

**Export:**
- [ ] **B11.1** Settings → Export All Data → JSON file downloads
- [ ] **B11.2** Open file → verify it contains keys: `version`, `stories`, `practice_history`, `user_resume`, `target_job`, `company_settings`, `preferences`, `saved_research`, `review_queue`
- [ ] **B11.3** `saved_research` and `review_queue` are present (not null if data exists)

**Import — version match:**
- [ ] **B11.4** Import the file just exported → "This will replace all your current data" confirm → data loads, page reloads
- [ ] **B11.5** All data intact after reload (stories, settings, research)

**Import — version mismatch:**
- [ ] **B11.6** Edit the exported JSON file: change `"version"` to `"2.0"` → Import
- [ ] **B11.7** Warning appears: "This backup is version 2.0 but app is version X. Import anyway?"
- [ ] **B11.8** Click Cancel → import aborted, data unchanged
- [ ] **B11.9** Click OK → import proceeds normally

**Import — invalid file:**
- [ ] **B11.10** Try importing a non-JSON file → error alert
- [ ] **B11.11** Try importing JSON without `version` key → "Invalid backup file format" alert

**Clear all:**
- [ ] **B11.12** Settings → Clear All Data → two-step confirm (dialog + type "delete") → all data cleared, page reloads
- [ ] **B11.13** After clear: BYOK modal appears, no stories, no settings

---

### B12. Cross-Cutting: Company Switch Propagation

**Purpose:** Verify that changing company in Settings updates ALL tabs

- [ ] **B12.1** Set company to Amazon → verify:
  - Practice tab: Amazon LP checkboxes
  - Simulator tab: Amazon LP dropdown options
  - AI prompts reference Amazon (check evaluation output)
- [ ] **B12.2** Switch to Google → verify:
  - Practice tab: Google competency checkboxes (not Amazon)
  - Simulator tab: Google competency dropdown
  - Run a Practice drill → evaluation references Google competencies
- [ ] **B12.3** Switch to Custom ("Stripe") → verify:
  - Practice tab: custom competency checkboxes
  - Q&A Review: competency tags use custom names with unique colors
  - Competency Map: grid uses custom competency names

---

### B13. Cross-Cutting: No API Key Mode

**Precondition:** Clear API key

- [ ] **B13.1** Story Analyzer → Analyze → error about API key
- [ ] **B13.2** Practice → Start Drill → error about API key
- [ ] **B13.3** Practice → Load from Queue → alert "API key required..."
- [ ] **B13.4** Q&A Review → Analyze → error about API key
- [ ] **B13.5** Competency Map → Analyze → error about API key
- [ ] **B13.6** Company Research → Research → error about API key
- [ ] **B13.7** Story Web → still viewable (read-only), AI suggestions fail gracefully
- [ ] **B13.8** Timeline → still viewable (no API needed for Mermaid rendering)
- [ ] **B13.9** Settings → Export/Import still work (no API needed)

---

### B14. Cross-Cutting: Error Handling

**Purpose:** Verify API errors show user-friendly messages

- [ ] **B14.1** Enter an invalid API key (e.g., "sk-invalid-key") → try any AI feature
- [ ] **B14.2** Alert should say "Invalid API key" (not generic "Error")
- [ ] **B14.3** `submitDrillResponse()` with bad key → "Invalid API key. Please check your key in Settings."
- [ ] **B14.4** `generateNextFollowUp()` with bad key → alert with error details (not silent)
- [ ] **B14.5** Disconnect internet → try AI feature → error about network/timeout

---

### B15. Security: XSS Prevention (FIX-14)

**Purpose:** Verify HTML escaping prevents script injection

- [ ] **B15.1** In Story Analyzer, paste a story containing `<script>alert('xss')</script>` and `<img onerror=alert(1) src=x>` → Analyze
- [ ] **B15.2** In the results, verify the title and feedback display the raw text (escaped) and do NOT execute scripts
- [ ] **B15.3** In Q&A Review, paste: `Q: <b>bold</b> question? A: <script>alert(1)</script> answer` → Analyze
- [ ] **B15.4** Results show escaped HTML text, no script execution, no bold rendering

---

## PART C: REGRESSION CHECKLIST

After all fixes, verify these previously-working features still work:

- [ ] **C1** Tab navigation — all 9+ tabs switch correctly
- [ ] **C2** Story Manager modal opens and lists stories
- [ ] **C3** Node detail modal opens and closes
- [ ] **C4** Audio recording in Practice Mode (if browser supports it)
- [ ] **C5** Mermaid CDN loads and renders charts
- [ ] **C6** Analytics Dashboard shows practice history
- [ ] **C7** Spaced Repetition scoring logic (score < 80 → queued)
- [ ] **C8** Multiple stories can be saved and selected independently
- [ ] **C9** LocalStorage persists across page refreshes
- [ ] **C10** App works without internet (except AI features and Mermaid CDN)

---

## PART D: BROWSER COMPATIBILITY

Test on at least 2 browsers:

| Test | Chrome | Firefox | Safari | Edge |
|------|--------|---------|--------|------|
| App loads | | | | |
| Tab navigation | | | | |
| API calls work | | | | |
| Mermaid renders | | | | |
| Export downloads | | | | |
| Import loads | | | | |
| localStorage persists | | | | |

---

## PART E: ACCEPTANCE CRITERIA VERIFICATION

Cross-reference each fix against `ACCEPTANCE_CRITERIA.md`:

| Fix | Test Steps | Status |
|-----|-----------|--------|
| FIX-1 | B6.1, B6.17, B7.8, B12.2 | |
| FIX-2 | B6.18, B6.19 | |
| FIX-3 | B6.7 | |
| FIX-4 | B6.12, B6.13, B14.3, B14.4 | |
| FIX-5 | B6.16 | |
| FIX-6 | B8.9 | |
| FIX-7 | B8.4, B8.5 | |
| FIX-8 | B9.2, B9.3 | |
| FIX-9 | B8.5, B12.3 | |
| FIX-10 | B5.3, B5.4, B5.5 | |
| FIX-11 | B11.2, B11.3 | |
| FIX-12 | Grep verification (code-level) | |
| FIX-13 | B3.5, B3.9, B3.10 | |
| FIX-14 | B15.1-B15.4 | |
| FIX-15 | B11.6-B11.9 | |
| FIX-16 | B8.8, B9.8 | |
| FIX-17 | B10.4 | |
| FIX-18 | B9.4 | |

---

## Quick Smoke Test (5-minute version)

If you only have a few minutes, hit these critical paths:

1. Open app → enter API key → go to Settings → switch to Google
2. Analyze tab → paste a story → click Analyze → verify score appears, button disables/re-enables
3. Practice tab → verify Google checkboxes → start drill → submit response → check evaluation uses Google competencies
4. Settings → Export → open JSON → verify `saved_research` and `review_queue` keys exist
5. Q&A Review → paste Q&A → analyze → verify competency tags appear as colored badges
