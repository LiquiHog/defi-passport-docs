# Developer tools

Everything the app does can be done directly against the contracts. This page
lists what is available and what each thing is for.

## DeFi Passport SDK

**[github.com/LiquiHog/defi-passport-sdk](https://github.com/LiquiHog/defi-passport-sdk)**

A TypeScript SDK covering everything an owner does with their own passport:
creating one, funding it, running strategies against it, and reading its state.

**It builds transactions; it does not sign them.** Every function returns an
unsigned transaction or reads chain state. Your wallet or your backend owns
signing. The SDK owns the part that is invisible at the call site and unforgiving
to get wrong - knowing which references, foreign applications and group orderings
each call requires.

It also decodes what a passport logs, so you can build a fill history and a
position view, and it can turn a rejected transaction into a readable reason
rather than an opaque failure code.

**Scope.** It is the user-facing surface only. Protocol administration is not
merely undocumented in it - those methods are absent from the build.

The SDK bundles the contract programs it can install, and a release is tied to a
set of published versions. If it is asked for a version newer than it carries, it
says so rather than guessing.

## HOGSWAP

The route aggregator behind DeFi Passport, the LiquiHog app, and the keeper's
routing. It finds a route across Algorand's DEXs and returns an unsigned swap for
you to sign. You can use it independently of DeFi Passport.

| | |
|---|---|
| **API** | [hogswap-v1.liquihog.dev/docs](https://hogswap-v1.liquihog.dev/docs#/) |
| **JavaScript SDK** | [github.com/LiquiHog/hogswap-js-sdk](https://github.com/LiquiHog/hogswap-js-sdk) |
| **Python SDK** | [github.com/LiquiHog/hogswap-py-sdk](https://github.com/LiquiHog/hogswap-py-sdk) |
| **MCP server** | [github.com/LiquiHog/hogswap-mcp](https://github.com/LiquiHog/hogswap-mcp) |

Both SDKs are zero-dependency and return unsigned transactions, the same
principle as the passport SDK - quotes and builds come from the API, signing
stays with you.

The MCP server exposes quotes and swap builds as tools for AI agents.

## Which to reach for

| You want to | Use |
|---|---|
| Create or manage a passport | DeFi Passport SDK |
| Read a passport's strategies, positions or fill history | DeFi Passport SDK |
| Quote or build a swap, with no passport involved | HOGSWAP API or either HOGSWAP SDK |
| Give an agent the ability to quote and build swaps | HOGSWAP MCP server |

## Getting started against the contracts

Two things to have in hand before anything else:

**The on-chain ids.** You need the directory and the registry - see
[On-chain ids](on-chain-ids.md). Keep them in your own configuration rather than
hardcoded in a build, and verify the directory's creator address before trusting
what it publishes.

**Which version the address may install.** Ask the registry before offering to
create anything. It returns the version an address is entitled to, or tells you
none is open to it. Building a create transaction without checking means finding
out from a failed transaction instead of from a screen. See
[Tiers](../core/tiers.md).

From there the flow is the one in [Getting started](../guides/getting-started.md):
create and register, fund, opt in, set aside gas, create a strategy.

## Two things worth knowing before you build

**Simulate before submitting.** A group can be correct when you build it and
wrong by the time it lands - a rule's committed funds can change between your
read and your transaction. Simulating catches that.

**Rule and strategy identifiers are read, never guessed.** They come from live
state. Deriving them by scanning what exists will eventually predict an
identifier the contract will not use.

Both are handled for you if you use the SDK.

## Support

Documentation issues and behaviour that does not match these docs: open an issue
on this repository.

SDK questions, API access, and anything else:

- **X (Twitter)**: [@LiquiHog](https://x.com/LiquiHog)
- **Discord**: [discord.gg/a9R4vkXd98](https://discord.gg/a9R4vkXd98)
- **Email**: [LiquiHog@gmail.com](mailto:LiquiHog@gmail.com)
