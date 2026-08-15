# Balancer

**Beta tier.** Hold a set of assets at target values, and trade back toward those
targets when a holding drifts too far from where you wanted it.

## Why you would want it

If you want a fixed allocation - a third ALGO, a third USDC, a third something
else - keeping it there by hand means noticing the drift and acting on it. A
balancer does that continuously and mechanically.

Rebalancing also does something a fixed allocation does not: it sells strength
and buys weakness automatically. That is a deliberate strategy rather than a side
effect, and it is the reason to run one instead of simply holding.

## How it works

You give each asset a **target value** and a **band**. While the holding's value
stays inside the band, nothing happens. When it drifts outside, the balancer
trades back toward the target - selling if it has become overweight, buying if it
has become underweight.

Values are expressed in a quote asset you choose for the strategy, and the
strategy holds a shared reserve of that quote asset to buy with.

Each asset you are balancing is one rule inside the strategy, so you can add,
re-price, or remove one without disturbing the others.

## What you configure

Per asset:

| Setting | What it means |
|---|---|
| **Target value** | What this holding should be worth, in the quote asset |
| **Band** | How far it may drift before the balancer acts |
| **Minimum interval** | The least time between rebalances of this asset. The floor is one minute |
| **Buy ceiling** | The most you will pay per unit when buying. **Required** |
| **Sell floor** | The least you will accept per unit when selling. **Required** |
| **Bounds expiry** | How long you stand behind that ceiling and floor. **Required** |
| **Position value floor** | Optional. An absolute floor on the holding's value when selling |

## The bounds are mandatory, and they expire

This is the part of the balancer that needs the most attention, and it is worth
understanding rather than clicking past.

The buy ceiling and sell floor are the balancer's only real price protection.
Unlike a limit order, a balancer trades in both directions and does so
repeatedly, so it needs a bound on each side. The contract will not let you
create a rule without both.

**They also expire, and that is deliberate.** A ceiling set at slightly above
today's price is a sensible bound today. After a 30% fall it is a ceiling far
above the market, and nothing on chain notices that it has drifted - the contract
cannot tell a correct bound from a stale one without a price feed, which is
exactly the dependency a balancer is built to avoid.

What it *can* tell is whether a bound is old. So you declare how long you stand
behind your numbers, and past that point the rule **stops rebalancing** rather
than rebalancing at a bound you no longer mean. It fails closed, not badly.

Re-pricing renews it. Editing a live rule's bounds does not disturb its interval
or the funds it holds, so renewal is a small routine action rather than a rebuild.

**Budget for this.** A balancer is not a set-and-forget strategy. If you will not
revisit it, its bounds will lapse and it will quietly stop working - which is
safe, but is not what you wanted.

## Counting positions you already hold

The balancer is the only strategy that can value a holding which is not a plain
token. A liquidity-pool share or a liquid-staking token can be counted toward an
allocation **without being sold** - so a quarter of your portfolio sitting in an
LP position stops looking like a gap the balancer needs to fill.

A staked asset that pays its yield in a second token, like hogALGO paying HOG,
is described in two parts: the ALGO principal and the accrued yield. A rule can
count either. In practice that means yield satisfies part of an allocation
instead of having to be bought.

**The valuation is yours, not the contract's.** For both kinds of holding the
contract takes the description you provide and cannot verify it. It uses it
conservatively - a check may only lower a value, never raise it - but the number
originates with you, and understating one makes the balancer read the allocation
as underweight and buy more of the other side than you intended.

See [Positions](../guides/positions.md) for how to set one up, what it costs,
and the limits - including that the share arithmetic describes constant-product
pools only.

## What it costs to run

Each rebalance pays both fees on its output, and the keeper reclaims the exact
trigger cost from your gas reserve. See
[Fees and costs](../reference/fees-and-costs.md).

A minimum trade size applies: a rebalance has to move a meaningful amount of
value relative to the target, so the strategy cannot churn on dust.

Set your band wide enough that a rebalance is worth the fees it pays. A band
tighter than your costs produces frequent trades that each lose a little.

## Limitations

**Bounds need maintenance.** Covered above, and it is the main ongoing cost of
running one.

**It is slow by design.** The minimum interval and the band both exist to stop it
reacting to noise. It is not a trading strategy and will not catch a move.

**Rebalancing is not always right.** In a sustained trend, selling the asset that
is going up is the wrong trade, and a balancer will keep doing it. That is
inherent to fixed-weight allocation, not a fault in this implementation.

## Cancelling

Remove a rule to stop balancing one asset, or close the strategy to unwind all of
them. Everything reserved - including the shared quote reserve - returns to free
balance immediately.
