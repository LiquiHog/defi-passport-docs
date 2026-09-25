# FAQ

## Custody

**Can you take my funds?**

No. Your passport is a contract owned by your address, and every method that
moves value checks the caller against that owner. There is no administrative
override and no recovery method. We cannot withdraw from your passport, and
neither can the keeper.

**What happens if LiquiHog disappears?**

Your passport keeps holding your funds and you keep full control of them. You can
withdraw, cancel rules, and close the passport without our cooperation - none of
those depend on anything we run.

What stops is the automation. Rules would no longer be triggered. Nothing is lost
and nothing is stuck; you would unwind at your leisure. See
[Closing your passport](../guides/closing-your-passport.md).

**Do I have to approve a token allowance?**

No. Deposits are ordinary transfers to your passport's address. There is no
allowance to grant and therefore nothing to revoke.

**Why do I need my own contract instead of just depositing somewhere?**

Because a shared contract is a shared risk. In a pooled design your balance sits
alongside everyone else's, and a fault affecting the pool affects you. A passport
holds only your funds, and the only address that can move them is yours.

## Balances

**Why can't I withdraw my whole balance?**

Something is holding part of it. Funds a rule needs are reserved for it, and so
is anything you have set aside - your gas reserve, for instance. Withdrawals are
limited to free balance.

Nothing is stuck. Remove the rule, close the strategy, or release the set-aside
holding, and it returns to free balance immediately. See
[Custody and control](../core/custody.md).

**My passport shows a balance but zero free ALGO.**

That is a passport sitting at its Algorand minimum balance. The protocol holds
that amount while the contract, its assets and its rules exist, and releases it
when they go away. Closing the passport returns all of it.

**Why did opting into an asset fail?**

Almost always because the passport has no free ALGO. Each opt-in raises its
minimum balance requirement, and that comes out of the passport's own ALGO. Fund
it with ALGO first. See
[Funding and withdrawing](../guides/funding-and-withdrawing.md).

## Strategies

**Why isn't my strategy filling?**

Three common reasons, all of them safe states where nothing has been lost:

- The market has not met your price bound. The rule is waiting, as intended.
- Your gas reserve is empty, so nothing is being triggered. Top it up and it
  resumes.
- For a balancer specifically, the bounds have expired. Re-price them.

**How do I see what my passport has done?**

Every fill is announced on chain - which rule, what went in, what came out, and
the fees. The app shows that as your history, and anyone reading the chain can
reconstruct it independently.

Fill history lives in transaction logs rather than contract state, because
storing a running history on chain would cost minimum balance for ever for
something only a reader needs. So history needs something that indexes past
transactions; current balances, rule contents and free balance are live state
and can be read straight from the contract. See
[Getting started](../guides/getting-started.md).

**Can I change a rule without cancelling it?**

Yes. Re-price it, add funds, or take funds back out, all while it is running. An
edit changes your settings; it cannot rewind what a rule has already done, so you
cannot use one to re-trigger something early or alter a fill that has happened.

**Can I run more than one strategy?**

Yes, of any combination of types your tier allows. They hold their funds
separately and do not interfere with each other. They do share one gas reserve,
so size it with all of them in mind.

**What happens to a limit order when it expires?**

It stops filling. Its funds stay reserved until you remove the rule - expiry does
not return them to free balance, because that would be a second thing happening
to your balance without your signature. Take them back when you want them.

## Fees

**What does it cost?**

Two fees, both on the output of a trade: HOGSWAP's routing fee at 0.05%, and the
keeper's at 0.05% on automated fills. Up to 0.10% total, halving to 0.05% once
your passport holds 100 HOG. A swap you trigger yourself pays no keeper fee.

Nothing is charged on deposits, withdrawals, creating a passport, creating a
rule, or cancelling anything. See [Fees and costs](fees-and-costs.md).

**How does the HOG discount work?**

The routing fee falls linearly with the HOG your passport holds - every micro
unit counts - reaching zero at 100 HOG. It takes effect immediately.

**Where do I keep the HOG?**

In your passport, not your wallet. The discount is calculated on the balance of
the account doing the trading.

**Do I have to set my HOG aside to get the discount?**

No. Setting it aside stops you accidentally trading or withdrawing it, which
would cost you the discount - but the discount is calculated on your passport's
total HOG holding regardless.

**Does hogALGO count toward the 100 HOG?**

No. hogALGO is a staked-ALGO token that pays its yield in HOG - a different
asset. The discount is counted on your passport's real HOG balance. The same
applies to a liquidity position holding HOG on one side.

Nor does it count as ALGO. It cannot fund your gas reserve, a strategy cannot
spend it as ALGO, and you withdraw it as hogALGO. If you describe it so a
balancer can value it, that description is arithmetic for the balancer alone -
it does not create a balance in either asset. See
[Positions](../guides/positions.md).

**Can the fee change after I start?**

A strategy captures the keeper fee rate when you create it and keeps that rate
for its life. A published change reaches strategies you create afterwards, not
ones already running. The routing fee is calculated per swap against your HOG
holding at that moment.

## Tiers and versions

**Why can't I use the grid or the balancer?**

Your passport is on v1.0.0 or v1.0.1, which refuse them. v1.0.2, the current
public version, opens grids and balancers; upgrading to it is in place and
nothing migrates. See [Versions and upgrades](versions-and-upgrades.md).

**How do I get beta access?**

The launch round is closed and criteria for future rounds will be announced. Ask
on [X](https://x.com/LiquiHog) or in the
[Discord](https://discord.gg/a9R4vkXd98) main chat.

**Will I have to migrate to get the beta build?**

No. Both versions are in the same line, so it installs in place. Your passport
keeps its address, funds, running strategies and history.

**Can you force an upgrade on me?**

No. Applying an update is an owner-signed action. There is no mechanism that
updates your passport on our say-so or on a schedule.

## Safety

**Has this been audited?**

No. DeFi Passport has not had an independent third-party security audit. It has
been reviewed and tested internally and runs on mainnet with real funds, but no
external firm has examined it. See [Security](security.md).

**Is the code open-source?**

No, and it is not planned for release. You can verify a great deal about
behaviour on chain - your passport's owner, version, rules, positions and every
fill are all public - but you cannot read the source.

**What is the worst a misbehaving keeper can do?**

Execute a trade you defined, at a price no worse than the bound you set. It
cannot withdraw, invent a trade, or edit a rule. The gap between your bound and
the best available price is the exposure, which is why bounds are mandatory and
why setting them tightly matters.

**What if I stop trusting the automation?**

Cancel your rules. Reserved funds return to free balance and there is nothing
left to trigger. You do not need our cooperation and there is no allowance to
revoke, because there was never one to grant.

## Still stuck

Open an issue on this repository, or ask on
[X](https://x.com/LiquiHog) or in the
[Discord](https://discord.gg/a9R4vkXd98).

Never share a private key or seed phrase. Nobody from LiquiHog will ask for one,
and a transaction id is enough for us to look something up.
