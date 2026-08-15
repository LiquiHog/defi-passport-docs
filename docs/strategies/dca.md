# Scheduled buys

**Public tier.** Trade a fixed amount, on a fixed interval, until the funds you
committed run out.

This is dollar-cost averaging, and it works in either direction - a schedule that
spends USDC to accumulate ALGO and a schedule that sells ALGO for USDC are the
same strategy with the assets the other way round.

## Why you would want it

Timing entry is hard and doing it by hand is worse: it requires you to be
present, and it invites you to change your mind at exactly the wrong moment. A
schedule takes the decision once and then executes it whether or not you are
watching.

The trade-off is honest. Averaging in gives up the best possible entry in
exchange for not getting the worst one. It is a strategy about consistency, not
about being right.

## What you configure

| Setting | What it means |
|---|---|
| **Pair** | What you are spending and what you are buying |
| **Total committed** | The full amount you want spent over the life of the schedule. Reserved up front |
| **Batch size** | How much to spend on each execution |
| **Interval** | Minimum time between executions. The floor is one minute |
| **Minimum output** | The least you will accept from one execution. **Required** |
| **Slippage tolerance** | Optional. An additional tightness constraint on a fill |
| **Cap on total received** | Optional. Stop buying once you have accumulated this much |

Total committed divided by batch size is roughly how many executions the schedule
will run for.

## The minimum output is mandatory

You cannot create a schedule without one, and this is the one place the contract
is deliberately more demanding than it strictly needs to be.

A schedule is the only strategy type that could otherwise run with no price
protection at all. A limit order is defined by its target price, a grid cell by
the quote it must return, a balancer by its bounds - each has protection built
into what the strategy *is*. A schedule does not. Without a floor it would buy
at any price, which over a long-running rule is the most expensive mistake
available.

So the floor is required. Set it as the least you would be content to receive
for one batch, not as a prediction of the price. If the market moves past it, the
schedule stops filling rather than filling badly, and you re-price it when you
choose.

## What it costs to run

Each execution pays the two fees on its output - HOGSWAP's routing fee and the
keeper's 0.05%. See [Fees and costs](../reference/fees-and-costs.md).

The keeper also reclaims the exact cost of each trigger from your gas reserve. A
schedule running every few hours for months is the strategy most likely to empty
that reserve, so check it periodically. If it empties, the schedule pauses and
resumes when you top it up - nothing is lost.

## Limitations

**It does not react to price.** Beyond refusing a fill below your floor, a
schedule does not buy more when the price is good or less when it is not. If you
want price-responsive behaviour, that is a [grid](grid.md) or a
[limit order](limit-orders.md).

**Your floor drifts against the market.** A floor set from today's price becomes
either unreachable or meaninglessly loose after a large move. Revisit it. Editing
a running schedule's floor does not disturb its interval or what it has already
spent.

**It stops when the committed funds run out.** It does not top itself up. You
can add funds to a running schedule at any time.

## Cancelling

Remove the rule or close the strategy. Whatever remains committed returns to free
balance immediately, and anything already bought was never reserved in the first
place - it has been sitting in your free balance all along, withdrawable at any
point.
