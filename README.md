# BitMint Protocol

**Turn idle Bitcoin into productive capital through overcollateralized lending.**  
BitMint enables Bitcoin holders to unlock liquidity while maintaining full BTC exposure—allowing users to mint synthetic stablecoins against BTC collateral, powered by Stacks’ Bitcoin finality.

---

## 🧭 System Overview

BitMint is a **Bitcoin-backed synthetic asset protocol** deployed on the **Stacks blockchain**, bridging Bitcoin’s store-of-value strength with decentralized finance (DeFi) utility.  
The system is designed for **non-custodial collateralized lending**, where users deposit Bitcoin (or Stacks-native wrapped BTC representations) to mint stable-value tokens without sacrificing asset ownership.

**Key Features**

- **Overcollateralized Lending:** Mint stablecoins against BTC while retaining upside exposure.  
- **On-Chain Collateralization:** Enforced minimum collateral ratios ensure systemic solvency.  
- **Trustless Liquidations:** Protocol-driven liquidation when collateral ratios breach safety thresholds.  
- **Native BTC Settlement:** Leverages Stacks' Bitcoin finality for settlement assurance.  
- **Transparent Oracles:** Admin-managed, auditable price feeds.

---

## ⚙️ Contract Architecture

The BitMint protocol consists of a **single primary Clarity smart contract** responsible for collateral management, loan issuance, repayment, liquidation, and administrative controls.

### **Core Components**

| Category | Module / Function | Description |
|-----------|------------------|--------------|
| **Constants & Errors** | Defined protocol-level constants, valid asset types (`BTC`, `STX`), and standardized error codes for predictable error handling. |
| **State Variables** | Stores protocol parameters like `collateral-ratio`, `liquidation-threshold`, and global metrics (`total-btc-locked`, `total-loans-issued`). |
| **Data Maps** | Persistent on-chain registries for loans, user indices, and price oracles. |
| **Read-Only Functions** | Provide transparent, gas-free access to protocol state (loans, stats, assets). |
| **Administrative Functions** | Owner-only functions for initialization and parameter management. |
| **Core Operations** | User-facing interactions: deposit collateral, request loans, repay loans. |
| **Private Helpers** | Internal logic for interest computation, collateral checks, and liquidation triggers. |

---

## 🧩 Data Structures

### **1. Loan Registry**

Stores individual loan records:

```clarity
{ 
  borrower: principal,
  collateral-amount: uint,
  loan-amount: uint,
  interest-rate: uint,
  start-height: uint,
  last-interest-calc: uint,
  status: (string-ascii 20)
}
```

### **2. User Loan Index**

Associates each user with a list of active loan IDs:

```clarity
{ user: principal } → { active-loans: (list 10 uint) }
```

### **3. Collateral Price Oracle**

Maintains trusted price data for supported assets:

```clarity
{ asset: (string-ascii 3) } → { price: uint }
```

---

## 🔄 Protocol Flow

### **1. Initialization**

- The contract owner initializes the platform via `initialize-platform`.
- Collateral ratio, liquidation threshold, and fee rate are set.

### **2. Collateral Deposit**

- Users lock Bitcoin-backed tokens through `deposit-collateral`.
- The protocol tracks total BTC collateralized.

### **3. Loan Request**

- Users call `request-loan(collateral, loan-amount)`.
- The protocol verifies collateral sufficiency using live BTC prices.
- Upon approval, a unique loan ID is issued and tracked.

### **4. Loan Repayment**

- Users repay outstanding principal + accrued interest using `repay-loan`.
- Collateral is released upon full repayment.
- User’s active loan index is updated.

### **5. Liquidation**

- When BTC price drops below threshold, `check-liquidation` triggers.
- If ratio < `liquidation-threshold`, the position is automatically liquidated.

---

## 🔒 Security Considerations

- **Overcollateralization:** Prevents systemic insolvency by ensuring loans are always backed by more BTC than borrowed value.
- **Admin Gating:** Only the protocol owner can update oracle feeds and key ratios.
- **Oracle Validation:** Each feed update enforces valid price and asset formats.
- **Non-Custodial:** Collateral management and accounting are on-chain, minimizing trust dependencies.

---

## 🧮 Key Parameters

| Variable | Default | Description |
|-----------|----------|-------------|
| `minimum-collateral-ratio` | 150% | Required BTC-to-loan value ratio. |
| `liquidation-threshold` | 120% | Safety trigger for liquidation. |
| `platform-fee-rate` | 1% | Protocol fee on loan issuance. |

---

## 🧠 Future Extensions

- **Automated Oracle Integration:** Chainlink or Hiro price feeds for decentralized pricing.
- **Multi-Collateral Support:** Support for STX, sBTC, or wrapped assets.
- **Stablecoin Minting:** Pegged tokens representing borrowed value.
- **Dynamic Interest Rates:** Risk-adjusted rates based on utilization.

---

## 📜 License

MIT License © 2025 BitMint Protocol Contributors

---

## 🧑‍💻 Author Notes

Developed for the **Stacks blockchain** using **Clarity**, BitMint demonstrates a secure, composable design for Bitcoin-backed DeFi.  
Engineered with explicit control flow, transparent accounting, and deterministic contract behavior, the protocol aims to serve as a foundational primitive for Bitcoin-native financial systems.
