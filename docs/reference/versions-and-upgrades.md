# Versions and upgrades

Your passport runs a specific published version of the contract. This page covers
what a version change means for you and, more importantly, what it cannot do to
you without your consent.

## Nobody can upgrade your passport but you

This is the part worth reading even if you skip the rest.

Applying an update is an owner-signed action, exactly like a withdrawal. We
cannot push a new version to your passport. Neither can the keeper. There is no
mechanism that updates it on a schedule, on our say-so, or as a condition of
anything.

That is not a policy we are choosing to follow - it is how the contract works. A
published version sits available. Your passport keeps running what it is running
until you decide otherwise.

## What the version numbers mean

Versions are `major.minor.patch`, and each part says what it *forces*, not how
big the change is.

| Part | Means | For you |
|---|---|---|
| **Patch** | Behaviour and guard fixes | Applies in place. Nothing outside your passport notices |
| **Minor** | The interface changed | Applies in place, and is safe for your funds. Tools built against the old interface need updating |
| **Major** | How records are stored changed | **Cannot** be applied in place. Needs a new passport and a deliberate move |

A major bump is refused as an in-place update by your passport itself, rather
than being merely discouraged. New code reading records written under an old
layout is how balances get stranded, so the contract will not do it - it makes
you create a new passport and move funds across deliberately.

## Live versions

Every version below is approved on mainnet, and an approved version is never
withdrawn, so each stays installable. Measured from the deployed programs:

| Version | Tier | Size | Strategies | What it adds |
|---|---|---|---|---|
| v1.0.0 | Public | 6,790 B | Scheduled buys, limit orders | The first public build |
| v1.0.1 | Public | 6,895 B | Scheduled buys, limit orders | Gas cap: you can cap the rate the keeper may charge for gas |
| **v1.0.2** | **Public (current)** | 6,911 B | + grid, balancer | Opens grids and balancers on the public build. Nothing else changes |
| v1.1.0 | Beta | 6,806 B | Scheduled buys, limit orders, grid, balancer | The beta line before the gas cap |
| v1.1.1 | Beta | 6,911 B | Same as v1.1.0 | Gas cap. The same program as v1.0.2 |
| **v1.1.2** | **Beta (current)** | 10,588 B | + lending (Folks Finance), payments | Proceeds routing, and gas paid in another asset |

The registry hands out **v1.0.2** on the public tier and **v1.1.2** on beta.
v1.0.2 and v1.1.1 are byte-identical: the same program approved under two
version numbers.

All of them are in the same major line, so moving between them - including from
the public build to the beta build - is an **in-place update**. Your passport
keeps its address, its funds, its running strategies and its history. Nothing
migrates.

See [Tiers](../core/tiers.md).

## Downgrades are refused

Your passport will not accept a version older than the one it is running, even a
legitimate published one.

This matters more than it first appears. Entitlement can move downward -
if an address stops being on the beta allowlist, for instance, what it is
entitled to install drops back to the public version. Without the block, a
passport running the newer build could be walked backward onto older code. It
cannot.

## What a version change can and cannot alter

**Can:** the strategy types available, the guards the contract enforces, the
behaviour of a fill, the interface tools use.

**Cannot, on your passport, without your signature:** anything at all.

There is one nuance about fees that this page should not gloss over. The keeper
fee rate is published by the registry, and a strategy captures the rate at the
moment you create it. A published change to that rate reaches strategies you
create afterwards - it does not require a version update, and it does not touch
strategies already running. See [Fees and costs](fees-and-costs.md).

So "nobody can change your passport" is exactly true of your passport's code and
your running strategies. It is not a claim that the published fee rate can never
change.

## Should you upgrade?

**A patch** - generally yes. Patches are guard and behaviour fixes, and the
contract holding your funds is better with them than without.

**A minor** - yes if you want what it adds, and check that whatever you use to
drive your passport supports it first.

**A major** - read what it says before deciding. It requires creating a new
passport and moving funds, which is real work, and the release notes should tell
you why it is worth it.

**Never** is also a legitimate choice, and one nobody can override. Your passport
keeps working on the version it has.

## Checking what you are on

Your passport records the version it is running in its own on-chain state, and
the app shows it. Both the version it is on and the version it is entitled to are
readable before you decide anything.

The registry is the authority on which versions exist and which one your address
may install, so that answer always comes from chain rather than from a page that
could go stale. New releases are announced on
[X](https://x.com/LiquiHog) and in the
[Discord](https://discord.gg/a9R4vkXd98).
