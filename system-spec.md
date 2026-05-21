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
*   **FR-1.4 (Collateral Reservation - Gap A Solution)**: The Syndicate Vault must enforce strict capital locking rules to prevent "bank runs" during exploits:
    *   **FR-1.4.1**: The vault smart contract must calculate `Free Capital = Total Vault Balance - Total Reserved Coverage`, where `Total Reserved Coverage` represents the sum of capital committed to back active, unexpired policies.
    *   **FR-1.4.2**: The contract must programmatically restrict LP withdrawals; any withdrawal request that would cause the vault's Free Capital to drop below 0 must be reverted.
    *   **FR-1.4.3**: Capital allocated to a specific policy must remain locked as `Reserved Coverage` for the policy duration (e.g., 24 hours). If no claim is filed within this window, the capital must automatically revert to the `Free Capital` pool.

### FR-2: AI-Driven Risk Assessment & Underwriting
*   **FR-2.1**: The ClawUp-hosted OpenClaw Agent must support a querying interface where clients (AI agents or DApps) request an underwriting quote for a specific transaction payload or contract address.
*   **FR-2.2**: The Underwriter Agent must generate a robust multi-wallet **Trust Score** by performing **Cross-Chain Related Wallet Clustering**:
    *   **FR-2.2.1**: The Agent must analyze public ledger history across multiple blockchains (including Ethereum, L2 networks like Base/Arbitrum, Solana, and Bitcoin) to trace shared funding sources, such as deposits originating from the same central exchange deposit address or a shared cross-chain bridge transit address.
    *   **FR-2.2.2**: The Agent must scan multi-chain co-interaction graphs, identifying wallet addresses that are commonly registered under the same decentralized identity registries (e.g., ENS or SNS), share identical cryptographic signature payloads, or exhibit highly correlated cross-chain behavioral timing patterns to cluster wallets belonging to the same owner.
    *   **FR-2.2.3**: The Trust Score must degrade programmatically if any wallet in the identified multi-chain cluster has a history of transaction failures, smart contract interactions with known exploits, active blacklists, or malicious activities on *any* covered blockchain network, dynamically adjusting the risk profile.
*   **FR-2.3**: The Underwriter Agent must calculate the premium and shield the treasury from volatility using a **Premium Gas Buffer (Gap C Solution)**:
    *   **FR-2.3.1**: The premium must include a dynamic gas surcharge that pre-pays all cross-chain messaging costs (e.g., Chainlink CCIP router fees or LayerZero relayer gas).
    *   **FR-2.3.2**: A dynamic pricing oracle must update the estimated message-passing gas costs every 10 minutes to guarantee the client pays 100% of the cross-chain overhead, insulating the protocol treasury from gas spikes.

### FR-3: x402 Micropayment Processing
*   **FR-3.1**: The system must generate an order intent when an underwriting quote is accepted by a client.
*   **FR-3.2**: The system must verify the on-chain receipt of the micro-payment premium via the x402 protocol before activating transaction cover.
*   **FR-3.3**: The system must store the active coverage status, transaction hash, and covered value in the protocol database.

### FR-4: Claims Verification & Payout Guardrails
*   **FR-4.1**: In the event of a covered transaction failure, the client DApp must submit a programmatic claim request containing the transaction hash.
*   **FR-4.2**: The OpenClaw Underwriter Agent must autonomously query the target network explorer via WebSockets/RPC to verify the transaction failure or exploit status.
*   **FR-4.3**: For claim payout values **below $50,000,000**, the system must automatically execute a programmatic payout from the Syndicate Vault to the claimant's target address via Chainlink CCIP/LayerZero.
*   **FR-4.4**: For claim payout values **equal to or exceeding $50,000,000**, the system must halt automatic settlement, notify the governance dashboard, and require a human auditor to review logs and sign a **2-of-3 Gnosis Safe Multi-Sig transaction** to authorize the payout.
*   **FR-4.5 (Replay & Double-Claim Mitigation - Gap B Solution)**: The smart contracts must implement cryptographically unique identifiers to prevent double-claiming:
    *   **FR-4.5.1**: The Syndicate Vault must store a mapping of `mapping(bytes32 => bool) public processedClaims` to register paid claims.
    *   **FR-4.5.2**: The unique key for each claim must combine the target chain ID and the transaction hash: `keccak256(abi.encodePacked(chainId, txHash))`.
    *   **FR-4.5.3**: Before any claim payout is sent or authorized, the contract must assert `processedClaims[claimKey] == false` and immediately set `processedClaims[claimKey] = true` upon transaction success, programmatically preventing cross-chain or multi-transaction replay attacks.

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
