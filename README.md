# IronWill Enforcer

> **A strict disciplinary protocol for your browser — block distractions, earn XP, build discipline.**

IronWill is a Chrome/Edge Manifest V3 browser extension paired with a React + Vite analytics dashboard. It enforces intentional browsing by gating access to distraction sites (Instagram, TikTok, YouTube Shorts, etc.) behind an XP economy — you earn points by staying disciplined and spend them to unlock sites with a hard timer.

---
# The IronWill Extension is currently available upon request only

## Project Structure

```
iron_will/
├── extension/          # Chrome/Edge MV3 browser extension
│   ├── manifest.json
│   ├── background.js   # Service worker — XP engine, timers, debt logic
│   ├── content.js      # Overlay injection & timer countdown
│   ├── popup.html      # Extension popup UI (SwiftDelux design)
│   ├── popup.js        # Popup controller
│   ├── overlay.css     # Injected overlay styles
│   ├── coach.js        # AI-style coaching message system
│   ├── audio-synth.js  # Web Audio API sound feedback
│   ├── achievements.js # Achievement definitions & unlock logic
│   └── site-rules.js   # Per-site blocking rules & config
│
└── dashboard/          # React + Vite analytics dashboard
    ├── src/
    │   ├── App.jsx           # Root app with tab navigation
    │   ├── components/
    │   │   ├── Insights.jsx      # Session history & charts
    │   │   ├── IronBank.jsx      # XP bank, loans, subscriptions
    │   │   ├── Achievements.jsx  # Achievement showcase
    │   │   └── Settings.jsx      # Site rules & configuration
    │   └── index.css         # SwiftDelux design tokens
    └── package.json
```

---

## Core Features

### Extension

| Feature | Description |
|---|---|
| **Site Blocking Overlay** | Full-screen overlay injected on blocked sites at `document_start` |
| **XP Economy** | Earn XP by retreating; spend XP to unlock sites for 5–15 min |
| **Streak System** | Daily streak counter with multiplier bonus (10% XP/day) |
| **Surge Pricing** | 3+ unlocks in 1 hour triggers 1.5× XP cost |
| **Willpower Meter** | Drops on failed self-control; blocks unlocks below 30% |
| **Focus Sessions** | Hard lock for N minutes; earns 2 XP/min on completion |
| **XP Loans** | Borrow XP at 20% interest (7-day repayment window) |
| **Premium Pass** | 1000 XP buys 24h unlimited access (bypasses soft blocks only) |
| **Site-Specific Rules** | YouTube Shorts = hard block; TikTok = 10 min max; Instagram = 15 min max |
| **Achievements** | 6 unlockable badges (First Step, Weekend Warrior, Focus Master, etc.) |
| **Coaching Messages** | Context-aware motivational messages during overlay |
| **Audio Feedback** | Web Audio API synthesized sounds for key events |
| **Cloud Sync** | XP, level, streak, and debt synced via `chrome.storage.sync` |

### Dashboard

| Tab | Description |
|---|---|
| **Insights** | Session timeline, usage stats, and behavioral charts |
| **IronBank** | XP balance, outstanding debt, loan history, Premium Pass status |
| **Achievements** | Visual showcase of locked and unlocked badges |
| **Settings** | Custom site rules, block lists, and enforcement configuration |

---

## Design System

IronWill uses the **SwiftDelux Design Language** — a high-density fintech aesthetic inspired by Revolut and Apple Card.

| Token | Value |
|---|---|
| Background | `zinc-950` (`#09090b`) |
| Surface | `zinc-900` (`#18181b`) |
| Border | `zinc-800` (`#27272a`) |
| Primary accent | `teal-500` (`#14b8a6`) |
| Danger | `rose-500` (`#f43f5e`) |
| Warning | `amber-500` (`#f59e0b`) |
| Typography | `Inter` (Google Fonts) |
| Border radius | `6px` (cards), `4px` (inputs) |
| Transitions | `120ms–300ms ease-out` |

