# Security

What protects you, what does not, and what can still go wrong. Written to be
useful for deciding whether to use DeFi Passport, which means it has to include
the unflattering parts.

## What protects you

### Custody

Your passport is a contract owned by your address. Every method that moves value
checks the caller against the owner recorded at creation, and that record cannot
be changed. There is no administrative override, no recovery method, and no
method that lets us or the keeper move your funds.

The automation has exactly one capability: triggering a rule you wrote. It cannot
withdraw, cannot create or edit a rule, and cannot opt your passport out of an
asset.

### Price bounds, enforced on-chain

Every rule carries a bound - a minimum output, a target price, a ceiling and
floor - and the contract enforces it by measuring what actually left and what
actually arrived, after the swap, before allowing the transaction to succeed.

It does not take the route's word for what a trade was worth. A route producing
less than your rule requires is rejected regardless of who proposed it.

**No strategy can be created with no protection at all.** The contract refuses.
Where a strategy type could otherwise run unbounded - a schedule, most obviously
- the bound is mandatory rather than optional.

### Bounds that go stale stop working

A balancer's price bounds expire. Static bounds drift against the market: a
ceiling set just above today's price is a ceiling far above the market after a
large fall, and the contract cannot tell a correct bound from a drifted one
without a price feed.

It can tell whether a bound is *old*. So you declare how long you stand behind
your numbers, and past that point the rule stops rebalancing rather than
rebalancing at a bound you no longer mean. See
[Balancer](../strategies/balancer.md).

### Reserved funds

Funds a rule needs are held for it and cannot be withdrawn or spent by another
rule. This protects the rule from you as much as you from the rule - a rule whose
funds could vanish underneath it would fail at the moment it mattered.

The accounting behind this is a single invariant the contract re-checks on every
operation that moves value: what is recorded as reserved must never exceed what
is actually held.

### Automation costs are bounded

The keeper reclaims exactly what a trigger transaction cost it, from a reserve
you set aside for the purpose, with a ceiling on any single reclaim. It cannot
reach your free balance or a rule's funds, and it cannot drain the reserve by
inflating its own transaction fees.

### Upgrades and exit

Only you can upgrade your passport, downgrades are refused, and a change to how
records are stored cannot be applied in place. Closing your passport is always
available and cannot be gated. See
[Versions and upgrades](versions-and-upgrades.md).

## What does not protect you

### The code has not been audited by a third party

DeFi Passport has **not** had an independent security audit. It has been reviewed
and tested internally and is running on mainnet with real funds, but no external
firm has examined it.

Treat that as material. It is the single most important sentence on this page.

### It is not open-source

The contract source is not published and is not planned for release. You cannot
read the code that holds your funds, and neither can anyone else outside
LiquiHog.

What you *can* verify independently is substantial - the deployed program is on
chain, your passport's owner and version are in its public state, its rules and
positions are publicly readable, and every fill is logged. That is verification of
behaviour, not of source. It is not the same thing and this page will not pretend
it is.

### It is beta software

Live on mainnet, in beta. Interfaces and mechanics may change. Size your use
accordingly.

### Your bounds are only as good as you set them

The contract enforces the number you give it. It has no opinion about whether
that number is sensible.

A bound set far from the market is protection you do not have. This matters most
where a strategy trades repeatedly - a loose bound is not one bad fill, it is a
bad fill available over and over. The strategy pages say what each bound does;
set them as the worst outcome you would genuinely accept.

### Anything that gets your signature can do what your signature permits

The contract cannot distinguish an owner-signed transaction you meant from an
owner-signed transaction you were tricked into. A compromised front end cannot
withdraw your funds directly - but it can present you with a rule whose bounds
are far too loose, and that rule will then be enforced exactly as written.

Check what you are signing, particularly price bounds. Use the published app.
Nobody from LiquiHog will ever ask for a private key or seed phrase.

### A hostile keeper can still cost you something

The bounds cap what a misbehaving keeper can extract; they do not reduce it to
zero. The gap between the price you accepted and the best available price is the
exposure, and it is yours to control by how tightly you set the bound.

This is the reason bounds are mandatory on every strategy type, and the reason a
balancer's expire.

### Availability is not guaranteed

If the keeper stops, your rules stop being triggered. Your funds remain fully
under your control - you can withdraw, cancel and close throughout - but the
automation is a service, and it can be interrupted.

There is no compensation for a rule that did not fire.

### Market risk is entirely yours

These are trading strategies. A grid loses in a trend. A balancer sells strength
in a bull market. A schedule keeps buying an asset that keeps falling. All of
that works exactly as documented while losing you money.

## Reporting something

If you believe you have found a security issue, please contact us privately
first, rather than opening a public issue:

- **X (Twitter)**: [@LiquiHog](https://x.com/LiquiHog)
- **Email**: [LiquiHog@gmail.com](mailto:LiquiHog@gmail.com)

Include what you observed and how to reproduce it. Never include a private key or
seed phrase - a transaction id is enough for us to investigate.

For documentation errors and behaviour that does not match what is written here,
a public issue is fine and is genuinely useful.
