# Closing your passport

Unwinding completely and getting everything back, including the ALGO Algorand has
been holding as minimum balance.

Closing is always available. It is not gated on a version, a tier, a fee, or our
agreement, and it cannot be. A contract that could be prevented from closing
would be one whose minimum balance nobody ever recovers.

## The order

1. **Close every strategy.** This releases everything the strategies and their
   rules were holding back into free balance.
2. **Release every set-aside holding**, including your gas reserve and any HOG
   you set aside.
3. **Check nothing is still reserved.** Free balance should now equal the whole
   of what your passport holds. If it does not, something is still committed -
   find it before continuing.
4. **Withdraw every asset**, then withdraw the free ALGO.
5. **Delete the contract.** This closes out any remaining holdings to you and
   releases the minimum balance the contract itself occupied.
6. **De-register.** A separate step - see below.

The app does all six as one flow. If you are driving the contracts directly, do
them in this order: deletion refuses to proceed while records remain, which is
fail-safe but means you have to unwind first rather than discovering it at the
end.

## Do not skip de-registration

This is the step that costs money to miss.

Deleting your passport removes the contract. It does not remove the registration
entry the registry is holding for it - and that entry was paid for out of *your*
ALGO when you created the passport. De-registering releases it back to you.

Skip it and that ALGO stays with the registry, associated with a contract that no
longer exists. Nothing else breaks, but you do not get it back without going and
doing this step.

Treat delete-and-de-register as one action, the same way create-and-register is
one action.

## Before you delete: opt in first

Deletion returns your remaining assets to your own address, and an Algorand
address cannot receive an asset it has not opted into.

If your passport holds an asset your wallet is not opted into, opt in first.
Otherwise the deletion is rejected - safely, but you will have to work out why.

## What you get back

| | |
|---|---|
| Your assets and ALGO | All of it - there is no exit fee and no lock-up |
| Minimum balance for the contract | Released on deletion |
| Minimum balance for each asset holding | Released as each holding closes |
| Minimum balance for each rule | Released as each rule is removed |
| Minimum balance for your registration | Released on de-registration |

Fees you already paid on trades that already happened are not returned. Nothing
else is withheld.

## Partial alternatives

Closing the passport is not the only way to stop.

**To stop trading but keep the passport**, close your strategies. Everything
returns to free balance, the automation has nothing left to trigger, and you can
withdraw whatever you like. The passport sits idle, costing only its minimum
balance. Starting again later means creating a new strategy, not a new passport.

**To stop one strategy**, close that strategy. The others carry on.

**To pause everything without cancelling**, empty your gas reserve. Rules stay
configured and funded but stop being triggered, and they resume when you top it
up. This is a blunt instrument - it stops everything at once - but it is
reversible and immediate.

## Starting again

If you delete a passport and later want another, create a new one. Your address
is free to do so once the previous registration has been cleared, which
de-registration does.

Nothing carries over: a new passport is a new contract with a new address, and
you fund and configure it fresh.
