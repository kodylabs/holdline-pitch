# 🛡️ HoldLine — Technical Architecture

**ETHGlobal Cannes 2026 · Technical Reference Document**

---

## 1. System Architecture — High-Level Overview

```mermaid
graph TB
    subgraph CLIENT["🖥️ Frontend — Next.js + React"]
        UI[Chat UI + Dashboard]
        PANIC[Panic Mode]
        COMMUNITY[Community View]
    end

    subgraph AUTH["🔐 Authentication"]
        DYNAMIC["Dynamic JS SDK<br/>Social Login + Wallet<br/>(Bounty: $1.67K)"]
        WORLDID["World ID 4.0 — IDKit<br/>Proof of Human<br/>(Bounty: $8K)"]
    end

    subgraph BACKEND["⚙️ Backend — Next.js API Routes"]
        API[API Routes]
        LLM["Claude API<br/>Thesis parsing → hypotheses<br/>Scoring → explanation"]
        RP_SIG["RP Signature Service<br/>World ID verification"]
        VERIFY["Proof Verification<br/>POST /v4/verify/{rp_id}"]
    end

    subgraph CRE["⛓️ Chainlink CRE — Data Pipeline (Bounty: $4K)"]
        TRIGGER["Cron Trigger<br/>Every 6 hours"]
        HTTP_CLIENT["HTTP Client<br/>News APIs + Market Data"]
        EVM_READ["EVM Read Client<br/>On-chain data"]
        SCORING["Scoring Logic<br/>Multi-source aggregation"]
        EVM_WRITE["EVM Write Client<br/>Score → on-chain"]
    end

    subgraph STORAGE["💾 Storage"]
        ZeroG["0G Storage<br/>Theses + history<br/>(Bounty: $3K)"]
        ZeroG_COMPUTE["0G Compute<br/>AI inference"]
        ENS["ENS Text Records<br/>Thesis hash timestamped<br/>(Bounty: $5K)"]
    end

    subgraph ONCHAIN["⛓️ On-chain"]
        WORLD_CHAIN["World Chain<br/>World ID proofs"]
        ENS_RESOLVER["ENS Public Resolver<br/>Mainnet/L2"]
    end

    UI --> DYNAMIC
    UI --> API
    DYNAMIC --> WORLDID
    WORLDID --> RP_SIG
    RP_SIG --> VERIFY
    VERIFY --> WORLD_CHAIN
    API --> LLM
    API --> ZeroG
    API --> ZeroG_COMPUTE
    CRE --> API
    TRIGGER --> HTTP_CLIENT
    TRIGGER --> EVM_READ
    HTTP_CLIENT --> SCORING
    EVM_READ --> SCORING
    SCORING --> EVM_WRITE
    API --> ENS_RESOLVER
    COMMUNITY --> WORLDID

    style CLIENT fill:#e0f2fe,stroke:#0284c7
    style AUTH fill:#fce7f3,stroke:#be185d
    style BACKEND fill:#f0fdf4,stroke:#16a34a
    style CRE fill:#fef3c7,stroke:#d97706
    style STORAGE fill:#ede9fe,stroke:#7c3aed
    style ONCHAIN fill:#fef2f2,stroke:#dc2626
```

---

## 2. Authentication Flow — World ID + Dynamic

Based on World ID 4.0 docs: IDKit 4.x with RP signature generated server-side, verification via `/v4/verify/{rp_id}` API.

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant F as 🖥️ Frontend<br/>(Next.js)
    participant D as 🔑 Dynamic SDK<br/>(Social Login)
    participant B as ⚙️ Backend<br/>(API Routes)
    participant W as 🌍 World ID<br/>(developer.world.org)
    participant WC as ⛓️ World Chain

    Note over U,WC: Phase 1 — Initial Connection (Dynamic)
    U->>F: Opens HoldLine
    F->>D: Initialize Dynamic JS SDK
    D->>U: Shows "Login with Google/Email"
    U->>D: Connects (social login)
    D->>F: Session + embedded wallet created
    Note right of D: User NEVER sees<br/>a wallet or seed phrase

    Note over U,WC: Phase 2 — World ID Verification (for community)
    F->>B: POST /api/rp-signature {action: "verify-human"}
    B->>B: signRequest(action, RP_SIGNING_KEY)<br/>→ {sig, nonce, created_at, expires_at}
    B->>F: Returns RP signature
    F->>F: IDKit.request({<br/>  app_id, action,<br/>  rp_context: {rp_id, nonce, sig...}<br/>}).preset(orbLegacy())
    F->>U: Shows QR code (World App)<br/>or mobile deeplink
    U->>W: Scans with World App<br/>→ generates ZK proof
    W->>F: Returns proof payload<br/>{protocol_version, responses: [{proof, nullifier, merkle_root}]}
    F->>B: POST /api/verify-proof {rp_id, idkitResponse}
    B->>W: POST /v4/verify/{rp_id}<br/>Forward payload as-is
    W->>WC: Verifies on-chain (merkle root + nullifier)
    WC->>W: ✅ Valid proof, unique human
    W->>B: 200 OK — verified
    B->>F: ✅ User verified
    F->>U: "Welcome! You're verified as human."
