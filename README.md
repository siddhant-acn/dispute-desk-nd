# Dispute Desk

A prototype AI agent for US demurrage and detention (D&D) disputes, built for a Forward Deployed Engineer assessment.
All data is synthetic. Not affiliated with or endorsed by Maersk.

**The idea in one line:** AI reads messy emails and writes replies. Code does the dates, money and rules. A person approves every message.

---

## Run it on your computer

You need **Node.js 20 or newer**. Check with `node --version`. Get it from https://nodejs.org if needed.

```bash
# 1. Open this folder in VS Code, then open the terminal (View > Terminal)

# 2. Install the packages (first time only)
npm install

# 3. Create your settings file
#    Mac/Linux:
cp .env.example .env
#    Windows (PowerShell):
copy .env.example .env

# 4. Open .env and paste your Claude API key after ANTHROPIC_API_KEY=
#    (Get one at https://console.anthropic.com. Without a key the app still works in offline mode.)

# 5. Start it
npm run dev
```

Open **http://localhost:5173** in your browser.

`npm run dev` starts two things at once:
- the **screens** (Vite) on port 5173
- the **server** on port 8787, which holds your API key and talks to Claude

## Other commands

| Command | What it does |
|---|---|
| `npm test` | Runs 10 automated checks on the rules engine and guardrail |
| `npm run build` | Builds the screens into the `dist` folder |
| `npm start` | Runs the server, which also serves the built screens (production mode, port 8787) |

## Put it online (so others can open a link)

The easiest option is **Render** (render.com), which has a free plan.

1. Put this folder in a GitHub repository. The `.env` file is ignored on purpose, so your key never goes to GitHub.
2. On Render, choose **New > Blueprint** and pick your repository. It reads `render.yaml` automatically.
3. When asked, paste your `ANTHROPIC_API_KEY`.
4. Wait for the build. Render gives you a link like `https://dispute-desk.onrender.com`.

Any host that runs Node works the same way: build command `npm install && npm run build`, start command `npm start`, and set `ANTHROPIC_API_KEY` as an environment variable.

Free Render services sleep when unused, so the first visit can take about a minute. Open the link once before your interview.

**Protecting your key:** the server limits each visitor to 40 AI calls per 10 minutes (change `RATE_LIMIT_PER_10_MIN` in `.env`). Set a monthly spending limit in the Anthropic console too.

## How the code is organised

```
server/index.js          The server: hides the API key, calls Claude, rate-limits, serves the app
src/
  main.jsx               Starts the app
  App.jsx                Shared state: selected case, agent runs, audit log
  styles.css             All styling. Green = code, purple = AI, amber = the 30-day clock
  config/rules.js        The FMC rules as settings (two versions)
  data/mockSystems.js    Synthetic data, split by system (inbox, billing, terminal...)
  adapters/index.js      One plug per system. Swap a mock for a real API here
  agent/caseFile.js      Calls the adapters and joins the data into one case file
  agent/pipeline.js      The agent's 6 steps
  engine/evaluate.js     The rules engine: day counts, deadlines, recommendation (no AI)
  engine/guardrails.js   Checks the AI's draft before it can be sent
  engine/engine.test.js  Automated tests
  ai/client.js           Calls our server (never Claude directly)
  ai/prompts.js          The two instructions given to Claude
  ai/templates.js        Backup text when the AI is off
  components/ui.jsx      The clock, AI/Code labels, clickable evidence references
  views/                 One file per tab
```

## How a request flows

```
Browser (screens)  ->  our server /api/claude  ->  Claude Messages API
                       (adds the secret key,
                        checks size, rate-limits)
```

## Change the AI model

Edit `CLAUDE_MODEL` in `.env` and restart. No code change needed.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Live (Claude), not set up" in the AI menu | The server can't see your key. Check `.env`, then stop (`Ctrl+C`) and run `npm run dev` again |
| Port already in use | Close the other program, or change `PORT` in `.env` and the proxy port in `vite.config.js` |
| AI step says "AI unavailable" | Check the terminal for the error. Common causes: wrong key, no credit, wrong model name. The app keeps working with templates |
| `node --watch` not recognised | Update Node to version 20 or newer |
