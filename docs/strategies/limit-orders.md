# Limit orders

**Public tier.** Trade a fixed amount, but only at or better than a price you
set. If the market never reaches it, nothing happens.

## Why you would want it

The simplest thing an automated passport can do for you, and often the most
useful: it lets you name a price and stop watching. The order sits until the
market comes to it, and it never fills worse than what you asked for.

Unlike a limit order on a centralised exchange, the funds stay in a contract you
own. Nobody is holding them on your behalf while the order waits, and you can
cancel and take them back at any moment.

## What you configure

| Setting | What it means |
|---|---|
| **Pair** | What you are spending and what you are receiving |
| **Amount in** | How much you are spending. Reserved when you create the order |
| **Target output** | The least you will accept in total for that amount. This is your price |
| **Partial fills** | Optional. Off by default - see below |
| **Minimum fill size** | Only with partial fills on. The smallest slice you will accept |
| **Expiry** | Optional. After this time the order stops filling |

Your price is expressed as a total output rather than a rate, which is the same
information stated in the form the contract actually checks: it measures what
arrived and compares it to this number.

## Fill-or-nothing, by default

By default a limit order fills completely or not at all. This is the safer
default and it is what most people mean by a limit order.

Turning on partial fills lets the order be filled in slices, which can get you
executed when there is not enough liquidity to take the whole order at your price
at once. If you do, set a minimum fill size - otherwise the order can be filled
in slices small enough that the fees and the effort are disproportionate to what
you receive.

When partial fills are on, each slice is still held to your price on a pro-rata
basis. A partial fill cannot be executed at a worse rate than the whole one would
have been.

## Expiry

An expiry is optional. If you set one, the order stops filling after that time.

**Expiry does not release your funds.** The order stops being fillable, but what
it was holding stays reserved until you remove the rule or close the strategy.
This is deliberate - an expiry that automatically returned funds would be a
second thing happening to your balance without your signature. If you want the
funds back, take them back.

## What it costs to run

Each fill pays the two fees on its output, and the keeper reclaims the exact cost
of the trigger from your gas reserve. An order that fills once costs very little
to run. See [Fees and costs](../reference/fees-and-costs.md).

An order that never fills costs nothing beyond the minimum balance its record
occupies, which is returned when you remove it.

## Limitations

**It is one trade, not a strategy.** It does not repeat, and it does not re-arm
after filling. If you want repeated behaviour, use [scheduled buys](dca.md) or a
[grid](grid.md).

**It fills at your price, not the best price.** If the market gaps well past your
target, the order fills at or better than what you asked for - but what you asked
for is what bounds it. Setting a target far from the market is not free
optionality; it is an instruction you may regret honouring.

**A resting order's funds are reserved.** They are not earning anything and you
cannot spend them elsewhere while the order is live. Cancel it if you want them
back.

## When it completes

An order that fills completely cleans itself up. The rule is removed and
anything it was still holding is released back to free balance, with nothing for
you to do. What it bought was never reserved in the first place - it has been in
your free balance since the moment it arrived, withdrawable at any point.

This happens only on a full fill. A partially filled order, or one that has
expired, stays put and keeps holding its funds until you remove it.

## Cancelling

Remove the rule or close the strategy. Reserved funds return to free balance
immediately. A partially filled order returns whatever has not been spent, and
whatever was already bought is in your free balance and always was.