```

**Key World ID technical notes (from docs):**
- Package: `@worldcoin/idkit-core` (version 4.x)
- RP signature MUST be generated server-side (never client-side)
- RP signing key must NEVER be exposed
- Verification via `POST https://developer.world.org/api/v4/verify/{rp_id}`
- Nullifier hash ensures uniqueness (1 human = 1 vote per action)
- Use `environment: "staging"` for testing with the simulator
- Bounty requirement: proof validation must occur in web backend or smart contract

---

## 3. Scoring Pipeline — Chainlink CRE

Based on CRE docs: trigger-and-callback model, TypeScript SDK, workflows compiled to WASM.

```mermaid
sequenceDiagram
    participant CRON as ⏰ Cron Trigger<br/>(every 6h)
    participant WF as 📋 CRE Workflow<br/>(TypeScript WASM)
    participant NEWS as 📰 News APIs<br/>(HTTP Client)
    participant CHAIN as ⛓️ On-chain Data<br/>(EVM Read Client)
    participant MARKET as 📊 Market APIs<br/>(HTTP Client)
    participant CONSENSUS as 🔒 DON Consensus<br/>(BFT)
    participant CONTRACT as 📝 Score Contract<br/>(EVM Write Client)
    participant BACKEND as ⚙️ HoldLine Backend

    CRON->>WF: Trigger fires → runs callback
    
    Note over WF,MARKET: Parallel data collection (Promises)
    par News data
        WF->>NEWS: GET /api/news?q=ethereum+L2<br/>(HTTP Client capability)
        NEWS->>WF: {articles, sentiment, frequency}
    and On-chain data
        WF->>CHAIN: read(L2_TVL_CONTRACT)<br/>(EVM Read capability)
        CHAIN->>WF: {tvl, tx_count, active_users}
    and Market data
        WF->>MARKET: GET /api/market/eth<br/>(HTTP Client capability)
        MARKET->>WF: {price, volume, staking_rate}
    end

    Note over WF,CONSENSUS: Automatic CRE consensus
    WF->>CONSENSUS: Each DON node independently<br/>executes the scoring
    CONSENSUS->>CONSENSUS: BFT aggregation<br/>→ single verified result
    CONSENSUS->>WF: Consensus scores:<br/>H1: 87, H2: 64, H3: 79

    WF->>CONTRACT: writeOnChain({<br/>  thesis_id, scores, timestamp<br/>})<br/>(EVM Write capability)
    CONTRACT->>BACKEND: Event emitted: ScoreUpdated
    BACKEND->>BACKEND: Updates dashboard<br/>+ triggers alerts if needed
```

**CRE Workflow internal architecture:**

```mermaid
graph LR
    subgraph WORKFLOW["CRE Workflow — holdline-scoring.ts"]
        T["handler(<br/>  cron.Trigger({schedule: '0 */6 * * *'}),<br/>  onScoreUpdate<br/>)"]
        
        CB["Callback: onScoreUpdate()"]
        
        subgraph CAPABILITIES["Capabilities used"]
            HTTP["httpClient.get()<br/>News + Market APIs"]
            EVM_R["evmClient.read()<br/>TVL, staking rate"]
            EVM_W["evmClient.write()<br/>Scores on-chain"]
        end
    end

    T --> CB
    CB --> HTTP
    CB --> EVM_R
    HTTP --> SCORE["Scoring Logic<br/>Aggregates signals<br/>→ score 0-100"]
    EVM_R --> SCORE
    SCORE --> EVM_W

    style WORKFLOW fill:#fef3c7,stroke:#d97706
    style CAPABILITIES fill:#fff7ed,stroke:#ea580c
```

