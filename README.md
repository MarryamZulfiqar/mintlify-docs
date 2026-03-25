<br />
<br />

<p align="center">
<img src="./Git-Banner.png" width="80%" height="50%">
</p>


## Z Protocol — the privacy platform for human and agentic on-chain action

This is the repository for the official documentation of Z Protocol — an EVM-compatible, privacy-preserving protocol powered by Zcash, with a vertically integrated DeFi stack built for both humans and AI agents.

## Quick start

Check out the following links:

- Deployed, live documentation: https://docs.zprotocol.xyz
- Protocol website: https://zprotocol.xyz
- GitHub: https://github.com/z-protocol

## Local development

This documentation site is built with [Mintlify](https://mintlify.com).

### Prerequisites

- Node.js 19 or newer
- The Mintlify CLI

```sh
npm i -g mintlify
```

### Run the docs locally

From the repository root, start the local preview server:

```sh
mintlify dev
```

The site will be available at `http://localhost:3000`.

## Contributing

Z Protocol uses [Mintlify](https://mintlify.com) for documentation. Content is written in Markdown (`.md`) and MDX (`.mdx`), configured through `docs.json` at the repository root.

For content changes you have two options:

- [Submit an issue](https://github.com/z-protocol/docs/issues)
- [Submit a pull request](https://github.com/z-protocol/docs/pulls) *(preferred)*

### The instant PR

The fastest way to fix a typo or small content error directly from the live site.

1. Open any page on https://docs.zprotocol.xyz
2. Click the `[ Edit ]` button at the top right of any content page
3. Make your edits in GitHub by clicking the ✎ (pencil) icon
4. Submit a PR with your changes and a brief description of what you fixed

### The typical PR

The standard workflow for larger changes:

1. Fork this repo to your own GitHub account (or clone directly if you are a Z Protocol team member)

2. Open your editor to the top-level repo folder to view the directory structure below

3. Install the Mintlify CLI if you do not already have it:

   ```sh
   npm i -g mintlify
   ```

4. From the repository root, run the local docs development server:

   ```sh
   mintlify dev
   ```

  The Mintlify preview starts locally and serves the docs at `http://localhost:3000`.

5. Make your changes

6. Verify those changes look correct in the local preview

7. Run a broken link check before submitting:

   ```sh
   mintlify broken-links
   ```

8. Submit a pull request with your changes and context for the reviewer

## Directory structure

```
├── README.md
├── docs.json                   <-- Mintlify site configuration and navigation
├── index.mdx                   <-- Homepage
├── llms.txt                    <-- AI discovery index (served at /llms.txt)
├── llms-full.txt               <-- Full concatenated docs for AI context injection
├── CLAUDE.md                   <-- Project instructions for AI coding tools
├── logo/                       <-- Brand assets (light.svg, dark.svg)
├── favicon.svg
│
├── introduction/
│   ├── what-is-z.md
│   ├── why-privacy-now.md
│   └── start-here.md
│
├── core-concepts/
│   ├── privacy-architecture.md
│   ├── zcash-satoshi-plus.md
│   ├── the-zcosystem.md
│   └── the-z-token.md
│
├── products/
│   ├── shieldedpool.md
│   ├── z-trade.md
│   ├── z-lend.md
│   ├── z-stake.md
│   ├── usdz.md
│   ├── yusdz.md
│   └── z-bridge.md
│
├── staking/
│   ├── zec-staking-overview.md
│   ├── self-custodial-staking.md
│   ├── liquid-staking.md
│   └── dual-staking.md
│
├── for-agents/
│   ├── z-for-ai-agents.md
│   ├── wallet-hygiene.md
│   ├── permissions-session-keys.md
│   ├── agent-tooling.md
│   └── simulation-preflight.md
│
├── developers/
│   ├── quick-start.md
│   ├── evm-compatibility.md
│   ├── local-devnet.md
│   ├── masp-integration-guide.md
│   ├── relayAdapter-pattern.md
│   ├── broadcaster-network.md
│   └── skillmd-reference.md
│
├── compliance/
│   ├── privacy-and-compliance.md
│   ├── selective-disclosure.md
│   └── boundary-enforcement.md
│
└── reference/
    ├── roadmap.md
    ├── security-properties.md
    ├── glossary.md
    └── faq.md
```

## Found a broken link?

For broken links within the docs, please [submit an issue](https://github.com/z-protocol/docs/issues) or a PR as described above.

## Validate changes locally

Before opening a pull request, run the local Mintlify preview from the repository root and verify that pages load correctly:

```bash
mintlify dev
mintlify broken-links
```