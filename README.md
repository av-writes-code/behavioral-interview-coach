# Behavioral Interview Coach

A comprehensive, AI-powered interview preparation tool for behavioral interviews. Built as a single HTML file with no backend required.

## Features

### Core Capabilities
- **Story Analyzer**: Evaluate if your story has sufficient depth for rigorous follow-up questions, with AI scoring and feedback
- **Story Web Builder**: Build stories with STAR components (Situation, Task, Action, Result) displayed as organized card views
- **Timeline View**: Visualize your stories as Mermaid Gantt charts showing sequential flow of events
- **Practice Mode**: Practice sessions with a 60-second target timer and AI-generated follow-up questions
- **Q&A Review**: Analyze your question-answer pairs against competency frameworks for strengths and improvements
- **Competency Map**: AI-analyzed coverage heatmap of competencies across your stories
- **Company Research**: Research company interview processes using Claude's knowledge base

### AI-Powered Features
- Story depth analysis and scoring (0-10 scale)
- Dynamic follow-up question generation based on your responses
- Probing questions informed by interview best practices
- Suggested response improvements with STAR framework evaluation
- Company interview research (uses Claude's training knowledge, not live web search)

### Bring Your Own Key (BYOK)
This app uses the Claude API for AI features. You'll need your own Anthropic API key:
1. Get a key from [Anthropic Console](https://console.anthropic.com/settings/keys)
2. Enter it when prompted on first load, or go to Settings
3. Your key is stored locally in your browser - never sent anywhere except Anthropic's API
4. Non-AI features (Story Web, Timeline) work without an API key

### Company-Agnostic
Supports multiple company competency frameworks:
- Amazon Leadership Principles
- Google Googleyness
- Meta Core Values
- Microsoft Growth Mindset
- Custom competencies for any company

Changing the company in Settings updates competency options across all tabs.

## Quick Start

1. Open `behavioral-interview-coach.html` in any modern browser
2. Enter your Anthropic API key when prompted (or skip for limited mode)
3. (Optional) Go to Settings to select your target company
4. Start building your stories!

## Project Structure

```
behavioral-interview-coach/
├── behavioral-interview-coach.html  # Main app (open this)
├── README.md                        # This file
├── LICENSE                          # MIT License
└── .gitignore                       # Git ignore patterns
```

## Usage

### Building a Story
1. Go to **Analyze** tab to evaluate your story's depth
2. Use **Story Web** to break your story into STAR components
3. Add detailed dimensions: Problem, Solution, Alternatives, Metrics, Tradeoffs
4. Tag each event with relevant competencies from your selected company

### Practice Mode
1. Select competencies in the **Practice** tab (story selection is optional)
2. Start a practice session with a 60-second target timer
3. Practice answering follow-up questions
4. Get AI feedback on your responses

### Timeline Visualization
1. Build your story in Story Web first
2. Go to **Timeline** tab
3. Select your story to see a Gantt chart showing the sequential flow of events

### Settings
- **API Key**: Manage your Anthropic API key
- **Company**: Select target company or define custom competencies
- **Data**: Export/import backups, clear all data

## Data Storage

All data is stored locally in your browser using localStorage:
- Stories and nodes
- Practice history
- Resume and job posting
- API key
- Company settings

Use the **Settings** tab to export/import data backups.

## Technical Details

- Single HTML file (~460KB)
- No server required
- Uses Claude API (claude-sonnet-4-5-20250929)
- Mermaid.js v10.9.1 for timeline charts (loaded from CDN)
- LocalStorage for persistence
- API calls include retry logic with exponential backoff for rate limits

## Privacy

- Your API key is stored only in your browser's localStorage
- No data is sent to any server except Anthropic's API for AI features
- All stories and practice data remain local

## Browser Support

Works in modern browsers (Chrome, Firefox, Safari, Edge). Requires JavaScript enabled and internet connection for CDN resources and API calls.

## License

[MIT License](LICENSE) - Feel free to modify and use for your interview prep.

---

*Version 3.1*
