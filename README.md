# Behavioral Interview Coach

A comprehensive, AI-powered interview preparation tool for behavioral interviews. Built as a single HTML file with no backend required.

## Features

### Core Capabilities
- **Story Analyzer**: Evaluate if your story has enough depth for 40+ minutes of follow-up questions
- **Story Web Builder**: Create visual story webs with STAR components (Situation, Task, Action, Result)
- **Timeline View**: Visualize your stories as interactive Mermaid Gantt charts
- **Practice Mode**: Timed practice sessions with AI-generated follow-up questions
- **Q&A Review**: Analyze your question-answer pairs for strengths and improvements
- **Competency Map**: Track coverage of competencies across your stories

### AI-Powered Features
- Story depth analysis and scoring
- Dynamic follow-up question generation
- Bar Raiser-style probing questions
- Suggested response improvements

### Bring Your Own Key (BYOK)
This app uses the Claude API for AI features. You'll need your own Anthropic API key:
1. Get a key from [Anthropic Console](https://console.anthropic.com/settings/keys)
2. Enter it when prompted on first load, or go to Settings
3. Your key is stored locally in your browser - never sent anywhere except Anthropic's API

### Company-Agnostic
Supports multiple company competency frameworks:
- Amazon Leadership Principles
- Google Googleyness
- Meta Core Values
- Microsoft Growth Mindset
- Custom competencies for any company

## Quick Start

1. Open `behavioral-interview-coach.html` in any modern browser
2. Enter your Anthropic API key when prompted
3. (Optional) Go to Settings to select your target company
4. Start building your stories!

## Project Structure

```
behavioral-interview-coach/
├── behavioral-interview-coach.html  # Main unified app (open this)
├── README.md                        # This file
├── .gitignore                       # Git ignore patterns
└── v2/                              # Reference materials
    └── timelines/                   # Timeline viewer reference
```

## Usage

### Building a Story
1. Go to **Analyze** tab to evaluate your story's depth
2. Use **Story Web** to break your story into STAR components
3. Add detailed dimensions: Problem, Solution, Alternatives, Metrics, Tradeoffs
4. Tag each event with relevant competencies

### Practice Mode
1. Select a story and competency in **Practice** tab
2. Start a timed session
3. Practice answering follow-up questions
4. Get AI feedback on your responses

### Timeline Visualization
1. Build your story in Story Web first
2. Go to **Timeline** tab
3. Select your story to see a Gantt chart visualization

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

- Single HTML file (~400KB)
- No server required
- Uses Claude claude-sonnet-4-5-20250929 model via Anthropic API
- Mermaid.js for timeline charts
- LocalStorage for persistence

## Privacy

- Your API key is stored only in your browser's localStorage
- No data is sent to any server except Anthropic's API for AI features
- All stories and practice data remain local

## Browser Support

Works best in modern browsers:
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## License

MIT License - Feel free to modify and use for your interview prep.

---

*Version 3.0*
