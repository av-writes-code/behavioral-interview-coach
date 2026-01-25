# Session State - Behavioral Interview Coach Project
**Last Updated:** 2025-01-19
**Purpose:** Track progress on unified interview prep tool

---

## Quick Resume Command
```bash
cd /Users/arpitvyas/Desktop/ruhi-recruiting-prep && claude
```

---

## Project Location
`/Users/arpitvyas/Desktop/ruhi-recruiting-prep/`

## Key Files
- **MAIN APP:** `behavioral-interview-coach.html` (426KB) - **OPEN THIS**
- **V1 (backup):** `amazon-interview-coach-ultimate.html` (393KB)
- **V2 Timeline Viewer:** `v2/timelines/story-timelines-viewer.html` (reference)
- **Artifacts Package:** `artifacts-import/project-handoff/` (architecture specs)

---

## What Has Been Completed

### 1. Unified App Created ✅ (2025-01-19)
- Created `behavioral-interview-coach.html` combining V1 + V2
- **8 Tabs:** Analyze, Story Web, Timeline, Competency Map, Practice, Q&A Review, Guide, Settings
- Version 3.0

### 2. BYOK API Key System ✅
- Modal appears on first load if no key stored
- Key stored in localStorage (never sent anywhere except Anthropic API)
- Settings page allows managing/changing key
- API status shown in header

### 3. Timeline Tab ✅
- Mermaid.js Gantt chart visualization
- Auto-generates from Story Web nodes
- Color-coded STAR sections (Situation, Task, Action, Result)
- Event details table

### 4. Company-Agnostic Competencies ✅
- Company presets: Amazon, Google, Meta, Microsoft
- Custom competency input for any company
- Saved to localStorage
- Header subtitle updates based on target company

### 5. Settings Tab ✅
- API key management
- Company/competency selection
- Data export/import (JSON backup)
- Clear all data option

### 6. Project Files ✅
- `.gitignore` created
- `README.md` updated

---

## What's Next To Build

### Remaining Tasks
1. Add company research tab with web search
2. Remove personal stories, create strong sample stories
3. Test all API features with real key
4. Push to GitHub

### Technical Details Ready
- BYOK implementation code designed
- Claude Web Search API integration designed
- Timeline generator from story nodes designed
- Provenance data model extensions designed
- LLM prompt templates created

---

## Design Documents Created (in agent outputs)

### Product Vision
- MVP scope vs deferred features
- User flow: Add story → Structure → Visualize → Practice
- Universal competency tags (not hardcoded to Amazon)
- Company presets available (Amazon, Google, Meta)

### Company Research Feature
- Uses Claude's web_search tool ($0.05-0.08 per research)
- Searches Glassdoor, Levels.fyi, Reddit, company sites
- Output structure: interview_process, competencies, questions, tips, mental_model
- User can edit all sections

### Non-Hallucinated Story Building
- 5 input methods with provenance tracking
- Extraction → Verification → Structure → Gap Fill → Assembly
- Color-coded UI: Green (verified), Yellow (needs confirm), Red (missing)
- Quality score algorithm (0-100)
- Export blocked until verified

---

## User Preferences Noted
- Tool should work for ANY company, not just Amazon
- Must actually research online (not just use LLM knowledge)
- Stories must be non-hallucinated (grounded in user facts)
- User should be able to modify generated content
- No personal data leaked to GitHub

---

## Resume Checklist
1. Open terminal: `cd /Users/arpitvyas/Desktop/ruhi-recruiting-prep && claude`
2. Tell Claude to read this file
3. Continue with "Create unified app combining V1 and V2"
