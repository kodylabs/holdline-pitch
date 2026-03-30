# 🛡️ HoldLine — User Workflows

**ETHGlobal Cannes 2026 · Product Reference Document**

---

## Workflow A — First Launch (Persona A or C)

```
Step 1 · User arrives on HoldLine
    ↓
Step 2 · Connects via social login (Dynamic SDK — email or Google, zero wallet visible)
    ↓
Step 3 · World ID verification (optional to start, required for community)
    → "Verify you're human to join the conviction community"
    ↓
Step 4 · Welcome screen: "What's your conviction?"
    → Large text field
    → Placeholder: "Ex: I believe Ethereum will go up because..."
    ↓
Step 5 · User types their thesis in natural language
    → "I think ETH is undervalued. L2s are exploding, ETFs are coming,
       and staking reduces the supply."
    ↓
Step 6 · AI structures the thesis into hypotheses (progressive display, streaming)
    → "I've identified 3 hypotheses in your thesis:"
      H1: L2 activity on Ethereum is increasing [scoring in progress...]
      H2: Institutional flows via ETFs are growing [scoring in progress...]
      H3: ETH supply is decreasing (staking + burn) [scoring in progress...]
    ↓
Step 7 · AI asks for confirmation / adjustments
    → "Does this capture your conviction? Want to adjust anything?"
    → User can modify, add, reweight
    ↓
Step 8 · Initial scoring (CRE pipeline runs in background)
    → Scores appear one by one with explanation:
      H1: 87/100 🟢 "L2 activity is surging..."
      H2: 64/100 🟡 "ETF flows are steady but not accelerating..."
      H3: 79/100 🟢 "Staking rate is at an all-time high..."
    ↓
Step 9 · Overall score displayed: "Your ETH thesis holds at 78/100"
    ↓
Step 10 · Prompt to timestamp on-chain (ENS)
    → "Want to anchor this conviction? It will be timestamped and provable."
    → One click → hash stored in ENS text record
    ↓
Step 11 · Conviction dashboard displayed — user can return anytime
```

---

## Workflow B — Thesis Check-up (Persona B, recurring use)

```
Step 1 · User returns to HoldLine (notification or own initiative)
    ↓
Step 2 · Dashboard displayed: overall score + trend since last visit
    → "Your ETH thesis: 72/100 (was 78 fifteen days ago)"
    ↓
Step 3 · User clicks on a declining hypothesis
    → H2: Institutional flows — 64 → 51 in 15 days
    → AI explanation: "Two ETFs postponed their launch date.
       Trading volume for existing ETFs dropped 18%.
       No major negative flow though — it's a slowdown, not a withdrawal."
    ↓
Step 4 · AI offers options:
    → "Do you want to:"
    a) Keep this hypothesis as-is (you think it's temporary)
    b) Adjust the weight (it matters less to you now)
    c) Reformulate the hypothesis (narrow what you want to track)
    d) Chat with me to dig deeper (conversation mode)
    ↓
Step 5 · User chooses and dashboard updates
    ↓
Step 6 · If significant changes: prompt to re-timestamp
    → "Your thesis has evolved. Want to anchor the updated version?"
```

---

## Workflow C — Panic Mode (Market crash)

```
Step 1 · Market drops 15-20% in a few hours
    ↓
Step 2 · HoldLine sends a push alert:
    "📉 ETH dropped 18% in 24h. Your thesis holds at 74%.
     Open HoldLine before making a decision."
    ↓
Step 3 · User opens the app in panic
    ↓
Step 4 · Special "Conviction Mode" screen:

    ┌─────────────────────────────────────────────┐
    │  🛡️ BREATHE. HERE ARE THE FACTS.            │
    │                                             │
    │  Price dropped 18%.                         │
    │  Your thesis moved -4%.                     │
    │                                             │
    │  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░ 74/100              │
    │                                             │
    │  What CHANGED in your fundamentals:         │
    │  • H1 (L2s): 87 → 85 (slight)             │
    │  • H2 (Institutions): 51 → 48 (slight)    │
    │  • H3 (Supply): 79 → 79 (unchanged)       │
    │                                             │
    │  What HASN'T changed:                       │
    │  L2 activity is at an all-time high.        │
    │  Staking is stable.                         │
    │  No fundamental negative event.             │
    │                                             │
    │  💬 YOUR OWN THESIS, set on April 3rd:      │
    │  "L2s are exploding, institutions are       │
    │   coming in, staking makes ETH              │
    │   deflationary."                            │
    │                                             │
    │  The question: has something                │
    │  FUNDAMENTALLY changed?                     │
    │  Or did the price just move?                │
    │                                             │
    │  [Hold my position]  [Chat with AI]         │
    └─────────────────────────────────────────────┘

    ↓
Step 5 · If "Chat with AI":
    → Objective AI conversation:
    "OK, what specifically makes you doubt?
     Is it the price dropping or a new piece of information?"
    ↓
Step 6 · Decision recorded and timestamped
    → "You chose to hold your position despite an 18% drop. Conviction timestamped."
    → On-chain proof that they held during the crash
```

---

## Workflow D — Social Sharing & Community

```
Step 1 · User wants to share their conviction
    ↓
Step 2 · Clicks "Share my conviction"
    ↓
Step 3 · HoldLine generates a shareable visual card:

    ┌─────────────────────────────────┐
    │  🛡️ My ETH conviction          │
    │  Set on April 3, 2026          │
    │  Current score: 78/100         │
    │  Held during the April 15 crash│
    │  Verified on-chain via ENS     │
    │  holdline.eth/proof/0x8f2...   │
    └─────────────────────────────────┘

    ↓
Step 4 · Shareable on Twitter/Farcaster/Telegram
    → Verifiable link: anyone can confirm the date and score
    ↓
Step 5 · "Community" tab in the app:
    → "247 verified humans have a bullish ETH thesis"
    → "Most shared hypotheses: L2 adoption (89%), staking (76%)"
    → "Average community conviction: 74/100"
    → No names, no amounts — just aggregated sybil-resistant convictions
```

---

## MVP Hackathon Screens (5 screens)

| # | Screen | Content |
|---|---|---|
| 1 | Onboarding | Social login (Dynamic) + World ID verification |
| 2 | Thesis creation chat | Conversational interface + AI structuring |
| 3 | Conviction dashboard | Overall score + hypothesis breakdown + trends |
| 4 | Panic Mode | "I'm doubting" button → special price vs thesis screen |
| 5 | Community | Verified human count per thesis (requires World ID) |

---

*HoldLine · ETHGlobal Cannes 2026*