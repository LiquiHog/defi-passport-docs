# Tiers

DeFi Passport ships as two builds of the same contract, from one source. Which
one you can install depends on your address.

| Tier | Version | Strategies | Availability |
|---|---|---|---|
| **Public** | v1.0.2 | Scheduled buys, limit orders, grid, balancer | Open to any Algorand address |
| **Beta** | v1.1.2 | All of the above, plus lending (Folks Finance) and payments | Allowlisted addresses only |

Both are live on mainnet. Beta also adds proceeds routing and gas paid in
another asset. Every version and what it adds is listed in
[Versions and upgrades](../reference/versions-and-upgrades.md).

## Why grids and balancers started in beta

Until v1.0.2, the public build refused grids and balancers. v1.0.2 opened them.
They started in beta not because they were unfinished, but because they are
harder to configure safely.

A scheduled buy and a limit order each protect a fill with a **single fixed
number**: the minimum output you will accept. There is one figure to get right,
its meaning is obvious, and getting it wrong is visible immediately - the rule
either fills at a price you recognise or it does not fill.

A grid and a balancer are different. A grid cell is a relationship between three
amounts, and a balancer rule carries a target, a band, a buy ceiling, a sell
floor and an expiry on those bounds. Each is a number that can be set to
something that looks reasonable and is not. The failure is quieter: the rule
runs, fills, and costs you more than it should.

So the public build started as the smaller surface. Both builds are the same
contract with the same interface - the difference is a single gate, checked when
a strategy is created, that refuses the strategy types a build does not offer.

Refusing at creation rather than at execution is the part that matters. A build
that accepted a strategy it could never service would leave your funds committed
to a rule nothing could act on, and releasing them would be the only thing left
to do with it. Refusing up front means the restriction can never strand
anything.

## Moving from public to beta

Both versions are in the same line, which means the beta build installs **in
place**. Nothing migrates, no funds move, your existing rules keep running, and
your passport keeps its address and its history. It is an update, not a
replacement.

The reverse is not possible - your passport refuses any version older than the
one it is running. See
[Versions and upgrades](../reference/versions-and-upgrades.md).

As with every update, applying it is something you do. Nobody can move your
passport between tiers without your signature.

## Getting beta access

The launch round of the allowlist is closed. It was drawn from the people
already committed to the project: the largest HOG holders, the largest providers
of HOG liquidity, everyone providing liquidity to a STAMM pool, holders of a
`hog.algo` segment, and a small number of addresses added individually.

**That does not describe future rounds.** It is how the first one was chosen, not
a standing rule, and criteria for the next will be announced rather than
inferred from this.

If you want to be considered, or want to know when the next round opens, ask:

- **X (Twitter)**: [@LiquiHog](https://x.com/LiquiHog)
- **Discord**: [discord.gg/a9R4vkXd98](https://discord.gg/a9R4vkXd98) - the main
  chat is the right place

If you are not on the allowlist, the public build is fully functional for what it
covers. Scheduled buys, limit orders, grids and balancers are not a trial
version; they work exactly as documented, on the same contract with the same
custody guarantees.

## Checking which tier an address has

The registry decides, and the answer can be read from chain before you create
anything - the app does this for you, and the SDK exposes it directly. It returns
which version an address may install, or tells you that none is open to it.

Ask before offering to create a passport rather than finding out from a failed
transaction.
