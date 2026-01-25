# Session State - Behavioral Interview Coach Project
**Last Updated:** 2025-01-25
**Version:** 3.0
**GitHub:** https://github.com/av-writes-code/behavioral-interview-coach

---

## Project Location
`/Users/arpitvyas/Desktop/ruhi-recruiting-prep/`

## Key Files
- **MAIN APP:** `behavioral-interview-coach.html` (445KB) - **OPEN THIS**
- **V1 (backup):** `amazon-interview-coach-ultimate.html` (not committed)
- **.gitignore:** Excludes personal data
- **README.md:** User documentation

---

## What Has Been Completed ✅

### 1. Unified App Created (2025-01-25)
- Created `behavioral-interview-coach.html` combining V1 + V2
- **9 Tabs:** Analyze, Story Web, Timeline, Competency Map, Practice, Q&A Review, Guide, Research, Settings
- All personal data removed (company-agnostic)

### 2. BYOK API Key System
- Modal appears on first load if no key stored
- Key stored in localStorage (never sent anywhere except Anthropic API)
- Settings page allows managing/changing key
- API status shown in header
- All 18 API calls properly authenticated

### 3. Timeline Tab
- Mermaid.js Gantt chart visualization
- Auto-generates from Story Web nodes
- Color-coded STAR sections (Situation, Task, Action, Result)
- Event details table

### 4. Company-Agnostic Competencies
- Company presets: Amazon, Google, Meta, Microsoft
- Custom competency input for any company
- Saved to localStorage
- Header subtitle updates based on target company

### 5. Company Research Tab
- AI-powered company research
- Returns: interview process, competencies, questions, tips, mental model
- Can apply researched competencies to settings
- Save/load/export research results

### 6. Settings Tab
- API key management
- Company/competency selection
- Data export/import (JSON backup)
- Clear all data option

### 7. GitHub Repository
- Public repo: https://github.com/av-writes-code/behavioral-interview-coach
- Clean of personal data
- Proper .gitignore

---

## Features Working

| Feature | Tab | Status |
|---------|-----|--------|
| Story depth analysis | Analyze | ✅ |
| Story Web builder | Story Web | ✅ |
| Timeline visualization | Timeline | ✅ |
| Competency coverage map | Competency Map | ✅ |
| Practice drills | Practice | ✅ |
| Q&A review | Q&A Review | ✅ |
| Interview guide | Guide | ✅ |
| Company research | Research | ✅ |
| Settings & data management | Settings | ✅ |

---

## Not Implemented (Future Enhancements)

- Non-hallucinated story building with provenance tracking
- Quality gates (verify before export)
- Web search for company research (currently uses LLM knowledge)
- Voice input for stories

---

## To Use

1. Open `behavioral-interview-coach.html` in browser
2. Enter Anthropic API key when prompted
3. (Optional) Go to Research tab to research your target company
4. Build stories in Story Web tab
5. Practice with Practice tab
