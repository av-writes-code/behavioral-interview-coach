# Behavioral Interview Coach

A comprehensive, AI-powered interview preparation tool for behavioral interviews. Built as a single HTML file — no server, no install, no dependencies. Just open it in a browser.

## Getting Started

### 1. Open the App

```
# That's it. Just open the file in your browser:
open behavioral-interview-coach.html

# Or double-click the file in Finder/Explorer
# Or drag it into Chrome/Firefox/Safari/Edge
```

No `npm install`. No `docker run`. No backend. It's one HTML file.

### 2. Add Your API Key

The app uses Claude (Anthropic) for AI features. You need your own API key:

1. Go to [console.anthropic.com/settings/keys](https://console.anthropic.com/settings/keys)
2. Create a new key (name it anything, e.g., "interview-coach")
3. Copy the key — it starts with `sk-ant-api03-...`
4. Paste it into the modal that appears when you first open the app
5. Alternatively: go to **Settings** tab and paste it under "API Configuration"

**Cost:** Each AI call costs ~$0.01–$0.10. A full practice session is roughly $0.10–$0.30. Your key is stored in your browser's localStorage only — never sent anywhere except `api.anthropic.com`.

**No key?** Story Web viewing/editing, Timeline, data export/import, and Settings all work without an API key.

### 3. Pick Your Company

Go to **Settings** and select your target company:

| Preset | Competencies |
|--------|-------------|
| **Amazon** | 14 Leadership Principles (Customer Obsession, Ownership, etc.) |
| **Google** | 7 competencies (Googleyness, General Cognitive Ability, etc.) |
| **Meta** | 5 core values (Move Fast, Be Bold, etc.) |
| **Microsoft** | 5 competencies (Growth Mindset, Customer Obsession, etc.) |
| **Custom** | Enter any company name + your own competency list |

This choice propagates everywhere — practice questions, evaluations, competency maps, all of it.

### 4. Start Prepping

See the tab-by-tab guide below.

---

## Features (Tab by Tab)

### Analyze — Story Depth Analyzer

Paste a behavioral interview story and get an AI score (0–10) on how "meaty" it is. The AI evaluates:
- Metrics and data points
- Technical depth
- Cross-functional impact
- Follow-up question potential

The story auto-saves and generates a Story Web structure for the next tab.

**Also here:** Save your resume and target job posting for personalized AI feedback across all tabs.

### Story Web — STAR Component Builder

Visual card-based view of your story broken into Situation, Task, Action, Result events. Each node has:
- Dimensions (Problem, Solution, Alternatives, Metrics, Tradeoffs)
- Memory anchors (emoji + keyword for recall)
- Detail chips (Difficulty, Stakeholder, Metric, Insight, Follow-up)
- Competency tags

Toggle between **Review Mode** (all visible) and **Practice Mode** (details hidden, click to reveal — for memory training).

**Export:** JSON, Markdown, or standalone interactive HTML.

### Timeline — Gantt Chart View

Select a story to see a Mermaid Gantt chart with STAR phases laid out sequentially. Color-coded: red = Situation, blue = Task/Action, green = Result.

### Practice — Question Drill

1. Pick competencies to practice
2. AI generates a behavioral question (from a question bank, or AI-generated for non-Amazon companies)
3. 60-second target timer with warnings
4. Submit your response for AI evaluation (score, strengths, weaknesses)
5. AI generates bar-raiser-style follow-up questions (depth tracking + pivot logic)
6. Sessions save to history; low-scoring questions go into a spaced repetition review queue

### Simulator — Full Interview Simulation

Two modes:
- **Story mode:** Paste a story, get interviewed about it with realistic follow-ups
- **LP mode:** Pick a competency, get cold-started with a behavioral question

The AI acts as a rigorous interviewer — pivots between competencies, probes for specifics, challenges weak answers.

### Q&A Review — Batch Answer Analyzer

Paste multiple Q&A pairs (`Q: ... A: ...` format) and get:
- Per-pair scores with strengths and suggestions
- Competency tags (colored badges showing which competencies each answer demonstrates)
- Improved STAR-structured answer for each pair

### Competency Map — Coverage Heatmap

Paste 2+ stories separated by `---` and get a heatmap showing which competencies each story covers, gaps in your coverage, and a MECE score.

### Guide — Quick Reference

Static reference card with STAR framework reminders. If you've saved a job posting, shows job-specific tips (top competencies to focus on, key skills).

### Research — Company Interview Research

Enter a company + role and get AI-generated research on:
- Interview process and format
- Key competencies/values they look for
- Common behavioral questions
- Preparation tips

Results come from Claude's training data (not live web search) — a disclaimer warns about potential inaccuracies.

### Settings

- **API Key:** Save, update, or clear your Anthropic API key
- **Company:** Select preset or define custom competencies
- **Data:** Export all data as JSON backup, import from backup, or clear everything

---

## Data & Privacy

All data lives in your browser's `localStorage`. Nothing is sent to any server except Anthropic's API for AI features.

**What's stored:**
- Stories and nodes
- Practice history and review queue
- Resume and job posting
- Company settings and preferences
- Saved research results
- API key

**Backup your data:** Settings → Export All Data → saves a JSON file. Import it on another browser/machine via Settings → Import.

---

## Project Structure

```
behavioral-interview-coach/
├── behavioral-interview-coach.html  # The entire app (open this)
├── README.md                        # This file
├── ACCEPTANCE_CRITERIA.md           # 18 fix criteria from v3.2 audit
├── TESTING_PLAN.md                  # Manual E2E testing plan
├── LICENSE                          # MIT License
└── .gitignore                       # Git ignore patterns
```

## Technical Details

- Single HTML file (~470KB) — all HTML, CSS, and JavaScript inline
- No server, no build step, no dependencies to install
- Uses Claude API (`claude-sonnet-4-5-20250929`) via direct browser fetch
- Mermaid.js v10.9.1 loaded from CDN for Gantt charts
- API calls include 120s timeout, retry with exponential backoff for 429/500 errors
- XSS protection via `escapeHTML()` on AI response fields

## Browser Support

Chrome, Firefox, Safari, Edge — any modern browser with JavaScript enabled. Requires internet for API calls and Mermaid CDN.

## License

[MIT License](LICENSE)

---

*Version 3.2*
