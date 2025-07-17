# IDO Vesting Monorepo

This repository contains IDO with vesting Smart Contracts, implemented using both **Anchor** and **native Rust** on Solana.

---

## Use Cases

- Token launches with controlled unlock schedules  
- Investor vesting plans  
- DAO treasury distribution  
- Any scenario requiring scheduled, partial token unlocks  

---

## Project Status

| Component             | Status           |
|-----------------------|------------------|
| Anchor Implementation | ✅ MVP completed |
| Native Rust           | ✅ Production-ready |

---

## Repository Structure

This monorepo contains two submodules:

- [`anchor`](https://github.com/ibg101/ido-vesting-anchor) - Anchor-based implementation  
- [`native_rust`](https://github.com/ibg101/ido-vesting-native) - Native Rust implementation  

> Both share a common instruction layer and business logic

---

## Features

- **Modular Vesting Logic**      
  Configurable `LinearVestingStrategy` with optional cliff and periodic unlocks.  
  > `MAX_UNLOCKS` must be >= 100
  
- **Flexible Recipient Design**      
  During the claim phase, any recipient and ATA can be specified - not limited to the original buyer.

- **Initialize, Buy, Claim Instructions**  
  Core functionality of this program. See [Program Instructions](https://github.com/ibg101/ido-vesting-monorepo?tab=readme-ov-file#program-instructions) for more details.

- **SPL Token 2022 & ATA Support**          
  Fully compatible with Token-2022 standard, including ATA derivation and mint logic.

- **No Anchor Dependency (in Native version)**  
  Native Rust implementation is entirely free of Anchor dependencies, resulting in:
    - Lower CU usage (check [benchmark section](https://github.com/ibg101/ido-vesting-monorepo?tab=readme-ov-file#benchmarks-cu-usage))
    - Faster execution

---

## Program Instructions

### 1. Initialize IDO with Vesting    
  - Initializes the IDO's `Treasury ATA`, `Config`.
  - Accepts a customizable `LinearVestingStrategy` and other arguments such as `lamports_per_token` and `transfer_amount`.
  > `transfer_amount` - amount of SPL tokens to be transferred from the `signers ATA` to the `Treasury ATA`

### 2. Buy with Vesting    
  - Creates a `VestingAccount` (if needed).
  - Transfers lamports from the buyer to the `Treasury ATA`.

### 3. Claim    
  - Checks eligibility based on vesting schedule, 
  - Initializes `recipient ATA` (if needed).
  - Transfers SPL tokens according to the unlocked portion to the `recipient ATA`.

---

## Benchmarks (CU usage)
> This section compares two implementations of the same Smart Contract.

You can verify the data [here](https://github.com/ibg101/ido-vesting-monorepo/blob/main/benchmarks).

| Component        | Initialize IDO | Buy Tokens | Claim Tokens (1st) | Claim Tokens (2nd) |
|------------------|----------------|------------|---------------------|---------------------|
| **Anchor**       | 38,374 CU      | 20,574 CU  | 22,083 CU           | 21,864 CU           |
| **Native Rust**  | 18,068 CU      | 9,662 CU   | 10,947 CU           | 11,041 CU           |

As you can see, the **native Rust implementation consumes ~2x fewer compute units** across all instructions!
