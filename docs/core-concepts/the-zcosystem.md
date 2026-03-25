---
title: "The Zcosystem"
sidebarTitle: "The Zcosystem"
keywords: ['Zcosystem', 'ZApps', 'Z Trade', 'Z Lend', 'Z Stake', 'USDZ', 'yUSDZ', 'RelayAdapter', 'DeFi composition', 'private DEX', 'private lending', 'vertical integration', 'concentrated liquidity']
ai_summary: "The Zcosystem is Z's vertically integrated DeFi application layer. Z Trade: Uniswap v3 fork, concentrated liquidity, MASP integration via RelayAdapter (atomic unshield → execute → reshield). adaptParams binding = keccak256(nullifiers, commitments, actionData). Primary pairs: ZEC/USDZ, Z/USDZ, stZEC/USDZ, stZ/USDZ. 50% protocol fee on USDZ trading pairs. Z Lend: Aave fork, MASP integration via RelayAdapter. Collateral: ZEC, stZEC, Z, stZ, USDZ, yUSDZ. Reserve factor model (majority to depositors). Z Stake: issues stZEC (post-launch) and stZ. USDZ: 1:1 USDC/USDT stablecoin. yUSDZ: ERC-4626 yield vault, non-rebasing. DeFi composition: stake ZEC into stZEC → deposit as collateral → borrow USDZ → deposit into yUSDZ → shield everything. Vertical integration rationale: agent predictability, privacy design coordination, internal revenue compounding, AI makes it practical."
---

# The Zcosystem

The Zcosystem is Z's application layer: a coordinated set of DeFi primitives, each integrated with the MASP at the protocol level.

This is a deliberate choice. Deep MASP integration across every application requires coordinated design. A single trusted stack gives AI agents a composable environment they can reliably coordinate across without discovering or evaluating third-party protocols. And AI tooling has collapsed the cost of shipping quality software, making vertical integration increasingly practical.

---

## Z Trade

Z's flagship DEX with concentrated liquidity, enshrined on Z with native MASP integration.

### How private trading works

Z Trade uses a RelayAdapter contract to execute an atomic three-step flow: unshield, execute, reshield.

1. The user's assets sit as private notes in the MASP.
2. The user builds a ZK proof authorizing the swap and submits it to a broadcaster.
3. The broadcaster calls the RelayAdapter, which atomically:
   - Unshields the input tokens from the MASP to the adapter contract
   - Executes the swap on the AMM (approve, swap, collect output)
   - Reshields the output tokens back into the MASP as new private notes

The entire sequence is one transaction. An observer sees that a swap happened at the adapter contract. They cannot see who initiated it, link it to prior activity, or connect it to a MASP balance.

The adaptParams binding (`keccak256(nullifiers, commitments, actionData)`) is included in the ZK proof's public inputs. This prevents the broadcaster from modifying the swap parameters, changing the recipient, or substituting a different trade. If any bound parameter changes, the proof is invalid.

### Trading characteristics

- Standard AMM with concentrated liquidity (Uniswap v3 mechanics)
- Primary pairs: ZEC/USDZ, Z/USDZ, stZEC/USDZ, stZ/USDZ
- 50% protocol fee on USDZ trading pairs (other 50% to liquidity providers)

---

## Z Lend

Z's flagship lending and borrowing protocol based on Aave, enshrined on Z with MASP integration.

### How it works

Deposit collateral and borrow against it. Supported collateral assets include ZEC, stZEC, Z, stZ, USDZ, and yUSDZ. The same RelayAdapter pattern used by Z Trade applies here: deposit and borrow operations can be executed from the MASP through stealth addresses, keeping the borrower's identity unlinkable.

### Revenue model

Z Lend generates protocol revenue through a reserve factor on borrow interest, following the Aave model. Depositors earn the majority of interest paid by borrowers. The protocol captures a percentage of the spread.

### Sample use case

Deposit stZEC as collateral. Borrow USDZ. Maintain ZEC price exposure and earn staking yield (if using stZEC) while accessing private stablecoins. This is the composition path described in [Liquid Staking](/docs/staking/liquid-staking).

---

## Z Stake

The staking application for the Zcosystem. Z Stake issues liquid staking tokens that turn staked assets into composable capital.

**stZEC** (post-launch) represents staked ZEC on Z. Deposit ZEC, receive stZEC, earn consensus rewards as stZEC appreciates. stZEC is freely composable: trade it on Z Trade, use it as collateral on Z Lend, deposit it into the MASP for privacy.

**stZ** is the same concept for the Z token. Stake Z, receive stZ, earn consensus rewards. stZ maintains DeFi composability while the underlying Z participates in consensus.

For the full liquid staking architecture, see [Liquid Staking](/docs/staking/liquid-staking).

---

## USDZ

Privacy-preserving stablecoin backed 1:1 by USDC and USDT.

Deposit USDC or USDT into the USDZ mint contract on the source chain. Receive USDZ on Z. USDZ can be deposited into yUSDZ to earn yield, used as collateral on Z Lend, traded on Z Trade, or shielded in the MASP.

For the full USDZ architecture, see [USDZ](/docs/products/usdz).

---

## yUSDZ

A yield-bearing stablecoin token. Users deposit USDZ into an ERC-4626 vault and receive yUSDZ, which appreciates as yield accrues. Yield sourcing is to be determined.

For the full yUSDZ architecture, see [yUSDZ](/docs/products/yusdz).

---

## What's next

**Z Perps.** Perpetual futures with private positions. Leverage trading where nobody outside can see position sizes, direction, or liquidation levels.

**Z Vault.** Yield strategy vaults across different assets. Z Vault accepts deposits and deploys them into optimized strategies within the Zcosystem.

---

## Why vertical integration

**Agents need a single trusted stack.** AI agents operate best when they can move through a composable set of applications with predictable patterns and no need to discover or evaluate third-party protocols.

**Privacy integration benefits from coordinated design.** The RelayAdapter pattern, which makes private DeFi possible, requires tight coupling between each application and the privacy layer. This works best when the applications are designed as one system.

**Revenue compounds internally.** Because every primitive is enshrined, revenue from Z Trade fees, Z Lend interest, USDZ fees, yUSDZ performance fees, staking protocol fees, and bridge fees stays within the protocol.

**AI makes vertical integration practical.** AI tooling has made it dramatically faster and safer to ship a full vertical stack. What used to require separate teams for each primitive can now be built and maintained as one coordinated system.

---

## The DeFi composition stack

The applications are designed to compose. A user can move through the full stack in a single session:

1. Stake ZEC into stZEC (earn consensus rewards)
2. Deposit stZEC as collateral on Z Lend
3. Borrow USDZ against it
4. Deposit USDZ into yUSDZ (earn yield on the borrowed stables)
5. Shield everything in the MASP

The result: ZEC price exposure, staking yield, borrowed stablecoins, yield on those stablecoins, and privacy over the full position. Each application handles one step. The MASP ties them together.