**Key CRE technical notes (from docs):**
- TypeScript SDK available (also Go)
- Trigger-and-callback model: `handler(trigger, callback)`
- Each trigger fire = independent, stateless execution
- Capabilities (HTTP, EVM Read/Write) return Promises → parallelizable
- Automatic BFT consensus on every operation
- Local simulation with `cre simulate` (makes real API calls)
- If simulation succeeds → Chainlink team can deploy to live DON during hackathon
- Bounty requirement: at least one blockchain integrated with an external API/data source + successful simulation

---

## 4. Thesis Storage — ENS Text Records

Based on ENS docs: text records = key-value pairs on the resolver, custom records supported.

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant F as 🖥️ Frontend
    participant B as ⚙️ Backend
    participant HASH as 🔐 SHA-256
    participant ENS_R as 📝 ENS Resolver<br/>(Public Resolver)
    participant CHAIN as ⛓️ Blockchain

    Note over U,CHAIN: Thesis timestamping

    U->>F: Clicks "Anchor my conviction"
    F->>B: POST /api/timestamp-thesis<br/>{thesis_content, hypotheses, scores}

    B->>HASH: SHA-256(JSON.stringify({<br/>  content, hypotheses,<br/>  scores, timestamp<br/>}))
    HASH->>B: hash = "0x8f2a3b..."

    B->>ENS_R: setText(<br/>  namehash("user.holdline.eth"),<br/>  "com.holdline.thesis",<br/>  hash<br/>)
    ENS_R->>CHAIN: Transaction: set text record
    CHAIN->>ENS_R: ✅ Confirmed

    B->>ENS_R: setText(<br/>  namehash("user.holdline.eth"),<br/>  "com.holdline.score",<br/>  "78"<br/>)

    B->>B: Stores hash → content mapping<br/>in 0G Storage

    B->>F: ✅ Conviction anchored
    F->>U: "Your thesis is timestamped on-chain.<br/>Proof link: holdline.eth/proof/0x8f2..."

    Note over U,CHAIN: Third-party verification
    
    participant V as 👀 Verifier

    V->>ENS_R: getText("user.holdline.eth",<br/>"com.holdline.thesis")
    ENS_R->>V: "0x8f2a3b..."
    V->>B: GET /api/proof/0x8f2a3b
    B->>V: {content, scores, timestamp}<br/>+ re-hash to verify
```

**Key ENS technical notes (from docs):**
- Text records = arbitrary key-value pairs on the resolver
- Custom key convention: `com.holdline.thesis`, `com.holdline.score`
- Read via wagmi `useEnsText` or viem `getEnsText`
- Write via `setText` on the resolver contract
- Subnames possible: `user123.holdline.eth` (via Name Wrapper)
- ENS supports custom records without format restrictions
- Bounty requirement: demo must be functional with no hard-coded values, present at ENS booth Sunday morning

---

## 5. Full Architecture — Layer View

```mermaid
graph TB
    subgraph PRESENTATION["Presentation Layer"]
        direction LR
        CHAT["💬 AI Chat<br/>Thesis creation"]
        DASH["📊 Dashboard<br/>Scores + trends"]
        PANIC_UI["🛡️ Panic Mode<br/>Conviction reminder"]
        SHARE["📤 Sharing<br/>Visual card"]
        COMM["👥 Community<br/>Aggregated convictions"]
    end

    subgraph APPLICATION["Application Layer — Next.js API"]
        direction LR
        THESIS_SVC["Thesis Service<br/>Parse + structure"]
        SCORE_SVC["Scoring Service<br/>Aggregates CRE data"]
        ALERT_SVC["Alert Service<br/>Detects changes"]
        AUTH_SVC["Auth Service<br/>World ID + Dynamic"]
        ENS_SVC["ENS Service<br/>Timestamp + proof"]
    end

    subgraph SPONSORS["Sponsor Layer (Bounties)"]
        direction LR
        
        subgraph WORLD_STACK["🌍 World — $8K"]
            IDKIT["IDKit 4.x<br/>@worldcoin/idkit-core"]
            VERIFY_API["Verify API<br/>/v4/verify/{rp_id}"]
        end

        subgraph CRE_STACK["⛓️ Chainlink CRE — $4K"]
            CRE_SDK["CRE SDK TypeScript"]
            CRE_CLI["CRE CLI<br/>simulate + deploy"]
            DON["Workflow DON"]
        end

        subgraph ENS_STACK["📛 ENS — $5K"]
            RESOLVER["Public Resolver"]
            TEXT_REC["Text Records<br/>com.holdline.*"]
            SUBNAMES["Subnames<br/>*.holdline.eth"]
        end

        subgraph ZG_STACK["💾 0G — $3K"]
            ZG_STORE["0G Storage<br/>Theses"]
            ZG_COMP["0G Compute<br/>AI inference"]
        end

        subgraph DYN_STACK["🔑 Dynamic — $1.67K"]
            DYN_SDK["Dynamic JS SDK<br/>Social Login"]
        end
    end

    subgraph EXTERNAL["External Services"]
        direction LR
        CLAUDE["Claude API<br/>LLM"]
        NEWS_API["News APIs"]
        MARKET_API["Market Data APIs"]
        DEFI_API["DeFi Data APIs<br/>(DefiLlama, etc.)"]
    end

    PRESENTATION --> APPLICATION
    APPLICATION --> SPONSORS
    APPLICATION --> EXTERNAL
    
    THESIS_SVC --> CLAUDE
    SCORE_SVC --> CRE_SDK
    AUTH_SVC --> IDKIT
    ENS_SVC --> RESOLVER

    style PRESENTATION fill:#e0f2fe,stroke:#0284c7
    style APPLICATION fill:#f0fdf4,stroke:#16a34a
    style SPONSORS fill:#fef3c7,stroke:#d97706
    style EXTERNAL fill:#f1f5f9,stroke:#64748b
