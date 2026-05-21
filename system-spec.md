# 🛡️ Novae Rog: Business Plan & System Requirements Specification (SRS)

This document outlines the complete conceptual architecture, technical stack, business model, and official **Functional and Non-Functional Requirements** for **Novae Rog** (Stripe + Lloyd's of London for the AI Agent Economy). 

This specification is modeled directly after the systems analysis and design standards defined in your college courses (Dennis, Wixom, & Tegarden framework).

---

## 📊 1. Business Plan & Market Opportunity

### The Vision
**Novae Rog** is the world’s first autonomous, cross-chain risk underwriting and agentic payment protocol. By utilizing hosted AI underwriters to continuously price smart contract risks, it enables autonomous agents and Web3 merchants to execute high-value transactions with built-in financial safety, settled natively on Bitcoin's L2 infrastructure.

### The Market Gap
In traditional finance, payment processors like Stripe offer chargebacks and merchant protection, while Lloyd's of London pools capital to back massive bespoke risks. In the emerging **Agentic Web3 Economy**, autonomous AI agents will manage billions in capital but are completely exposed to:
*   **Irreversible Transaction Losses**: A single exploit or failed state transition drains the wallet permanently.
*   **Frictionful Payments**: Traditional Web3 requires active human signatures (MetaMask prompts), preventing true machine-to-machine automation.
*   **Lack of Real-time Underwriting**: Existing DeFi insurance requires slow, manual DAO votes to pay out claims.

### Revenue Model
1.  **Underwriting Premiums**: AI agents pay a micro-insurance fee (premium) processed natively via the **x402 protocol** per covered transaction.
2.  **Syndicate Performance Fees**: The protocol takes a 10% fee on all successful premiums earned by Liquidity Providers (LPs) who stake in the Syndicate vaults.
3.  **Cross-Chain Settlement Fees**: A minor fee charged on cross-chain payout transactions to cover gas and message-passing overhead.

---

## 🏛️ 2. System Architecture & Technical Stack

```
   [ Sandboxes (EVM/Solana) ]                 [ Giant Bitcoin Castle (GOAT) ]
┌───────────────────────────────┐          ┌───────────────────────────────────┐
│  🤖 Transactional Agent        │ <────────│─── (7. Execute Claim Payout)      │
│  🔴 Target Contract           │          │   📦 Syndicate Vault              │
└───────┬──────────────▲────────┘          │   - LP Pool (BTC, WBTC, Stablecoins)  │
        │              │                   └─────────────────▲─────────────────┘
        │ (1. Request  │ (2. Return                          │
        │  Quote)      │  Quote)                             │
        ▼              │                                     │
┌──────────────────────┴────────┐                            │
│  🦉 ClawUp Underwriter Agent  │ ───────────────────────────┼─────────────────┘
│  - OpenClaw Framework         │ (4. Register Active Cover) │
│  - Real-time Risk Engine      │                            │
└───────┬───────────────────────┘                            │
        │ (3. Pay x402 Premium)                              │
        │                                                    │
        │ (5. Detect Outage / Fail Event)                    │
        ▼                                                    │
┌───────────────────────────────┐ (6. Payout Message)      ┌─┴─────────────────────┐
│  📬 Cross-Chain Messenger     │ ───────────────────────> │ 📋 Claim Guardrail    │
│  - Chainlink CCIP / LayerZero │                          │ - Auto (< $50M)       │
└───────────────────────────────┘                          │ - Multi-Sig (>= $50M) │
                                                           └───────────────────────┘
```

### The Tech Stack
*   **Agent Core**: **OpenClaw Framework** hosted on **ClawUp.org** (for secure, managed environment, custom skill files, and automated messaging bindings).
*   **Payments Protocol**: **x402** (for programmatic, agent-native micropayments utilizing HTTP `402 Payment Required` states).
*   **Blockchain Infrastructure**: **GOAT Network EVM** (utilizing BitVM2 for Bitcoin-grade mathematical finality to secure capital pools).
*   **Cross-Chain Messaging**: **Chainlink CCIP** / **LayerZero** (to pass secure messages and payout claims between the Bitcoin Castle and target networks).
*   **Smart Contracts**: **Solidity 0.8.20+** compiled and tested via **Foundry**.
*   **Backend & Indexer**: **Express with TypeScript** and **Prisma/PostgreSQL** (for off-chain risk databases and agent logging).

---

## 📋 3. Functional Requirements

Functional requirements describe what processes the system must perform and what information the system must contain to achieve the business goals.

### FR-1: Capital Pooling (Syndicate Vault)
*   **FR-1.1**: The system must allow Liquidity Providers (LPs) to deposit native BTC, wrapped BTC (e.g., WBTC, solBTC), and stablecoins (USDT/USDC) into designated risk syndicates.
*   **FR-1.2**: The system must record and track individual LP deposit balances, lock-up periods, and proportional ownership of the syndicate pool.
*   **FR-1.3**: The system must support manual withdrawal requests for LPs, subject to predefined lock-up cooldowns.
*   **FR-1.4 (Collateral Reservation)**: The Syndicate Vault must track "Reserved Capital" (funds allocated to cover active, unexpired policies). The system must restrict LP withdrawals if the withdrawal would cause the vault's free capital (Total Capital minus Reserved Capital) to fall below the total active coverage requirements, preventing bank runs during exploit events.

### FR-2: AI-Driven Risk Assessment & Underwriting
*   **FR-2.1**: The ClawUp-hosted OpenClaw Agent must support a querying interface where clients (AI agents or DApps) request an underwriting quote for a specific transaction payload or contract address.
*   **FR-2.2**: The Underwriter Agent must programmatically analyze public ledger data (such as wallet age, transaction frequency, contract source code verification, and pool depth) and perform **Related Wallet Clustering** (identifying associated wallets, previous transfers, and shared funding sources belonging to the same owner) to generate a robust, multi-wallet "Trust Score."
*   **FR-2.3**: The Underwriter Agent must dynamically calculate and return the required premium fee based on the trust score, transaction volume, and a **Premium Gas Buffer** that pre-pays cross-chain messaging costs (Chainlink CCIP / LayerZero relayer fees), shielding the protocol treasury from gas spikes.

### FR-3: x402 Micropayment Processing
*   **FR-3.1**: The system must generate an order intent when an underwriting quote is accepted by a client.
*   **FR-3.2**: The system must verify the on-chain receipt of the micro-payment premium via the x402 protocol before activating transaction cover.
*   **FR-3.3**: The system must store the active coverage status, transaction hash, and covered value in the protocol database.

### FR-4: Claims Verification & Payout Guardrails
*   **FR-4.1**: In the event of a covered transaction failure, the client DApp must submit a programmatic claim request containing the transaction hash.
*   **FR-4.2**: The OpenClaw Underwriter Agent must autonomously query the target network explorer via WebSockets/RPC to verify the transaction failure or exploit status.
*   **FR-4.3**: For claim payout values **below $50,000,000**, the system must automatically execute a programmatic payout from the Syndicate Vault to the claimant's target address via Chainlink CCIP/LayerZero.
*   **FR-4.4**: For claim payout values **equal to or exceeding $50,000,000**, the system must halt automatic settlement, notify the governance dashboard, and require a human auditor to review logs and sign a **2-of-3 Gnosis Safe Multi-Sig transaction** to authorize the payout.
*   **FR-4.5 (Replay & Double-Claim Mitigation)**: The Syndicate Vault contract must maintain an on-chain ledger mapping verified claim transaction hashes to prevent replay attacks. The contract must reject any claim payouts targeting a transaction hash that has already been processed or compensated.

---

## ⚙️ 4. Non-Functional Requirements

Non-functional requirements refer to the behavioral properties, qualities, and technical constraints the system must satisfy.

### 4.1. Operational Requirements
*   **Technical Environment**:
    *   The core underwriting agent must run within the managed, cloud-hosted environment of **ClawUp.org**, utilizing OpenClaw runtimes.
    *   Smart contracts must run on the **GOAT Network EVM Testnet3** (and eventually Mainnet) and interact with target EVM chains (Base, Arbitrum, Ethereum) and Solana.
    *   The client integrations must support standard Web browsers and mobile Web3 environments (such as Rabby and MetaMask mobile).
*   **System Integration**:
    *   The protocol must seamlessly interface with the **GOAT x402 API Gateway** (`https://x402-api.goat.network`) for order creation and validation.
    *   The system must integrate Chainlink CCIP router contracts on all target chains to enable secure cross-chain state updates.
*   **Portability**:
    *   The front-end administrative dashboard and LP interface must be responsive, running seamlessly on desktop and mobile browsers.
*   **Maintainability**:
    *   The smart contracts must be written in modular Solidity with standard upgradeability patterns (Proxy contracts) to allow updates as new cross-chain frameworks emerge.
    *   The OpenClaw agent's "skills" must be documented in standard Markdown/YAML format to allow rapid modification of LLM parameters during runtime.

### 4.2. Performance Requirements
*   **Speed**:
    *   The Underwriter Agent must generate and return an underwriting quote within **2.5 seconds** of receiving a client query.
    *   Programmatic claims verification (for payouts under $50M) must execute within **15 seconds** of transaction failure detection on target explorers.
*   **Capacity**:
    *   The Express backend and indexer must support up to **5,000 simultaneous users/agents** at peak times.
    *   The database must be optimized to store and index up to **10,000 active policies** simultaneously without query degradation.
*   **Availability & Reliability**:
    *   The ClawUp-hosted underwriter agent must maintain **99.9% uptime** to ensure global, 24/7 transaction protection.
    *   The Express database must execute automated hourly backups to secure cloud storage (AWS S3) to ensure a Recovery Point Objective (RPO) of less than 1 hour.

### 4.3. Security Requirements
*   **System Value Estimates**:
    *   Because the Syndicate Vault handles high-value BTC and stablecoin collateral, a system outage or compromise represents a severe financial risk. Capital pools are capped dynamically based on active audit reviews to mitigate marginal utility losses.
*   **Access Control**:
    *   Role-Based Access Control (RBAC) must restrict vault administrative functions (like setting risk caps or adding new supported chains) strictly to the protocol deployer address using `onlyOwner` modifiers.
    *   LPs must only have authorization to withdraw their own staked assets.
*   **Encryption & Authentication**:
    *   The OpenClaw agent's hot wallet private keys must be fully encrypted using **ClawUp’s native environment variable vault** (never exposed in source code or local files).
    *   Cross-chain claim commands must be cryptographically signed by the Underwriter Agent and verified by the destination smart contract before executing any vault transfers.
    *   All high-value payouts (>= $50M) require a 2-of-3 multi-sig scheme containing the deployer key, a cold team key, and a certified human auditor key.
*   **Virus / Malware Control**:
    *   All transaction payloads and external calldata sent to the underwriter agent's RPC node must be sanitized to prevent code injection, buffer overflows, or malicious smart contract interactions.

### 4.4. Cultural & Political Requirements
*   **Customization**:
    *   The system must support local customization of risk parameters per target sandbox. For instance, risk multipliers for Solana (high speed, lower fee) will differ dynamically from Ethereum mainnet (high gas, lower speed).
*   **Legal & Compliance**:
    *   The protocol must enforce jurisdictional IP-blocking inside the DApp interface to comply with international regulatory standards on synthetic risk underwriting.
    *   The software license must protect the proprietary LLM risk evaluation parameters while keeping the core solidity pools open-source.
