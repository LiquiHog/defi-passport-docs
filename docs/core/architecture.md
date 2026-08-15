# Architecture

Four things are involved when your passport makes a trade. Only one of them
holds your funds.

![Architecture Overview](../assets/architecture.svg)

## Your passport

A smart contract on Algorand, deployed by you and owned by your address. It
holds your ALGO and your assets, stores your rules, and enforces them.

Every method that moves value checks the caller against the owner recorded at
creation. That includes withdrawing, opting into or out of an asset, creating,
editing or removing a rule, reserving and releasing funds, upgrading the
contract, and closing it. None of those can be called by anyone else, including
us.

**One passport per owner.** Your balance is not pooled with anyone else's. There
is no shared position to be diluted, no queue to be behind, and no other user
whose loss can become yours. It also means the cost of running one is yours
alone - see [Fees and costs](../reference/fees-and-costs.md).

## HOGSWAP

LiquiHog's in-house route aggregator. When your passport trades, HOGSWAP is what
finds the route across Algorand's DEXs and builds the swap.

It is the same aggregator behind the LiquiHog app, the public API, the JavaScript
and Python SDKs, and the MCP server - see
[Developer tools](../reference/developer-tools.md). The keeper uses it too, to
work out how to execute a rule that has come due.

HOGSWAP charges 0.05% on the output of a swap, reduced by the HOG your passport
holds. That fee and the keeper's are the only two charged on a trade.

Routing is where the passport's guarantee earns its keep. Your passport does not
trust the route it is handed. It measures what actually left and what actually
arrived, and checks that against the bound on your rule. A route that produces
less than your rule requires is refused, whoever proposed it.

## The registry

The contract your passport registers with when it is created. It does three
things you can observe:

- **It records that your passport exists**, which is how the automation finds
  work. A passport that is not registered cannot be automated.
- **It publishes the keeper's identity.** Your passport reads it to decide whose
  trigger to accept. Nobody else can trigger your rules.
- **It publishes the fee rate.** A strategy captures the rate when you create it
  and keeps it - see [Fees and costs](../reference/fees-and-costs.md).

The registry cannot move your funds. It holds no balance of yours and has no
method that reaches into your passport.

## The directory

A published list of the current addresses of the contracts your passport uses.
It exists so those can be replaced - a router upgrade, say - without every
existing passport needing to be recreated.

**Your passport does not follow it automatically.** Adopting an updated address
is an owner-signed action, the same as any other change. We publish; you decide
whether to accept. That ordering is deliberate: an address that decides where
funds go is not something an automated process should be able to change on your
behalf.

For the ids and how to verify them, see
[On-chain ids](../reference/on-chain-ids.md).

## The keeper

An off-chain service, run by LiquiHog, that watches for rules that have come due
and triggers them.

It sits outside everything above. It holds no balance of yours, has no
withdrawal method available to it, and cannot create or edit a rule. Triggering
is the only thing it can do, and a trigger that does not satisfy your rule's own
terms is rejected by your passport before anything moves.

See [Automation](automation.md) for what it is paid, what happens when it stops,
and what it deliberately cannot do.

## How a trade actually happens

1. A rule of yours becomes eligible - its interval has elapsed, or the market has
   reached the price it was waiting for.
2. The keeper works out a route through HOGSWAP and submits a trigger to your
   passport.
3. Your passport executes the swap, then measures what left and what arrived.
4. It checks the result against your rule's bound. If it falls short, the whole
   thing is rejected and nothing moves.
5. If it passes, the rule's state advances, the two fees come out of the output,
   and the keeper reclaims exactly what the trigger transaction cost it from your
   gas reserve.

Step 4 is the part that matters. Every guarantee on this page reduces to it:
your passport does not take anyone's word for what a trade was worth, including
ours.
