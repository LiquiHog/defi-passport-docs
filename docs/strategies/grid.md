# Grid

**Beta tier.** A set of price cells that buy low, sell high, and re-arm
themselves after every fill. No oracle is involved.

## Why you would want it

A grid earns from oscillation. In a pair that moves up and down within a range
without going anywhere in particular, a grid keeps buying the dips and selling
the rallies, and each completed cycle banks the difference.

It is the strategy that most rewards a correct view about *volatility* rather
than about direction - which is a genuinely different thing to have an opinion
about, and one that a limit order or a schedule cannot express.

## How a cell works

Each cell holds one side at a time.

- A cell armed to **buy** is holding quote (say USDC) and waiting to spend it.
- When it fills, it now holds base (say ALGO) and flips to **sell**.
- When that sells, it is holding quote again and flips back to **buy**.

The flip is automatic and needs nothing from you. A grid is a set of these cells
at different price points, so as the market moves through the range, different
cells fire.

The contract does not consult a price feed to decide any of this. A cell fills
when a trade satisfying its terms is possible, and its terms are the amounts you
set - which is what makes a grid work with no oracle and no trust in an external
price.

## What you configure

Per cell:

| Setting | What it means |
|---|---|
| **Pair** | Base and quote |
| **Starting side** | Whether this cell begins armed to buy or armed to sell |
| **Quote to spend** | How much quote the buy side spends |
| **Base to acquire** | How much base that buy must produce - this sets the buy price |
| **Quote to return** | How much quote the sell side must produce - this sets the sell price |

The gap between "quote to spend" and "quote to return" is the cell's profit per
completed cycle. Everything else about a grid follows from choosing those three
numbers across a set of cells.

Fund each cell on the side it starts armed for: a buy-first cell needs quote, a
sell-first cell needs base.

## A cell must earn more than it re-commits

The contract enforces this and will refuse a cell that fails it.

When a cell sells, it re-commits quote for its next buy. If it re-commits more
than the sell actually produced, the cell quietly converts free balance into
reserved balance on every cycle - and loses money each time round regardless. It
looks like it is working. It is not.

So the quote a cell returns on its sell must be at least the quote it spends on
its buy. In practice you will want a real margin between them, not the bare
minimum: that margin is the entire point of the cell, and it has to cover both
fees as well.

## What it costs to run

Every fill - each buy and each sell - pays both fees on its output, and the
keeper reclaims the exact trigger cost from your gas reserve. A grid fills more
often than any other strategy type, so it is the one where fees and gas matter
most to the arithmetic.

When you space your cells, size the gap so a completed cycle clears both fees
with room left over. A grid whose cells are spaced tighter than its costs will
run busily and lose money. See [Fees and costs](../reference/fees-and-costs.md).

## Limitations

**Trends hurt it.** This is the honest weakness. If the price leaves your range
upward, your cells have sold into the move and you are holding quote while the
asset runs away. If it leaves downward, your cells have bought all the way down
and you are holding the asset at a loss. A grid converts a directional move into
the worst side of it. Only use one where you genuinely expect range-bound
behaviour, and size it as a position you are willing to hold.

**It needs maintenance.** Cells do not re-space themselves. When the market moves
out of your range, the grid stops doing useful work until you re-price it.

**Every cell holds funds.** A grid with many cells reserves a lot of your balance
and occupies minimum balance for each rule. That is recoverable, but it is
capital committed.

## Cancelling

Remove individual cells to shrink the grid, or close the strategy to unwind all
of it. Everything reserved returns to free balance immediately, in whatever mix
of base and quote the cells happened to be holding at the time.
