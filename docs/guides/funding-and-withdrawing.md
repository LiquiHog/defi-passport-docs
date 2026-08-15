# Funding and withdrawing

Moving value into and out of your passport. Both directions are ordinary
Algorand operations with one thing worth understanding on each side.

## Depositing

**A deposit is a plain transfer.** Send ALGO or an asset to your passport's
address. There is no approval to grant, no allowance to set, and nothing to
revoke afterwards.

That simplicity has one consequence: because a deposit is just a transfer,
nothing on chain records that it *was* a deposit rather than any other payment.
The app attaches a note so your history stays readable. If you are transferring
directly, consider doing the same - it costs nothing and it is the difference
between a legible history and a list of anonymous payments.

### ALGO first

Your passport needs ALGO before it can hold anything else. Algorand charges every
account a minimum balance, and that requirement rises with each asset held and
each rule stored. Those charges come out of your passport's ALGO.

Fund it with ALGO before opting into assets, or the opt-in fails.

### Assets need an opt-in first

An Algorand account cannot receive an asset it has not opted into. Opt your
passport in, then send the asset. Doing it the other way round means the transfer
is rejected.

Opt into every asset your strategies will touch, including ones they will
*acquire*. A schedule buying an asset your passport has never held needs the
opt-in in place before its first execution.

Each opt-in raises your passport's minimum balance. That ALGO is not spent - it
comes back when you opt out or close the passport.

## Withdrawing

**You can withdraw your free balance.** Free balance is what is left after
everything currently reserved: funds held by rules, quote reserves held by
strategies, and anything you have set aside.

![Free, reserved, and set-aside balance](../assets/free-vs-committed.svg)

This is why a passport can show a balance you cannot fully withdraw. Nothing is
stuck - something is holding it, and you can always release it:

| What is holding it | How to release it |
|---|---|
| A rule's committed funds | Move funds out of the rule, or remove the rule |
| A strategy's shared reserve | Reduce the reserve, or close the strategy |
| Your gas reserve | Un-set-aside the ALGO |
| Set-aside HOG | Un-set-aside it |

Releasing is always available to you and needs nobody's cooperation.

### ALGO is a special case

Your passport's free ALGO is what remains after its minimum balance as well as
after anything reserved. A passport that has been fully drained sits exactly at
its minimum balance and reads as zero free ALGO - which is correct, not a fault.
That minimum-balance ALGO is released when you close the passport.

Size a withdrawal against free balance rather than against the balance you can
see. The app does this for you.

## Opting out of an asset

Opting out closes the asset holding, returns any remaining units to you, and
releases the minimum balance the holding occupied.

Two conditions:

- **None of that asset may be reserved.** Remove or unwind whatever is holding it
  first.
- **ALGO cannot be opted out of.** It is not an opted-in asset. Withdraw it
  instead, and close the passport when you want the last of it back.

## Swapping directly, without automation

Your passport can also make a swap because *you* asked it to, rather than because
a rule came due. It uses the same routing and the same on-chain check that what
arrived meets the minimum you declared.

Two differences from an automated fill:

- **No keeper fee.** No keeper did any work, so none is charged. You pay only
  HOGSWAP's routing fee - and nothing at all if your passport holds 100 HOG.
- **It spends free balance.** It cannot touch funds a rule is holding.

The contract checks the result the same way it checks a keeper's: it measures
what left and what arrived and refuses anything below the minimum you declared.
A swap you asked for is bounded by your own number, not trusted because you
asked for it.

## What this costs

Deposits and withdrawals themselves carry no DeFi Passport fee. You pay the
Algorand network transaction fee, as with any transaction.

Fees apply to trades, not to moving your own funds in and out. See
[Fees and costs](../reference/fees-and-costs.md).
