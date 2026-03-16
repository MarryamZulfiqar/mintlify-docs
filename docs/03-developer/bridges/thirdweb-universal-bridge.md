---
id: thirdweb-universal-bridge
title: "Thirdweb Universal Bridge"
sidebar_label: "Thirdweb Universal Bridge"
sidebar_position: 4
description: "Universal Bridge is a comprehensive web3 payment solution to help developers monetize any app or game."
keywords: ["thirdweb", "universal", "bridge"]
tags: ["thirdweb", "bridge"]
hide_table_of_contents: false
pagination_next: "node-operator/types-of-nodes"
pagination_prev: "developer/bridges/chainlink-ccip-guide"
---
# Thirdweb Universal Bridge

---

[**Universal Bridge**](https://thirdweb.com/payments) is a comprehensive web3 payment solution to help developers monetize any app or game.

It enables users to effortlessly onramp, bridge, and swap tokens or fiat across any [EVM](/docs/07-reference/glossary#e) chain through automatic cross-chain routing, removing the complexity of managing multiple chains.

By simplifying cross-chain payments and unlocking multi-chain functionality with minimal implementation effort, it also provides developers with monetization opportunities via fee-sharing and supports advanced capabilities like webhooks and transaction tracking.

This makes building seamless, multi-chain payment experiences fast, reliable, and developer-friendly.

## Features

- Let users pay for assets in any EVM token on any EVM chain
- Automatic cross-chain routing for seamless transactions
- Earn from day one with the fee-sharing mechanism
- Access a ready-made UI component for easy implementation

Learn more in the [Universal Bridge documentation](https://portal.thirdweb.com/payments).

## Get Started

See the [thirdweb documentation](https://portal.thirdweb.com/payments) to get started with Universal Bridge.

## See Universal Bridge in Action

Want to see how Universal Bridge works? Check it out under the hood with this example:

```tsx
import { createThirdwebClient } from "thirdweb";
import { PayEmbed, BuyWidget } from "thirdweb/react";
import { defineChain } from "thirdweb";

const client = createThirdwebClient({
  clientId: "....",
});

function Example() {
  return (
    <BuyWidget
      client={client}
      chain={defineChain(1116)}
      amount="0.002"
      seller="0x0000000000000000000000000000000000000000"
      paymentMethods={["crypto", "card"]}
      currency="USD"
    />
  );
}
```text

See how the Buy Crypto flow works in the [playground](https://playground.thirdweb.com/payments/ui-components).

## Related

- [Core Bridge Resources](/docs/core-bridge-resources)
- [Bridge with LayerZero](/docs/bridging-tokens-using-layerzero)
- [Network Configuration](/docs/network-configuration)
