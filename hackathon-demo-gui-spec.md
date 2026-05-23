# 🎨 Novae Rog: Hackathon Demo & Graphical Dashboard Specification (GUI)

This document outlines the visual layout, user experience (UX) flows, real-transaction demo sequence, and 5-hour build sprint schedule for the **Novae Rog** hackathon presentation dashboard. 

Based on your design choices and comments, the dashboard will utilize a highly visual, graphical pipeline representing the air-gapped agent transitions (the **Wise Forest Council** playground characters) to make the backend cryptographic coordination instantly understandable to the judges.

---

## 🖥️ 1. The "Air-Gap Pipeline" Graphical Dashboard (GUI)

The presentation dashboard is built as a single-page, real-time reactive Web GUI (React with Tailwind CSS or vanilla HTML/CSS with Tailwind). It is styled as a sleek, modern cyber-security terminal with a dark mode base, vibrant color accents, and premium glassmorphic cards.

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│  🛡️ Novae Rog: Autonomous Clearing & Multi-Agent Risk Pipeline [Network: GOAT L2]      │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│   [ 1. RISK ORACLE ] ───► [ 2. x402 CLEARING ] ───► [ 3. DISPUTE ARBIT ] ───► [ 4. VAULT SETTLE ]│
│         🦉 ROSA                 🐿️ XCPA                   🦝 CVAA                 🐻 VRSA      │
│      (Trust Score)            (Candy Box)              (Detective)            (Castle Bear)    │
│      [Status: Active]       [Status: Waiting]        [Status: Idle]         [Status: Safe]     │
│                                                                                        │
├────────────────────────────────────────────────────────────────────────────────────────┤
│  📊 Live State Synchronization Terminal (SSCE Engine)                                  │
├────────────────────────────────────────────────────────────────────────────────────────┤
│  [14:40:01] ROSA 🦉: Analyzing multi-chain cluster for merchant... Trust Score = 92/100  │
│  [14:40:02] ROSA 🦉: Signed quote payload generated. ecrecover signature posted.        │
│  [14:40:05] XCPA 🐿️: Detected order intent. x402 premium payment required: 100 USDT   │
│  [14:40:06] Client 🤖: Programmatic premium payment executed via auto-signed hot wallet  │
│  [14:40:08] XCPA 🐿️: Payment verified on-chain. State updated to: POLICY_ACTIVE          │
│  [Status Log: Idle - Waiting for transaction confirmation or dispute trigger...]       │
│                                                                                        │
├────────────────────────────────────────────────────────────────────────────────────────┤
│  📦 Syndicate Vault: 120.00 BTC  │ 🛡️ Merchant Stake Vault: 2,500 USDT (USDC)          │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

### The Graphical Node Layout (The Forest Council)

Each of the four air-gapped agents is represented as a graphical, glowing card that changes colors dynamically based on active state transitions:

1.  **🦉 ROSA Card (Risk Oracle & Scorer Agent - Agent 1)**
    *   *Visuals:* A Wise Owl avatar with a glowing trust index ring (green for $\ge 95$, yellow for $60-94$, red for $< 60$).
    *   *Live Display:* Shows the active wallet addresses, cross-chain funding source trace, and the generated cryptographic quote payload.
2.  **🐿️ XCPA Card (x402 Clearing & Payment Agent - Agent 2)**
    *   *Visuals:* A Candy-Counting Squirrel avatar holding a glowing x402 payment terminal.
    *   *Live Display:* Shows active x402 micropayment requests. Displays a pulsing progress bar that turns solid green the moment the premium is auto-signed on-chain.
3.  **🦝 CVAA Card (Claims Verification Agent - Agent 3)**
    *   *Visuals:* A Detective Raccoon avatar with a magnifying glass scanning a block explorer graph.
    *   *Live Display:* Shows block explorer queries in real-time. When a dispute is filed, it displays a mock FedEx/UPS shipping API card showing shipment tracking status.
4.  **🐻 VRSA Card (Vault Settlement & Recourse Agent - Agent 4)**
    *   *Visuals:* A Castle Guard Bear guarding a vault door.
    *   *Live Display:* Shows the balances of the **Syndicate Vault** and the **Merchant Stake Vault**. Displays cryptographic signature verification indicators (`ROSA_Sig: OK`, `CVAA_Sig: OK`) before the vault doors open.

---

## ⚡ 2. The Demo Sequence (Real Transaction Simulation)