```

---

## 6. Complete Data Flow — From Thesis to Score

```mermaid
flowchart LR
    subgraph INPUT["User Input"]
        USER_TEXT["'I believe in ETH<br/>because L2s are<br/>exploding and<br/>institutions are<br/>coming in'"]
    end

    subgraph PARSE["AI Parsing (Claude)"]
        LLM_PARSE["System prompt:<br/>Extract verifiable<br/>hypotheses"]
        JSON_OUT["Structured JSON:<br/>{hypotheses: [<br/>  {id: 'h1', claim: 'L2 growth',<br/>   metrics: ['tvl', 'tx_count']},<br/>  {id: 'h2', claim: 'institutional',<br/>   metrics: ['etf_flows', 'corp_holdings']}<br/>]}"]
    end

    subgraph CRE_PIPE["CRE Pipeline"]
        FETCH["HTTP Client:<br/>• News API → sentiment<br/>• DefiLlama → L2 TVL<br/>• Market API → ETF flows"]
        READ["EVM Read:<br/>• Staking contract → rate<br/>• Burn tracker → supply"]
        AGG["Aggregation:<br/>Score per hypothesis<br/>0-100"]
    end

    subgraph OUTPUT["Results"]
        SCORES["H1: 87/100 🟢<br/>H2: 64/100 🟡<br/>H3: 79/100 🟢<br/><br/>Overall: 78/100"]
        EXPLAIN["AI Explanations:<br/>'L2 activity is surging<br/>(+23% TVL this month)...'"]
    end

    subgraph STORE["Persistence"]
        ZG["0G Storage<br/>(thesis + scores)"]
        ENS_TR["ENS Text Records<br/>(hash + timestamp)"]
    end

    USER_TEXT --> LLM_PARSE
    LLM_PARSE --> JSON_OUT
    JSON_OUT --> FETCH
    JSON_OUT --> READ
    FETCH --> AGG
    READ --> AGG
    AGG --> SCORES
    SCORES --> EXPLAIN
    SCORES --> ZG
    SCORES --> ENS_TR

    style INPUT fill:#e0f2fe,stroke:#0284c7
    style PARSE fill:#f0fdf4,stroke:#16a34a
    style CRE_PIPE fill:#fef3c7,stroke:#d97706
    style OUTPUT fill:#ede9fe,stroke:#7c3aed
    style STORE fill:#fef2f2,stroke:#dc2626
