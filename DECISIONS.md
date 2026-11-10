# Build log: decisions and how I directed the AI tools

Fill this in in your own words. Interviewers asked to see how you direct AI tools.

## The problem I chose and why
-

## Decisions I made
| Decision | Why | What I rejected |
|---|---|---|
| AI only reads emails and drafts replies; code does dates, money and rules | Models can make arithmetic and date mistakes; the money must always be right | Letting the model decide the outcome |
| Rules stored as config with versions | The FMC rule changed in Sept 2025 and may change again | Writing the rules into the prompt |
| Code checks every AI draft before Send | Stops invented facts and dollar amounts reaching a customer | Trusting the draft |
| Agent escalates when our systems disagree | A wrong guess costs more than a short delay | Picking the carrier's date by default |
| Server holds the API key | A key in the browser can be copied by anyone | Calling Claude from the browser |
| Offline mode and fallbacks | The demo must not break if the network or AI fails | |

## How I used AI tools
- What I asked for:
- What I changed or pushed back on:
- What I checked myself:

## What I'd do next
- Connect the inbox and billing system read-only
- Build an eval set from 200 past disputes and measure agreement
- Run the same checks before invoices are sent (the revenue-protection phase)
