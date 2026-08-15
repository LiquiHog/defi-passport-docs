# Automation

The keeper is the off-chain service that makes your rules actually happen. This
page covers what it does, what it is paid, and - more usefully - what it cannot
do and what happens when it stops.

## What it is

A service run by LiquiHog that watches registered passports for rules that have
come due, works out a route through [HOGSWAP](architecture.md#hogswap), and
submits a trigger transaction to the passport.

It is untrusted by design. Your passport does not accept its word for anything:
it executes the swap, measures what actually left and what actually arrived, and
checks that against the bound on your rule. Fall short and the whole transaction
is rejected.

## What it can and cannot do

| | |
|---|---|
| Trigger a rule you created, when that rule's own terms are met | Yes |
| Withdraw or transfer your funds anywhere | No |
| Create a rule, or edit one of yours | No |
| Opt your passport out of an asset | No |
| Upgrade, downgrade, or delete your passport | No |
| Execute a fill worse than your rule's bound | No - refused on-chain |
| Trigger a rule on a passport that is not registered | No |

Your passport also checks that the trigger came from the keeper the registry
publishes. A stranger cannot trigger your rules, and neither can another user.

## What it is paid

Two things, and they are separate.

**A fee of 0.05% on the output of the fill.** This is the keeper's compensation
and it is charged only on automated fills. A swap you trigger yourself pays no
keeper fee, because no keeper did any work.

**Its exact transaction cost, reclaimed from your gas reserve.** Triggering costs
the keeper an Algorand transaction fee, and it reclaims precisely that amount -
what the transaction actually cost, not a flat allowance and not an estimate.
There is a ceiling on what a single trigger may reclaim, so a misconfigured or
hostile keeper cannot drain your reserve by inflating its own fees.

Both are covered in [Fees and costs](../reference/fees-and-costs.md), alongside
HOGSWAP's routing fee.

## Your gas reserve

Automation is paid for out of ALGO you set aside for the purpose. Nothing else is
touched: the keeper cannot reclaim costs from your free balance or from funds a
rule is holding.

**If the reserve empties, your rules stop being triggered.** They do not fail,
they do not fill badly, and nothing is lost - they simply do not run until you
top it up. Everything a rule was holding stays exactly where it is and remains
yours to withdraw or release.

Size it against how often you expect your rules to fire. A limit order that may
fill once needs very little. A grid or a schedule running for months needs
topping up periodically.

## When things go wrong

**The keeper is offline.** Nothing happens. Rules that come due are not
triggered, and they are triggered when it returns. Your funds sit where they
are, and you retain full access throughout - you can still withdraw free
balance, cancel rules to release reserved funds, or close the passport
entirely. Automation stopping never blocks you.

**The keeper is not opted into an asset your rule is buying.** Your fill still
happens. The keeper forgoes its 0.05% on that swap rather than the trade
failing. An operational gap on our side is never allowed to become your problem;
the incentive to fix it sits with us, where it belongs.

**The keeper misbehaves.** The bound on your rule is what stops it, and it is
checked on-chain against measured balances. The most a hostile keeper can
extract is the difference between the price you accepted and the price it could
have got - which is why the bounds are mandatory on every strategy type, and why
the balancer's expire and have to be renewed. Set them tightly and that gap is
small. Set them loosely and it is not.

**You stop trusting it.** Cancel your rules. Reserved funds return to free
balance and there is nothing left for the keeper to trigger. You do not need our
cooperation, and there is no allowance to revoke because there was never one to
grant.

## What is not documented here

How the keeper decides what to execute, in what order, at what size, or how it
retries - none of that is published. It is operational detail that changes, and
publishing it would invite gaming without telling you anything you need in order
to evaluate the product.

What matters for that evaluation is on this page: the keeper cannot move your
funds, cannot exceed your bounds, and cannot do anything if you cancel your
rules. Those properties hold regardless of how it is tuned.
