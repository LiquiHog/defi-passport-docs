# On-chain ids

DeFi Passport is live on **Algorand mainnet**.

| Contract | App id |
|---|---|
| Directory | `3670912562` |
| Registry | `3672932347` |

Your own passport has its own app id, assigned when you create it.

## The directory is the only id you should keep

The **directory** publishes the current addresses of the contracts a passport
uses. Everything else resolves through it at runtime, which means those can be
replaced without you shipping a new build or creating a new passport.

Keep the directory id in your own configuration and pass it in. Do not compile it
into a release: an id baked into a published build is a promise it will never
change, and correcting one afterwards means every consumer has to install a new
version.

**Verify the directory's creator address before trusting what it publishes.**
That check is what makes accepting a directory id from configuration safe in the
first place. An entry that has not been published yet reads as zero rather than
failing, so check the specific entry you need before relying on it.

## The registry is different

The **registry** is where your passport is registered, and it is the one thing a
passport can never be re-pointed at. It is fixed when the passport is created,
deliberately: the registry holds your passport's registration and is what the
automation authenticates against, so allowing it to be swapped would allow the
trust anchor to be swapped.

This is why the registry is not resolved through the directory even though it
appears in it. Your passport uses the registry it was born with, for its whole
life.

## Adopting a published change

If a contract address the directory publishes is updated, your passport does not
follow it automatically. Accepting the new address is an owner-signed action.

We publish; you decide whether to accept. An address that determines where funds
go is not something an automated process should be able to change on your behalf,
and the contract is built that way rather than merely operated that way.

## Verifying independently

Everything here is readable from any Algorand node or block explorer, and you do
not have to take these values from this page.

- Look up either app id and check its creator address.
- A passport's owner, its registry, and the version it is running are all in its
  own on-chain state.
- Strategy and rule records are stored on the passport and are publicly readable,
  as are the events it emits when a rule fills.

If a value here disagrees with what is on chain, chain is correct - please open
an issue so we can fix the page.

## What is not listed here

Other contract addresses exist - routing, execution budget, the keeper's own
address. They are published through the directory and are resolved at runtime,
so they are not pinned in documentation that would go stale the moment one
changed.

If you are building something that needs them, resolve them through the
directory. See [Developer tools](developer-tools.md).
