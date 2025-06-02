# BitVault Options Protocol

A **Decentralized Bitcoin Options Trading Platform** built on the **Stacks Layer 2**, leveraging Bitcoin’s security for trustless, oracle-settled derivatives.

---

## Overview

**BitVault** is a smart contract-based protocol for **creating, trading, exercising, and settling Bitcoin options (CALLs and PUTs)** in a fully permissionless and transparent manner. By operating on Stacks L2, it inherits Bitcoin’s finality while enabling DeFi-native primitives and automated collateral management.

---

## Features

* **Permissionless Options Creation**: Deploy Bitcoin CALL/PUT options with flexible parameters.
* **sBTC Collateralization**: Positions are backed by sBTC, ensuring strong guarantees.
* **Oracle-Powered Price Feeds**: Secure BTC pricing with staleness protection.
* **Auto Settlement & Expiry**: Options can be exercised or expired through contract logic.
* **Admin Controls**: Platform fees and risk parameters are configurable by the contract owner.
* **User-Centric Balance Management**: Track sBTC deposits and locked collateral per user.

---

## Smart Contract Architecture

```text
+----------------------+
|   Contract Owner     |
|  (Admin Functions)   |
+----------------------+
          |
          v
+----------------------+         +----------------------+
| Oracle System        |<------->| Price Feed Interface |
| - BTC Price Update   |         | - Validates Price    |
+----------------------+         +----------------------+

          |
          v

+--------------------------------------------------------+
|                  BitVault Protocol Core                |
|--------------------------------------------------------|
| - Option Creation & Storage                            |
| - Deposit / Withdraw Logic                             |
| - Collateral Locking Mechanism                         |
| - Exercise & Expiry Functions                          |
| - Fee & Collateral Ratio Governance                    |
+--------------------------------------------------------+

          |
          v

+-----------------------+     +--------------------------+
|  User Balances Map    |     |  Options Map             |
| - sbtc-balance        |     | - ID, Type, Strike, etc. |
| - locked-collateral   |     | - Status, Expiry         |
+-----------------------+     +--------------------------+
```

---

## Key Data Structures

### `options (map)`

Stores details about each option:

```clojure
{
  creator: principal,
  holder: principal,
  option-type: "CALL" | "PUT",
  strike-price: uint,
  expiry: uint,
  amount: uint,
  collateral: uint,
  status: "ACTIVE" | "EXERCISED" | "EXPIRED"
}
```

### `user-balances (map)`

Tracks sBTC balances and locked collateral for each user:

```clojure
{
  sbtc-balance: uint,
  locked-collateral: uint
}
```

---

## Key Functions

### Core Public Methods

| Function                     | Description                                  |
| ---------------------------- | -------------------------------------------- |
| `deposit-sbtc(amount)`       | Deposit sBTC into your account               |
| `create-option(...)`         | Create a new option contract                 |
| `exercise-option(option-id)` | Exercise an active option                    |
| `expire-option(option-id)`   | Settle expired option and reclaim collateral |

### Oracle Functions

| Function                  | Description                            |
| ------------------------- | -------------------------------------- |
| `update-btc-price(price)` | Set new BTC price (oracle-only)        |
| `get-current-btc-price()` | Retrieve and validate latest BTC price |

### Admin Functions

| Function                            | Description                           |
| ----------------------------------- | ------------------------------------- |
| `set-platform-fee(fee)`             | Update fee (basis points)             |
| `set-min-collateral-ratio(ratio)`   | Change minimum collateral requirement |
| `set-oracle-address(address)`       | Assign new oracle address             |
| `set-price-validity-window(blocks)` | Modify price staleness threshold      |

---

## Security & Validations

* **Collateral Safety**: Enforced min-collateral-ratio protects against under-collateralization.
* **Price Freshness**: All pricing uses a validity window to avoid stale quotes.
* **Access Control**: Only contract owner can modify sensitive parameters.
* **Error Handling**: Descriptive error codes (e.g. `ERR_INVALID_PRICE`, `ERR_OPTION_EXPIRED`).

---

## Developer Notes

* **Stacks Language**: Written in Clarity, the smart contract language for the Stacks blockchain.
* **sBTC Integration**: Ensure sBTC wrapper and logic are implemented externally or imported.
* **Contract Ownership**: `CONTRACT_OWNER` is statically set to `tx-sender` at deploy time—update this if deploying via a factory or DAO.

---

## Future Enhancements (Optional)

* sBTC redemption support
* Option trading (secondary market)
* Multi-oracle redundancy
* UI & analytics dashboards
* DAO-based parameter governance
