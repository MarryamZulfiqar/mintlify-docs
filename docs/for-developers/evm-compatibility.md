---
title: "EVM Compatibility"
keywords: ['EVM compatibility', 'Solidity', 'ethers.js', 'Hardhat', 'Foundry', 'precompiles', 'Equihash', 'Blake2b', 'ZEC gas', 'Paymaster', 'ERC-4337', 'ERC-5564']
ai_summary: "Z is EVM-compatible. Identical to Ethereum: Solidity, ABI encoding, JSON-RPC, ethers.js/viem/wagmi, Hardhat/Foundry/Remix, all standard EIPs, all opcodes, gas model (EIP-1559), events, ECDSA/EIP-712, ERC-4337. New on Z (additive, don't affect existing contracts): Equihash precompile at 0x0B (Zcash PoW verification, flat-rate gas), Blake2b precompile at 0x0C (Zcash hashing, linear gas), ERC-4337 Paymaster for ZEC gas. Different on Z: native gas token is Z token (not ETH, not ZEC natively — ZEC accepted via Paymaster, conversion transparent to user), no opcode changes, no Solidity syntax changes. Deploying existing Ethereum contract to Z: update network config only."
---

# EVM Compatibility

Z is EVM-compatible. The vast majority of what you know from Ethereum development works identically on Z. This page covers the complete list of differences — what's identical, what's new, and what's different.

---

## What's identical to Ethereum

Everything in this list works on Z exactly as it works on Ethereum. No code changes required.

| Category | Works identically |
|----------|------------------|
| Language | Solidity (all versions), Vyper |
| ABI encoding | Standard ABI, packed encoding, calldata format |
| JSON-RPC | All standard methods: `eth_call`, `eth_sendRawTransaction`, `eth_getLogs`, etc. |
| JavaScript | ethers.js v5 and v6, viem, wagmi, web3.js |
| Python | web3.py |
| Dev frameworks | Hardhat, Foundry, Truffle, Remix |
| Testing | Mocha, Chai, Foundry forge test, Echidna |
| Contract patterns | OpenZeppelin contracts, ERC-20, ERC-721, ERC-1155, ERC-4626, proxy patterns |
| Opcodes | All EVM opcodes — no opcode changes |
| Gas mechanics | Same gas model (EIP-1559 base fee + priority fee) |
| Events | Same ABI-encoded log format, same filter mechanics |
| Signature verification | ECDSA (`ecrecover`), EIP-712 typed data |
| Account abstraction | ERC-4337 (UserOperations, EntryPoint, Bundlers) |
| Standard precompiles | All standard Ethereum precompiles at 0x01–0x0A |

---

## What's new on Z (additive)

These features exist on Z but not on standard Ethereum. They are additive — they don't affect existing contracts unless you explicitly use them.

### Equihash precompile (0x0B)

Validates Zcash proof-of-work solutions on-chain. Used by Z's ZSP consensus infrastructure (ZcashLightClient) to verify Zcash block headers.

**Gas:** Heavy flat-rate fee, benchmarked on minimum-spec validator hardware to prevent computational DoS.

You only need this precompile if you're building ZSP infrastructure (relayers, validators). Standard application contracts don't use it.

### Blake2b precompile (0x0C)

Blake2b hashing with Zcash personalization strings. Used for Zcash Merkle root verification and ZIP-244 transaction ID computation.

**Gas:** Dynamic linear fee based on payload size.

Same caveat as Equihash — ZSP infrastructure use only.

### ERC-4337 Paymaster for ZEC gas

An ERC-4337 Paymaster contract accepts ZEC for gas, swaps it for the Z token at the protocol level, and pays the EVM gas fee. The conversion is transparent to users — sending ZEC as gas is identical in UX to sending ETH as gas on Ethereum.

This is already available as a system contract. You don't need to deploy your own Paymaster to accept ZEC for gas.

---

## What's different from Ethereum

### Gas token

| Ethereum | Z |
|----------|---|
| Native gas token: ETH | Native gas token: Z token |
| — | ZEC accepted as gas via ERC-4337 Paymaster |

On Ethereum, you pay gas in ETH. On Z, you pay gas in Z tokens. ZEC holders can pay gas in ZEC — the Paymaster handles the conversion and the user never interacts with it directly.

This means:
- Your wallet must hold Z tokens (or ZEC) to pay gas
- The faucet on testnet dispenses Z tokens
- For applications where you want to sponsor user gas, you use an ERC-4337 Paymaster

---

## Migration checklist

Migrating an existing Ethereum dApp to Z:

- [ ] Update network config (RPC URL and chain ID — see [Quick Start](/docs/for-developers/quick-start))
- [ ] Verify your contracts deploy correctly (no changes needed for pure EVM contracts)
- [ ] If your contracts handle gas payments: confirm ZEC Paymaster behavior is acceptable for your users
- [ ] If you want to add ShieldedPool integration: see [MASP Integration Guide](/docs/for-developers/masp-integration-guide)
- [ ] If you're building agent wallets: see [Permissions & Session Keys](/docs/for-agents/permissions-session-keys)

For most applications, the migration is a network config update only.

---

## Precompile reference

| Address | Name | Z-specific? | Purpose |
|---------|------|-------------|---------|
| 0x01 | ecRecover | No | ECDSA recovery |
| 0x02 | SHA-256 | No | SHA-256 hash |
| 0x03 | RIPEMD-160 | No | RIPEMD-160 hash |
| 0x04 | identity | No | Data copy |
| 0x05 | modexp | No | Modular exponentiation |
| 0x06 | ecAdd | No | BN254 point addition |
| 0x07 | ecMul | No | BN254 scalar multiplication |
| 0x08 | ecPairing | No | BN254 pairing check (used for Groth16 proof verification) |
| 0x09 | blake2f | No | Blake2f compression |
| **0x0B** | **Equihash** | **Yes** | **Zcash PoW validation** |
| **0x0C** | **Blake2b** | **Yes** | **Zcash hashing (ZIP-244)** |
