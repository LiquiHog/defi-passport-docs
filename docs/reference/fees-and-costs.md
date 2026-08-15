# Fees and costs

Two fees, both charged on the **output** of a trade, plus some ALGO your passport
has to hold. This page covers all of it.

## The two fees

| Fee | Charged on | Rate |
|---|---|---|
| **HOGSWAP routing fee** | The output of every swap | 0.05%, reduced by the HOG your passport holds, reaching **zero at 100 HOG** |
| **Keeper fee** | The output of every automated fill | 0.05%, flat |

An automated fill therefore costs up to **0.10%** of what it produces, falling to
**0.05%** once your passport holds 100 HOG.

A swap you trigger yourself pays no keeper fee - no keeper did any work - so it
costs only the routing fee, and nothing at all at 100 HOG.

Both are taken out of the asset you receive. Nothing is charged on deposits,
withdrawals, creating a passport, creating a rule, or cancelling anything.

## The HOG discount

The routing fee falls **linearly** with the HOG your passport holds. Every micro
unit counts: the discount is not tiered and there is no threshold to cross before
it begins.

| HOG held by your passport | Routing fee |
|---|---|
| 0 | 0.05% |
| 25 | 0.0375% |
| 50 | 0.025% |
| 75 | 0.0125% |
| 100 or more | 0 |

Four things to be precise about:

**The HOG must be held by your passport, not by your wallet.** The discount is
calculated on the balance of the account doing the trading, which is your
passport. HOG sitting in the wallet that owns the passport does nothing for it.

**It takes effect immediately.** Send HOG to your passport and the next fill is
cheaper. There is nothing to claim, register, or wait for.

**Setting HOG aside protects the discount; it does not create it.** You can set
your HOG aside so that neither you nor a strategy can accidentally trade or
withdraw it - which would silently cost you the discount. But the discount itself
is calculated on your passport's **total** HOG holding, not on the amount set
aside. Setting aside is a safety measure, not a requirement.

**It counts HOG, not tokens that accrue HOG.** hogALGO is a staked-ALGO token
that pays its yield in HOG; it is a different asset, and holding it does nothing
for this discount. The same goes for a liquidity position holding HOG on one
side. If you describe such a holding so a balancer can value it, that
description is arithmetic for the balancer alone - it does not become a HOG
balance. See [Positions](../guides/positions.md).

## Worked example

A fill that produces 1,000 USDC:

| Your passport holds | Routing fee | Keeper fee | Total |
|---|---|---|---|
| 0 HOG | 0.50 USDC | 0.50 USDC | **1.00 USDC** |
| 50 HOG | 0.25 USDC | 0.50 USDC | **0.75 USDC** |
| 100 HOG | 0 | 0.50 USDC | **0.50 USDC** |

The same fill, if you triggered it yourself rather than a rule firing:

| Your passport holds | Total |
|---|---|
| 0 HOG | **0.50 USDC** |
| 100 HOG | **nothing** |

## When the keeper fee rate is fixed

**Each strategy captures the keeper fee rate in force at the moment you create
it, and keeps that rate for its life.** A change to the published rate never
touches a strategy that is already running.

It reaches strategies you create *afterwards*. The rate is published by the
registry and read when a strategy is created, so a change applies from that point
on, without requiring anything from you and without a version update.

Two practical consequences:

- A long-running strategy's cost is settled when you create it. You do not need
  to monitor it.
- If the published rate changes and you want your existing strategies on the new
  rate, close and recreate them. Nothing does this automatically.

The routing fee behaves differently: it is calculated per swap, against your HOG
holding at that moment, so it responds immediately to HOG moving in or out.

## What the ALGO is for

Separately from fees, your passport has to hold some ALGO. There are two
different things going on and they are worth separating.

### Minimum balance - held, not spent

Algorand requires every account to hold a minimum balance, and that requirement
rises with what the account is storing. Your passport pays it for:

- the contract itself
- each asset it is opted into
- each rule and each position record it stores

**This ALGO is not a fee and is not spent.** It is held by the protocol while the
thing exists and released when the thing goes away - when you remove a rule, opt
out of an asset, or close the passport. Closing a passport completely returns all
of it.

It does mean a passport with many rules and many assets has more ALGO tied up
than a simple one. Budget for it, and remember it is recoverable.

The app shows what an action will cost before you sign it. If you are driving the
contracts directly, the SDK exposes the same figures.

### The gas reserve - actually spent

Triggering a rule costs the keeper an Algorand transaction fee, and it reclaims
that from ALGO you have set aside for the purpose.

**It reclaims exactly what the trigger cost.** Not a flat allowance, not an
estimate - the actual fee paid on that transaction. There is a ceiling on what a
single trigger may reclaim, so a keeper that overpaid its own fees, whether by
misconfiguration or deliberately, cannot drain your reserve.

The reserve is the only ALGO automation can touch. It cannot reach your free
balance or funds a rule is holding.

**If it empties, your rules stop being triggered.** They do not fail and nothing
is lost - they resume when you top it up. This is the most common reason a
strategy quietly stops working, so check it periodically. Strategies that fire
often - a grid especially - need more.

## Fee-aware strategy design

For strategies that trade repeatedly, the fees are part of the arithmetic rather
than a rounding error.

**Grid cells** must clear both fees on a completed cycle before they earn
anything. A cell whose buy and sell prices are spaced more tightly than its costs
runs busily and loses money. See [Grid](../strategies/grid.md).

**A balancer's band** should be wide enough that a rebalance is worth the fees it
pays. A minimum trade size is enforced so the strategy cannot churn on dust, but
that floor is a backstop, not a substitute for setting a sensible band. See
[Balancer](../strategies/balancer.md).

**Scheduled buys and limit orders** fill rarely enough that fees are usually not
the deciding factor - though a schedule with a very small batch size pays them on
every execution.

Holding 100 HOG halves the cost of every automated fill, which changes the
arithmetic on all of the above.