To demonstrate maximum technical authenticity (Option B), the demo will execute real blockchain transactions on-chain and trigger an actual smart contract revert to show the system's resilience.

*   **Step 1: The Agentic Checkout**
    *   Our AI shopping agent (using OpenClaw) attempts to purchase a physical item (e.g., a $100 jacket on a mock Lululemon/Amazon store). 
    *   The store belongs to a **Medium-Risk Merchant (Trust Score 85/100)**, meaning they qualify for instant settlement but require an x402 risk premium paid to the Syndicate.
*   **Step 2: Premium Auto-Signing**
    *   ROSA generates the quote. The shopping agent programmatically auto-signs the premium payment via a hosted hot wallet (Option A).
    *   The Squirrel (XCPA) detects the premium receipt, updates the state to `Active`, and the merchant instantly receives the $100 purchase price ($T+0$).
*   **Step 3: The Exploit (Real On-Chain Revert)**
    *   To demonstrate claims coverage, we click a "Fail Transaction" button that triggers a real transaction failure (e.g., submitting an invalid signature or calling a reverting smart contract function on-chain).
    *   The transaction hash is logged, and a red failure warning flashes on the GUI.
*   **Step 4: Agentic Arbitration & Refund**
    *   The Detective Raccoon (CVAA) automatically queries the GOAT explorer, verifies the revert status of the transaction hash, and posts a verified claim signature.
    *   The Guard Bear (VRSA) reads the verified proof, confirms the signatures, opens the vault, and executes an instant refund to the shopping agent's wallet.
*   **Step 5: The Merchant Stake Vault Clawback**
    *   We simulate the merchant defaulting on their return.
    *   After a short demo-countdown, VRSA programmatically **liquidates** the stablecoin collateral from the merchant's Stake Vault, returning it to the Syndicate Vault along with the **5% Liquidation Bounty** for stakers.

---

## ⏰ 3. The 5-Hour Build Sprint Plan (Dual-Track)

To guarantee a finished, functioning prototype before the hackathon submission deadline, development will be split into two parallel tracks (Option A):

```
       [ Hour 1 ] ────────► [ Hour 2 ] ────────► [ Hour 3 ] ────────► [ Hour 4 ] ────────► [ Hour 5 ]
Track 1: Deploy Smart       Configure Syndicate   Setup Escrows         Integrate On-Chain   End-to-End
(Contracts) Contracts       Vaults & Pools        & Stake Vaults        Signature Verif.     Debug & Pitch
          ▲                                                                 ▲                Practice
          │                                                                 │
Track 2: Configure OpenClaw  Connect Database      Build GUI Dashboard   Connect Frontend     (Full Run of
(Agent/UI) Runtimes (ClawUp)  State Sync Engine     Layout & Animations   to Database APIs     Revert Demo)
```

### Hour 1: Foundation Deployment
*   **Track 1 (Contracts):** Compile and deploy core Solidity contracts (Syndicate Vault, x402 router interface) on GOAT Network testnet using Foundry.
*   **Track 2 (Agent/UI):** Initialize the four OpenClaw agent runtimes on ClawUp.org, configuring their hot wallet variables. Start the Express database backend.

### Hour 2: State Sync & Context Engine
*   **Track 1 (Contracts):** Establish capital reservation and deposit rules in the Syndicate contracts.
*   **Track 2 (Agent/UI):** Connect the Prisma/PostgreSQL state database. Program the API endpoints for ROSA to post signed quotes.

### Hour 3: Escrows & GUI Visualizer
*   **Track 1 (Contracts):** Code the 30-day Programmatic Escrows and Merchant Stake Vault collateral locks.
*   **Track 2 (Agent/UI):** Develop the graphical dashboard GUI. Animate the state transitions (Pending -> Active -> Disputed -> Refunded) using reactive UI states.

### Hour 4: Cryptographic Integration
*   **Track 1 (Contracts):** Integrate direct on-chain `ecrecover` signature verification in the settlement contracts (Option A).
*   **Track 2 (Agent/UI):** Connect the frontend visualizer directly to the Prisma database API, ensuring live updates as agents post signed state scrolls.

### Hour 5: Integration, Debugging, & Practice
*   **Both Tracks:** Combine the frontend dashboard and the smart contracts. Run three complete test runs of **Option B (Real Transaction Simulation)**. Practice the 5-minute presentation slides to ensure perfect timing.
