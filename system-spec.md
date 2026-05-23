# 🛡️ Novae Rog: Business Plan & System Requirements Specification (SRS)

This document outlines the complete conceptual architecture, technical stack, business model, and official **Functional and Non-Functional Requirements** for **Novae Rog** (providing programmatic payment clearing and real-time decentralized risk underwriting for the AI Agent Economy, modeled as an automated equivalent to traditional payment gateways and Lloyd's-style bespoke underwriting syndicates).

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
    *   **FR-1.4.4 (Syndicate Pool Exposure Cap)**: To insulate stakers from catastrophic smart contract exploits, the vault contract must programmatically reject any policy underwriting request if the requested coverage amount exceeds **10%** of the vault's current `Free Capital`.


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
*   **FR-3.4 (x402 Payment Intent Memo Mapping)**: To enable fully automated, machine-to-machine clearing, the client's x402 premium transaction must explicitly include the unique `policyId` (generated by hashing the approved ROSA quote parameters) inside the on-chain transaction memo/data payload. This enables the x402 Clearing & Payment Agent (XCPA) to programmatically match the premium payment to the pending policy and update its state to `Active`.

### FR-4: Claims Verification & Payout Guardrails
*   **FR-4.1**: In the event of a covered transaction failure, the client DApp must submit a programmatic claim request containing the transaction hash.
*   **FR-4.2**: The OpenClaw Underwriter Agent must autonomously query the target network explorer via WebSockets/RPC to verify the transaction failure or exploit status.
*   **FR-4.3**: For claim payout values **below $50,000,000**, the system must automatically execute a programmatic payout from the Syndicate Vault to the claimant's target address via Chainlink CCIP/LayerZero.
*   **FR-4.4**: For claim payout values **equal to or exceeding $50,000,000**, the system must halt automatic settlement, notify the governance dashboard, and require a human auditor to review logs and sign a **2-of-3 Gnosis Safe Multi-Sig transaction** to authorize the payout.
*   **FR-4.5 (Replay & Double-Claim Mitigation - Gap B Solution)**: The smart contracts must implement cryptographically unique identifiers to prevent double-claiming:
    *   **FR-4.5.1**: The Syndicate Vault must store a mapping of `mapping(bytes32 => bool) public processedClaims` to register paid claims.
    *   **FR-4.5.2**: The unique key for each claim must combine the target chain ID and the transaction hash: `keccak256(abi.encodePacked(chainId, txHash))`.
    *   **FR-4.5.3**: Before any claim payout is sent or authorized, the contract must assert `processedClaims[claimKey] == false` and immediately set `processedClaims[claimKey] = true` upon transaction success, programmatically preventing cross-chain or multi-transaction replay attacks.
*   **FR-4.6 (Target Chain RPC Outage Safeguard)**: If the Claims Verification Agent's RPC queries to the target chain explorer timeout or fail due to external network outages or RPC throttling, the smart contracts on GOAT L2 must programmatically freeze the policy's expiration countdown timer on-chain, preventing policy expiry during third-party network downtime and preserving client claims.

### FR-5: Programmatic Escrow & Everyday Retail Underwriting
*   **FR-5.1**: The system must support a **Programmatic Escrow Pathway** for consumer retail e-commerce transactions (e.g., shopping on Amazon, Fashion Nova, or Lululemon).
*   **FR-5.2**: The escrow smart contract must lock client funds upon transaction initialization and support a standard **30-day return dispute window**.
*   **FR-5.3**: The Underwriter Agent must programmatically interface with shipping API endpoints (e.g., FedEx, UPS, DHL, or merchant portals) to verify delivery status and return tracking.
*   **FR-5.4**: If a shipping dispute or return is verified by the Underwriter Agent within the 30-day window, the contract must autonomously refund the locked escrow to the shopping agent's wallet.
*   **FR-5.5**: If no dispute is filed within the 30-day window, the contract must programmatically release the locked funds to the merchant's target wallet.
*   **FR-5.6**: To optimize scaling for micro-transactions:
    *   **FR-5.6.1**: The Underwriter Agent must cache risk scores and coverage quotes off-chain to minimize RPC calls.
    *   **FR-5.6.2**: All retail transactions must utilize high-throughput L2/L3 execution (GOAT L2) to ensure transactional gas fees remain below $0.001 per purchase.
*   **FR-5.7**: The system must enforce **Dynamic Settlement Pathways** for merchants based on their real-time trust score to balance merchant cash flow and consumer protection:
    *   **FR-5.7.1 (Instant Payout Pathway - $T+0$)**: If a merchant's Trust Score (as defined in FR-2.2) is $\ge 95/100$, the escrow contract must instantly release the transaction capital to the merchant's wallet at checkout, waiving the 30-day return lockup.
    *   **FR-5.7.2 (Staker-Backed Risk Coverage)**: For transactions cleared instantly under FR-5.7.1, if a customer agent files a valid dispute or returns an item within 30 days, the Underwriter Agent must trigger a payout directly from the **Syndicate Vault** pool to refund the buyer, fully insulating the consumer from merchant default.
    *   **FR-5.7.3 (Syndicate-Backed Instant Payout Option)**: If a merchant's Trust Score is between 60 and 94/100, the system must allow the merchant to request an instant settlement in exchange for a dynamic risk premium paid via the **x402** protocol to the Syndicate Vault to offset the credit and chargeback risk.
    *   **FR-5.7.4 (Standard Escrow Pathway)**: If a merchant's Trust Score is $< 60/100$ or unverified, the contract must strictly lock funds in the standard 30-day escrow, releasing them only upon carrier delivery verification or return window expiry.
*   **FR-5.8**: The system must implement a robust **Merchant Recourse Flow** to safeguard the Syndicate Vault stakers against capital draining caused by disputes and returns cleared under $T+0$ pathways:
    *   **FR-5.8.1 (Programmatic Reimbursement Prompts)**: Upon verifying a refund dispute, the ClawUp Underwriter Agent must instantly trigger a programmatic notification payload (webhook and messaging bridge) to the merchant's external integration dashboard or dedicated alert channels, issuing a formal debt notice detailing the transaction hash, Syndicate Vault debit amount, and a strict 48-hour replenishment deadline.
    *   **FR-5.8.2 (Linked Settlement Clawback)**: Simultaneously with the buyer's refund execution, the escrow smart contract must attempt to programmatically execute an automated direct clawback (pull transaction) targeting the merchant's linked Web3 clearing wallet or active pending payout streams to reclaim the debited funds.
    *   **FR-5.8.3 (Merchant Stake Vault & Programmatic Liquidation)**: To qualify for the $T+0$ Instant Payout Pathway (defined in FR-5.7.1) or the Syndicate-Backed Option (defined in FR-5.7.3), merchants must maintain a locked collateral threshold in a dedicated **Merchant Stake Vault** on the GOAT Network. If the merchant fails to manually top up or clear their debt within the 48-hour notification window, the smart contract must programmatically liquidate the required amount from their Stake Vault and transfer it directly to the Syndicate Vault, keeping stakers fully whole.
    *   **FR-5.8.4 (Trust Score Degradation & Premium Penalties)**: If a merchant's debt remains unliquidated due to insufficient collateral, or their Stake Vault balance falls below the minimum required threshold, the system must instantly degrade their Trust Score by a fixed penalty of 20 points, revoke their $T+0$ instant payout eligibility, downgrade their account to the Standard 30-Day Escrow Pathway, and spike their future x402 dynamic risk premiums to act as a financial disincentive for non-performance.


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
