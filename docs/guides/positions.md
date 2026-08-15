# Positions: liquidity and staked assets

A passport can hold more than plain tokens. It can hold a liquidity-pool share
or a liquid-staking token, know roughly what that holding is worth, and let a
balancer count it toward a target **without selling it**.

That last part is the point. A quarter of your portfolio sitting in an LP
position is still a quarter of your portfolio; a strategy that cannot see it
will keep buying more of what you already hold.

## What a position is

Two separate things happen when you make a holding into a position.

**Reserving it** puts the holding out of reach of both withdrawal and your
strategies, so nothing spends it by accident. This creates the position record.

**Describing it** attaches the metadata that says what the holding represents -
which assets back it, and at what rate. A balancer reads that description to
value the holding.

You can do the first without the second. A reserved holding with no description
is just protected; it is worth its face value and nothing more. That is how the
ALGO gas reserve and a protected HOG balance work - see
[Custody and control](../core/custody.md).

## Liquidity positions

### Getting into one from inside your passport

Your passport can enter a STAMM liquidity position directly, using the same
owner-driven swap described in
[Funding and withdrawing](funding-and-withdrawing.md). You spend one asset and
receive LP units - the routing handles splitting and depositing on the way
through.

**Opt into the LP token first.** An Algorand account cannot receive an asset it
has not opted into, and the swap does not do it for you. Skip this and the whole
trade is rejected when the LP units try to arrive.

This works from a single asset. It does **not** work from two at once: the
contract permits exactly one asset to leave your passport per trade, so a
two-sided deposit is refused. Route from one asset and let it do the splitting.

### Depositing LP you already hold

Send it in like any other token - opt in, then transfer. Nothing special is
needed to *hold* it.

### Describing it, so a balancer can use it

Whichever way the LP arrived, your passport does not yet know what it is. It is
a token with a balance. To make it count toward an allocation you reserve it and
then describe it: which two assets the pool holds, and how much of each one unit
represents.

**The description is yours, and nothing on chain checks it.** For a liquidity
position the contract cannot read the pool, so it takes the figures you provide.
It uses them conservatively - a live check may only lower a value, never raise
it - but the numbers originate with you.

Understating a position has a real cost. A balancer reading the holding as worth
less than it is will treat the allocation as underweight and buy more of the
other side than you intended. Take the figures from the pool at the time you
describe it, not from memory.

The arithmetic here describes **constant-product pools**. Stableswap and
concentrated-liquidity pools use different curves, and a share of one cannot be
described this way correctly.

### Getting out

The reverse of getting in: redeem the LP units back to a single asset through
the same owner-driven swap. Release the reservation first, or there is nothing
free to spend.

## Staked assets

Algorand has liquid staking tokens that pay their yield in a **second asset**.
You stake ALGO, hold a token representing it, and accrue a paired token
alongside - hogALGO paying HOG, folksALGO paying FOLKS, and others.

A passport can hold these and describe them the same way, and the description
has room for exactly this shape: **two legs**, one for the ALGO principal and
one for the accrued yield. That is not a coincidence of layout - it is what a
dual-staking token needs, and it means a balancer can count the principal toward
an ALGO target and the accrued yield toward a target in the paired asset, while
the token itself stays staked.

The practical effect is that yield satisfies part of an allocation instead of
having to be bought. A rule holding a target in the paired asset finds itself
closer to that target over time without trading.

**Status, plainly.** The contract carries a path for verifying a staked-asset
valuation on chain, against live figures rather than your description. **That
path is not available yet** - it needs a published price oracle, and none is
published today. Until one is, a staked-asset position is valued exactly like a
liquidity position: from your description, with no on-chain check.

So this works, and it is useful, but it carries the same trust position as an LP
holding rather than the stronger one the design allows for. Treat the two the
same way when deciding how much to rely on either.

## A description is not a balance

This is the one thing to be sure of before relying on any of the above.

Describing a position tells **a balancer** what a holding is worth, so it can
decide whether that holding sits at its target. It does not change what your
passport actually holds. Nothing else in the system reads a description, and
none of it becomes spendable.

**hogALGO is where this catches people**, because the name suggests otherwise.
Holding it does not mean holding HOG, and it does not mean holding ALGO:

| | |
|---|---|
| The routing-fee discount | Counted on your passport's real **HOG** balance. hogALGO does not count toward the 100 |
| Your gas reserve | Paid from real **ALGO** only. A staked token cannot fund automation |
| What a strategy can spend | Real balances only. A described holding is never sold to fill a rule |
| What you can withdraw | Real balances. You withdraw hogALGO as hogALGO |

The same applies to a liquidity position. Describing LP units as backed by two
assets does not give you a balance in either one - it tells a balancer what the
units are worth, and nothing more.

So: **if you want the fee discount, hold HOG. If you want automation to keep
running, hold ALGO.** A description is an accounting convenience for one
strategy's arithmetic, not a substitute for holding the thing.

What it *does* buy you is real, and worth restating: a balancer can see the
value and stop buying more of something you already hold in another form.

## What this costs

Each position record and each asset your passport holds occupies some ALGO as
minimum balance. It is held rather than spent, and released when you clear the
position or close the passport. See
[Fees and costs](../reference/fees-and-costs.md).

Entering or exiting a position through a swap pays the routing fee on the
output, and no keeper fee - no keeper did the work.

## Limits worth knowing

| | |
|---|---|
| One asset per trade | Entering or leaving a position moves a single asset each way. Two-sided deposits are refused |
| Liquidity minting is STAMM-only | Other venues' LP tokens can be **held and described**, but not minted or redeemed through the passport |
| Descriptions are not verified | For both liquidity and staked positions, the valuation is yours. Nothing on chain confirms it |
| Reserve before describing | The description attaches to a position record, and reserving is what creates one |
| Constant-product only | The share arithmetic does not describe stableswap or concentrated-liquidity pools |

## Where this is used

Only the [balancer](../strategies/balancer.md) reads position descriptions. The
other three strategies trade plain balances and ignore them.

If you are not running a balancer, reserving a holding is still useful on its
own - it is how you stop a strategy spending something you meant to keep.
