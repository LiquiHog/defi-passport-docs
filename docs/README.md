# DeFi Passport Documentation

Non-custodial automated trading on Algorand. You own the contract, you write the
rules, and the automation can only execute what you already permitted.

---

## Documentation Structure

### Core Concepts

What the product is and what holds your funds.

- **[Architecture](core/architecture.md)** - your passport, the registry, the directory, and HOGSWAP
- **[Custody and control](core/custody.md)** - owner-gated methods, free vs reserved balance, and why exit is never blocked
- **[Automation](core/automation.md)** - what the keeper is, what it is paid, and what happens when it stops
- **[Tiers](core/tiers.md)** - the public and beta builds, and moving between them

### Strategies

The four things a passport can be told to do.

- **[Overview](strategies/README.md)** - all four compared, and the vocabulary
- **[Scheduled buys](strategies/dca.md)** - recurring trades at a fixed interval
- **[Limit orders](strategies/limit-orders.md)** - trade at your price or not at all
- **[Grid](strategies/grid.md)** - self-re-arming price cells (beta)
- **[Balancer](strategies/balancer.md)** - hold a portfolio at target values (beta)

### Guides

Doing it.

- **[Getting started](guides/getting-started.md)** - from nothing to a running strategy
- **[Funding and withdrawing](guides/funding-and-withdrawing.md)** - moving value in and out
- **[Positions](guides/positions.md)** - liquidity and staked assets a balancer can count without selling
- **[Closing your passport](guides/closing-your-passport.md)** - unwinding without stranding anything

### Reference

- **[Fees and costs](reference/fees-and-costs.md)** - both fees, the HOG discount, and what the ALGO is for
- **[Developer tools](reference/developer-tools.md)** - SDKs, the HOGSWAP API, and the MCP server
- **[On-chain ids](reference/on-chain-ids.md)** - the directory and registry, and how to verify them
- **[Versions and upgrades](reference/versions-and-upgrades.md)** - what a version bump means for you
- **[Security](reference/security.md)** - what protects you, what does not, and the risks
- **[FAQ](reference/faq.md)** - the questions people actually ask
- **[Glossary](reference/glossary.md)** - terms and definitions

---

## Where to start

**Deciding whether to use it.** Read [Custody and control](core/custody.md) and
[Security](reference/security.md) first. They are the two pages that tell you
what you are trusting and what you are not, and they say so plainly rather than
favourably.

**Choosing a strategy.** [Strategies overview](strategies/README.md) compares
all four in one table. Each has its own page covering what you configure, what
protects you from a bad fill, and where it performs badly.

**Working out the cost.** [Fees and costs](reference/fees-and-costs.md) covers
both fees, the HOG discount, and the ALGO your passport needs to hold.

**Building on it.** [Developer tools](reference/developer-tools.md) and
[On-chain ids](reference/on-chain-ids.md).

---

## What is not here

DeFi Passport is proprietary. This repository documents behaviour, not
implementation, and deliberately excludes:

- contract source, compiled programs, and internal state layouts
- how the keeper decides what to execute, in what order, or at what size
- protocol administration and the mechanism behind tier access

If something you need to make a decision is missing, that is worth an issue -
some of these omissions are deliberate and some are gaps, and we would rather
know which is which.

---

## License

This documentation is provided for informational purposes and is not financial
advice. DeFi Passport is proprietary software; see [LICENSE](../LICENSE).

Contact [@LiquiHog](https://x.com/LiquiHog) on X, the
[Discord](https://discord.gg/a9R4vkXd98), or LiquiHog@gmail.com.