```

---

## 7. Bounties → Technical Components Mapping

```mermaid
graph TB
    subgraph BOUNTIES["💰 Targeted Bounties — $21.67K max"]
        W["🌍 World ID 4.0<br/>$8,000"]
        E["📛 ENS Creative<br/>$5,000"]
        C["⛓️ Chainlink CRE<br/>$4,000"]
        Z["💾 0G Wildcard<br/>$3,000"]
        D["🔑 Dynamic JS<br/>$1,670"]
    end

    subgraph INTEGRATION["Technical Integrations"]
        W --> W1["IDKit 4.x React SDK"]
        W --> W2["RP signature in backend"]
        W --> W3["Verify API /v4/verify"]
        W --> W4["Nullifier = community uniqueness"]

        E --> E1["setText() on Public Resolver"]
        E --> E2["Custom records com.holdline.*"]
        E --> E3["Subnames *.holdline.eth"]
        E --> E4["Read via wagmi useEnsText"]

        C --> C1["CRE TypeScript SDK"]
        C --> C2["Cron Trigger every 6h"]
        C --> C3["HTTP Client → News + Market APIs"]
        C --> C4["EVM Read → on-chain data"]
        C --> C5["EVM Write → scores on-chain"]
        C --> C6["Simulation via CRE CLI"]

        Z --> Z1["0G Storage → persistent theses"]
        Z --> Z2["0G Compute → scoring inference"]

        D --> D1["Dynamic JS SDK"]
        D --> D2["Social login (Google/Email)"]
        D --> D3["Transparent embedded wallet"]
    end

    subgraph QUALIFICATION["✅ Sponsor Requirements"]
        W --> WQ["Proof validation in backend ✓<br/>Product breaks without World ID ✓"]
        C --> CQ["On-chain state change ✓<br/>Integrates external API + blockchain ✓<br/>Successful simulation ✓"]
        E --> EQ["Clearly improves the product ✓<br/>No hard-coded values ✓<br/>Present at ENS booth Sunday ✓"]
        Z --> ZQ["GitHub + README ✓<br/>Demo video < 3 min ✓<br/>Novel non-DeFi app ✓"]
        D --> DQ["App deployed and usable ✓"]
    end

    style BOUNTIES fill:#fef3c7,stroke:#d97706
    style INTEGRATION fill:#f0fdf4,stroke:#16a34a
    style QUALIFICATION fill:#e0f2fe,stroke:#0284c7
```

---

## 8. Tech Stack Summary

| Layer | Technology | Role |
|---|---|---|
| **Frontend** | Next.js 14 + React + TailwindCSS | Chat UI + dashboard + panic mode |
| **Auth** | Dynamic JS SDK | Social login, embedded wallet |
| **Identity** | World ID 4.0 (IDKit 4.x) | Proof of human, sybil resistance |
| **LLM** | Claude API (Anthropic) | Thesis parsing, score explanation, chat |
| **Data pipeline** | Chainlink CRE (TypeScript SDK) | Multi-source collection, scoring, consensus |
| **Naming** | ENS (Public Resolver + Text Records) | Thesis hash timestamp, on-chain proof |
| **Storage** | 0G Storage | Persistent theses + history |
| **Compute** | 0G Compute | Decentralized AI inference |
| **Deploy** | Vercel | Frontend + API routes |

---

## 9. Hackathon Build Plan (36h)

```mermaid
gantt
    title HoldLine Build Plan — 36h
    dateFormat HH:mm
    axisFormat %H:%M

    section Day 1 (Friday 1pm → Saturday 1am)
    Project setup Next.js + Dynamic SDK    :a1, 13:00, 2h
    Chat UI + Claude API integration       :a2, after a1, 4h
    Thesis parsing → hypotheses JSON       :a3, after a2, 2h
    World ID IDKit integration             :a4, 13:00, 3h
    World ID backend verify                :a5, after a4, 2h
    CRE pipeline scaffold                  :a6, 16:00, 3h

    section Day 2 (Saturday 8am → Sunday 1am)
    CRE HTTP Client + data sources         :b1, 08:00, 3h
    CRE scoring logic + simulation         :b2, after b1, 3h
    Conviction dashboard UI                :b3, 08:00, 4h
    Panic Mode UI + AI logic               :b4, after b3, 3h
    ENS text records integration           :b5, 14:00, 3h
    0G Storage integration                 :b6, after b5, 2h
    Community view (World ID data)         :b7, 19:00, 2h

    section Day 3 (Sunday 8am → 6pm)
    UI polish + responsive                 :c1, 08:00, 3h
    End-to-end testing + bug fixes         :c2, after c1, 2h
    Demo prep + script rehearsal           :c3, 13:00, 2h
    Record demo video                      :c4, after c3, 2h
    Submission + rest                      :c5, after c4, 1h
```

---

## 10. Key npm Dependencies

```
# Auth & Identity
@worldcoin/idkit-core          # World ID 4.0
@dynamic-labs/sdk-react-core   # Dynamic social login

# ENS
viem                           # ENS text records read/write
wagmi                          # React hooks for ENS

# Chainlink CRE
@chainlink/cre-sdk             # CRE TypeScript SDK
# + CRE CLI installed globally

# AI
@anthropic-ai/sdk              # Claude API

# 0G
@0glabs/0g-ts-sdk              # 0G Storage + Compute

# Frontend
next                           # Framework
tailwindcss                    # Styling
recharts                       # Dashboard charts
framer-motion                  # Animations
```

---

*HoldLine · Technical Architecture · ETHGlobal Cannes 2026*