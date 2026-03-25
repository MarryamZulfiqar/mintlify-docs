---
title: "SKILL.md Reference"
keywords: ['SKILL.md', 'agent documentation', 'LLM', 'structured walkthroughs', 'self-contained', 'agent tooling']
ai_summary: "SKILL.md files are structured documentation files designed for LLM/agent consumption. Four required properties: (1) Self-contained — no mid-flow external links, everything needed to complete the task on one page; (2) Deterministic structure — same format across every file (objective, prerequisites, steps, verification); (3) Real executable values — actual contract addresses and network parameters, no placeholder values; (4) Inline references — ABIs and addresses appear where they are used. Z ships SKILL.md files for all core operations. External developers write their own SKILL.md files using Z's template and format conventions."
---

# SKILL.md Reference

SKILL.md files are structured documentation files designed for LLM and AI agent consumption. Each file covers one complete operation end-to-end. Agents parse them, extract parameters, and execute — without needing to follow links to other pages or infer missing information.

---

## The four required properties

**1. Self-contained.** Each SKILL.md is a single document. No mid-flow external links. No "see also" references. No "refer to the API docs for the ABI." Everything an agent needs to complete the task lives on one page.

Every cross-page dependency is a failure mode. If an agent must fetch a second document to get an ABI, it has an opportunity to hallucinate the missing piece. SKILL.md files eliminate this entirely.

**2. Deterministic structure.** Every SKILL.md follows the same format:

```markdown
# [Operation Name]

## Objective
One sentence describing the outcome.

## Prerequisites
- What the agent must have before starting
- What contracts must be deployed
- What tokens or balances are required

## Steps
### Step 1: [Name]
[Exact instructions]
[Inline ABI if needed]
[Real contract addresses]

### Step 2: [Name]
...

## Verification
How to confirm the operation succeeded.

## Common Errors
Known failure modes and how to recover.
```

An agent that has parsed one Z SKILL.md can predict the shape of every other one. Predictable structure reduces tokens spent orienting and leaves less surface area for hallucination.

**3. Real executable values.** All protocol-side values are real and complete: contract addresses, network parameters, ABIs, default configurations. No pseudocode. No placeholder addresses. No `[YOUR_ADDRESS_HERE]` tokens in the middle of a transaction call.

Agent-specific inputs (wallet address, signing key, token amounts) are clearly typed and named so the agent knows exactly what to supply and in what format.

**4. Inline references.** Contract ABIs, deployment configs, and network parameters appear where they are used — not on a separate reference page the agent would need to locate and cross-reference.

---

## Template

```markdown
# [Operation Name]

**Network:** [Mainnet / Testnet]  
**Gas estimate:** [X gas at current conditions]  
**Prerequisites:** [List inline]

## Objective
[One sentence. What the agent will have accomplished when complete.]

## Prerequisites
- [Concrete requirement 1, e.g., "USDZ balance >= 100 in MASP"]  
- [Concrete requirement 2, e.g., "Z token balance >= 0.01 for gas"]
- [Contract X is deployed at 0xABC... on mainnet]

## Steps

### Step 1: [Action name]

[Description of what this step accomplishes.]

Call `[function_name]` on `[ContractName]` at `[0xDeployedAddress]`:

\`\`\`
ABI: [exact function signature]
Parameters:
  - [param1]: [type] — [description, including valid ranges]
  - [param2]: [type] — [description]
\`\`\`

Expected output: [what the agent should see on success]

### Step 2: [Action name]
...

## Verification

After completing all steps, confirm success by:
- [Check 1: exact verification step]
- [Check 2: exact verification step]

## Common Errors

| Error | Cause | Recovery |
|-------|-------|---------|
| [Error message] | [Why it happens] | [How to fix] |
```

---

## Z's own SKILL.md files

Z ships SKILL.md files for all core operations. They are designed to be injected directly into agent context.

> **⚠ Engineering input required:** The URL where Z's SKILL.md files are hosted, how to access them programmatically (API endpoint or CDN path), and the complete list of available operations require documentation from the engineering team.

---

## Writing SKILL.md files for your ZApp

If you are building a ZApp and want to make it agent-accessible:

1. Identify the core operations your ZApp supports
2. Write one SKILL.md per operation using the template above
3. Host them at a predictable URL (e.g., `https://your-zapp.xyz/skills/`)
4. Register them in Z's agent tooling registry

The most common mistake is writing SKILL.md files for humans and then labeling them for agents. Human documentation explains decisions, provides context, and links to background reading. SKILL.md files skip all of that. They start at the objective and end at verification. Nothing else belongs in them.