---

## Tech Stack

### Extension
- **Manifest Version:** 3
- **Service Worker:** `background.js` (ES module)
- **Content Scripts:** `content.js`, `site-rules.js`, `audio-synth.js`
- **Storage:** `chrome.storage.sync` (XP, level, streak, debt) + `chrome.storage.local` (history, focus sessions)
- **APIs used:** `webNavigation`, `notifications`, `alarms`, `tabs`, `storage`

### Dashboard
- **Framework:** React 19 + Vite 7
- **Styling:** Tailwind CSS v3
- **Build:** Vite (ESM, `@vitejs/plugin-react`)
- **Linting:** ESLint 9

---

## Installation

### Extension (Chrome / Edge)

1. Open `chrome://extensions` or `edge://extensions`
2. Enable **Developer Mode** (toggle, top-right)
3. Click **Load unpacked**
4. Select the `extension/` folder
5. Pin the extension to your toolbar

### Dashboard

```bash
cd dashboard
npm install
npm run dev
```

Open `http://localhost:5173` in your browser.

---

## How the XP Economy Works

```
User visits blocked site
        │
        ▼
 Overlay injected
        │
   ┌────┴────┐
   │         │
Retreat    Unlock (costs XP)
(+10 XP)        │
            ┌───┴───┐
            │       │
         Normal   Surge Pricing
         cost     (3+ unlocks/hr → 1.5×)
```

**XP Sources:**
- Retreat from a blocked site → **+10 XP**
- Complete a Focus Session → **+2 XP per minute**
- Daily streak maintained → **+10% XP multiplier per consecutive day**

**XP Sinks:**
- Unlock a blocked site → **−(5–25 XP)** depending on site & duration
- Take an XP Loan → **20% interest**, 7-day window
- Buy Premium Pass → **−1000 XP** for 24h unlimited access

---

## Storage Architecture

| Key | Storage | Description |
|---|---|---|
| `xp` | sync | Current XP balance |
| `level` | sync | Current level (`floor(xp / 100) + 1`) |
| `willpower` | sync | Willpower % (0–100) |
| `streak` | sync | Consecutive active days |
| `debt` | sync | `{ amount, originalAmount, dueDate }` |
| `subscription` | sync | `{ active, endTime, purchaseTime }` |
| `battlesWon` | sync | Total successful retreats |
| `battlesLost` | sync | Total willpower-draining events |
| `unlockHistory` | local | Last 24h of unlock events |
| `focusSessionActive` | local | Boolean focus lock state |
| `focusSessionEndTime` | local | Unix timestamp of session end |
| `moodHistory` | local | Mood tracking entries |
| `lastActiveDate` | local | Date string for streak logic |

---

## Achievements

| ID | Title | Condition |
|---|---|---|
| `first_step` | First Step | Earn 100 XP |
| `weekend_warrior` | Weekend Warrior | 7-day streak |
| `focus_master` | Focus Master | Complete 5 focus sessions |
| `iron_will` | Iron Will | Reach Level 10 |
| `early_bird` | Early Bird | Unlock a site before 8 AM |
| `night_owl` | Night Owl | Unlock a site after 10 PM |

---

## Development Notes

- **Data Migration:** On install/startup, `ensureDataMigration()` checks for local→sync gaps and backfills missing fields automatically.
- **Hard Blocks:** Sites like YouTube Shorts cannot be unlocked by any means — not even the Premium Pass. The overlay shows no timer options.
- **Willpower Gate:** Users below 30% willpower cannot purchase unlocks. Below 50% blocks Premium Pass purchase.
- **Debt Auto-Pay:** When XP is earned, outstanding debt is paid first before adding to balance.

---

## preview 

-See [Extension](./extension.png)
-See [Extension_2](./extension.png)
-See [Dashbord](./dashbord.png)
-See [Dashbord_2](./dashbord(2).png)
-See [Setting](./setting.png)

---

## License

Private project. All rights reserved.
