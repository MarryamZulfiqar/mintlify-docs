---
title: "Roadmap"
slug: roadmap
description: "Z launches with privacy, DeFi, and consensus from day one. Post-launch additions deepen the stack with new staking products, deeper privacy capabilities, and additional applications."
keywords: [roadmap, launch, post-launch, ZSP, MASP, Z Trade, Z Lend, USDZ, stZEC, ZEC bridge, private staking, in-pool DeFi, Z Perps, post-quantum]
ai_summary: |
  Launch features: ZSP consensus (all three legs operational), ShieldedPool (MASP) with UTXO-based notes and Groth16 proofs, stealth addresses via RelayAdapter pattern, compliance infrastructure (KYC/OFAC entry/exit screening via ASPs), relayer network (protocol-operated at launch, permissionless to follow), stZ liquid staking for Z tokens, LayerZero bridge for USDC/USDT/other assets with direct MASP deposit, Z Trade (enshrined DEX, concentrated liquidity), Z Lend (enshrined lending/borrowing), USDZ (1:1 USDC/USDT backed stablecoin), yUSDZ (yield-bearing USDZ vault). Post-launch: stZEC (liquid staking for ZEC, requires SPV bridge), ZEC bridge into ShieldedPool (SPV-verified, non-custodial), shielded staking payouts, partially shielded staking (without revealing position size or validator choice), reduced block times (~0.75s Core hardfork), in-pool RFQ swaps (notes-based atomic swaps, zero public footprint), in-pool lending, new applications (Z Perps, Z Predict), custodian support, post-quantum signatures research, fully shielded staking, fully private DeFi.
---

# Roadmap

Z launches with privacy, DeFi, and consensus from day one. Post-launch additions deepen the stack with new staking products, deeper privacy capabilities, and additional applications.

---

## Launch

- **ZSP consensus.** Z is secured by Zcash miners, ZEC holders, and Z token stakers from the start.
- **ShieldedPool (MASP).** The UTXO-based note system for private balances and transfers, embedded at the protocol level.
- **Stealth addresses.** One-time addresses for unlinkable DeFi interactions via the RelayAdapter pattern.
- **Compliance infrastructure.** KYC/OFAC address blocking at launch.
- **Relayer network.** Operated by the protocol team at launch, with permissionless relayer support to follow.
- **stZ.** Liquid staking for the Z token.
- **LayerZero bridge.** Cross-chain bridge for USDC, USDT, and other assets, with direct deposit into the ShieldedPool.
- **Z Trade.** Enshrined DEX with concentrated liquidity.
- **Z Lend.** Enshrined lending and borrowing protocol.
- **USDZ.** Private stablecoin backed 1:1 by USDC and USDT.
- **yUSDZ.** Yield-bearing version of USDZ.

---

## Post Launch

- **stZEC.** Liquid staking token for ZEC with single-staked yield.
- **ZEC bridge into the ShieldedPool.** Cryptographic, non-custodial bridge for ZEC.
- **Shielded staking payouts.** Staking rewards distributed privately via viewing keys.
- **Partially shielded staking.** Stake without revealing position size or validator choice, initially achievable through viewing keys.
- **Reduced block times.** Adopting the Core hardfork that reduces block times to 0.75 seconds.
- **In-pool RFQ swaps.** Request-for-Quote atomic swaps inside the ShieldedPool with zero public footprint.
- **In-pool lending.** Borrow and lend entirely within the privacy layer.
- **New applications.** Z Perps, Z Predict, and other Zcosystem primitives as they become viable.
- **Custodian support.** Institutional-grade custody integration.
- **Post-quantum signatures.** Research into quantum-resistant signature schemes.
- **Fully shielded staking.** Staking positions completely private, even from validators.
- **Fully private DeFi.** All operations inside the ShieldedPool without public chain interaction.

For the Zcosystem's applications, see [Zcosystem](./07-zcosystem.md). For the privacy architecture, see [Privacy Architecture](./03-privacy-architecture.md). For consensus, see [Consensus (ZSP)](./04-consensus-zsp.md).
