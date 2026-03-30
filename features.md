# 🛡️ HoldLine — Detailed Features

**ETHGlobal Cannes 2026 · Product Reference Document**

---

## Feature 1 — Conversational Thesis Creation

**The user types their conviction in natural language. The AI structures it into verifiable hypotheses.**

- The user writes as they'd talk to a friend: "I believe Ethereum will go up because institutions are coming in, L2s are exploding, and staking makes ETH deflationary"
- The AI identifies underlying hypotheses and reformulates them into testable propositions:
  - H1: "Institutional adoption of Ethereum is increasing" → measurable via ETF flows, corporate announcements, on-chain institutional deposits
  - H2: "Activity on Ethereum L2s is growing" → measurable via L2 TVL, transaction count, active users
  - H3: "ETH supply is decreasing through staking + burn" → measurable via staking rate, net supply, burn rate post-EIP-1559
- The user can modify, add, remove, or weight hypotheses
- The AI asks clarifying questions if the thesis is vague: "When you say 'institutions are coming in', do you mean spot ETFs, companies holding ETH in treasury, or banks offering staking?"

**Hackathon:** Core of the MVP. Chat + AI parsing + structuring into hypotheses.

---

## Feature 2 — Objective Hypothesis Scoring

**Each hypothesis gets a 0-100 score based on real data, not opinions.**

- Chainlink CRE pipeline orchestrates multi-source data collection: news APIs, on-chain data, market data
- The AI aggregates signals and produces a score per hypothesis with a natural language explanation: "Institutional adoption scores 73/100. ETF flows are steady (+2% this month), but no major corporate announcement in 6 weeks. Neutral-positive signal."
- Overall thesis score = weighted average of hypotheses (user chooses the weights)

**Hackathon:** Simplified scoring (2-3 data sources per hypothesis). What matters: the score moves and the explanation is clear.

---

## Feature 3 — Conviction Dashboard

**Visual overview of your thesis status at any time.**

- Overall score with visual gauge ("Your ETH thesis holds at 78%")
- Per-hypothesis breakdown with color coding: 🟢 (70-100) solid / 🟡 (40-69) under pressure / 🔴 (0-39) invalidated
- Trend over time: "30 days ago, your thesis was at 85%. It dropped mainly because of H3 (ETH supply)"
- Asset price overlay → shows the disconnect between price and fundamentals ("price dropped 20% but your thesis only moved -7%")

**Hackathon:** Simplified but visually impactful dashboard. Gauge + 3 colored hypotheses + AI message.

---

## Feature 4 — "Conviction Check" Alerts

**Proactive notifications when something changes in your thesis — in either direction.**

- **Degradation alert:** "⚠️ Your 'L2 adoption' hypothesis dropped from 85 to 62 in 48h. Here's why."
- **Reinforcement alert:** "🟢 Your 'institutional flows' hypothesis just climbed to 91. A new ETF was just approved."
- **Market vs thesis alert:** "📉 ETH dropped 15% this week. But your thesis holds at 76% — the fundamentals haven't changed. Breathe."
- **Thesis drift alert:** "🔄 It's been 45 days since you last reviewed your thesis. The market has moved. Want a check-up?"

**Hackathon:** Show a simulated alert during the demo (market crash → alert → thesis holds). This is the "wow" moment.

---

## Feature 5 — On-chain Conviction Timestamp (ENS)

**Your thesis is hashed and timestamped on-chain via ENS text records. Immutable proof.**

- When you finalize your thesis, HoldLine generates a SHA-256 hash of the content
- This hash is written to an ENS text record (custom key `com.holdline.thesis`)
- "On April 3, 2026, [user] set a bullish ETH thesis with an initial score of 82/100" → verifiable by anyone
- The user can share a link: on-chain proof of conviction

**Hackathon:** Hash written to ENS text record + displayed in the app. Functional, no hard-coded values.

---

## Feature 6 — Conviction Community (World ID)

**See how many verified humans share a thesis similar to yours.**

- Each user verified via World ID (1 human = 1 vote) can make their thesis public
- HoldLine aggregates: "247 verified humans share a bullish ETH thesis based on L2 adoption"
- No bots, no fake accounts — World ID guarantees uniqueness
- Community indicators: "The community is 73% bullish on ETH this month. It was 89% 30 days ago."
- First ever "human-verified sentiment index" — a data point that exists nowhere else

**Hackathon:** Working World ID integration + display of verified human count per thesis.

---

## Feature 7 — Panic Mode (Emotional killer feature)

**Emergency button for when you're about to panic sell.**

- The user taps "🛡️ I'm doubting"
- HoldLine immediately displays:
  1. Current score: "Your thesis holds at 76%. Fundamentals haven't changed."
  2. Reminder of THEIR OWN thesis, in THEIR OWN original words
  3. Historical perspective: "Last time ETH dropped 20%, the price recovered in 6 weeks"
  4. The key question: "Has something FUNDAMENTALLY changed in your thesis? Or did the price just move?"
- Option to chat with the AI to dig deeper
- Decision is timestamped: "You held during the April 15 crash" → on-chain proof of conviction

**Hackathon:** Panic mode interface + contextual AI message. This is the demo climax.

---

## Summary: The 3 Moments of Truth

1. **"Oh, this is MY thesis, structured"** — the shift from "I think that..." to scored, testable hypotheses
2. **"Price dropped but my thesis holds"** — the price/fundamentals disconnect, made visible
3. **"I held, and I can prove it"** — on-chain proof of conviction + viral shareable content

---

*HoldLine · ETHGlobal Cannes 2026*