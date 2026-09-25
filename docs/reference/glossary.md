# Glossary

| Term | Definition |
|---|---|
| **ALGO** | Algorand's native currency. Used for network fees, minimum balance, and your passport's gas reserve |
| **Allowlist** | The set of addresses entitled to install the beta build. See [Tiers](../core/tiers.md) |
| **ASA** | Algorand Standard Asset - any token on Algorand other than ALGO itself |
| **Balancer** | Beta-tier strategy that holds a set of assets at target values and trades back toward them on drift |
| **Band** | How far a balancer holding may drift from its target before the strategy acts |
| **Basis points (bps)** | Hundredths of a percent. 5 bps is 0.05% |
| **Beta tier** | The v1.1.2 build: the public strategies plus lending and payments, available to allowlisted addresses |
| **Buy ceiling** | The most a balancer will pay per unit when buying. Mandatory |
| **Cell** | One price point in a grid, holding one side at a time and flipping after each fill |
| **Committed** | See *Reserved* |
| **DCA** | Dollar-cost averaging - see *Scheduled buys* |
| **Directory** | The contract that publishes the current addresses of the contracts a passport uses. App id `3670912562` |
| **Dual staking** | Staking ALGO in exchange for a token that accrues a paired asset alongside it |
| **Fill** | One execution of a rule - the trade actually happening |
| **Free balance** | What your passport holds that is not reserved by a rule or set aside. The amount you can withdraw |
| **Gas reserve** | ALGO you set aside for the keeper to reclaim its trigger costs from. Automation draws on this and nothing else |
| **Grid** | Beta-tier strategy of price cells that buy low, sell high, and re-arm themselves |
| **HOG** | LiquiHog's token. HOG held by your passport reduces the routing fee, to zero at 100 HOG |
| **HOGSWAP** | LiquiHog's in-house route aggregator, used by the app, the SDKs, the API and the keeper |
| **Keeper** | The off-chain service that triggers rules that have come due. It cannot move your funds |
| **Keeper fee** | 0.05% of the output of an automated fill. Not charged on a swap you trigger yourself |
| **Leg** | One of the two parts a position is described in - for a liquidity share the two pool assets, for a staked asset the principal and the accrued yield |
| **Limit order** | Public-tier strategy that trades a fixed amount only at or better than a price you set |
| **Line** | A series of versions sharing a major number. Versions in one line upgrade in place |
| **Liquid staking token (LST)** | A token representing staked ALGO. Dual-staking ones pay their yield in a second asset - hogALGO pays HOG, folksALGO pays FOLKS |
| **LP token** | A token representing a share of a liquidity pool. Can be held by a passport and counted toward a balancer target |
| **Major / minor / patch** | The three parts of a version. Major forces a new passport; minor and patch apply in place |
| **Minimum balance** | ALGO the Algorand protocol requires an account to hold. Held, not spent, and released when the thing requiring it goes away |
| **Owner** | The address recorded at creation as controlling a passport. Cannot be changed |
| **Owner-gated** | A method only the owner may call. Every method that moves value is owner-gated |
| **Partial fill** | A limit order filled in slices rather than all at once. Off by default |
| **Passport** | Your own smart contract. Holds your funds, stores your rules, and enforces them |
| **Position** | A holding your passport has recorded - either set aside from spending, or described so a balancer can value it |
| **Public tier** | The v1.0.2 build, with scheduled buys, limit orders, grids and balancers, open to any address |
| **Quote asset** | The asset a strategy prices in and, for a balancer, holds a shared reserve of |
| **Registry** | The contract a passport registers with. Publishes the keeper's identity and the fee rate. App id `3672932347`. Fixed for a passport's life |
| **Reserved** | Funds a rule or strategy is holding. Not withdrawable and not spendable by anything else until released |
| **Routing fee** | HOGSWAP's 0.05% on the output of a swap, reduced by HOG held, reaching zero at 100 HOG |
| **Rule** | One instruction: trade this pair, this much, under these conditions, not worse than this price |
| **Scheduled buys** | Public-tier strategy that trades a fixed amount on a fixed interval |
| **Sell floor** | The least a balancer will accept per unit when selling. Mandatory |
| **Set aside** | Reserving a holding yourself, so neither you nor a strategy spends it. Used for the gas reserve and for HOG |
| **Slippage tolerance** | An optional additional tightness constraint on a fill |
| **Strategy** | A container for rules of one type. Closing it releases everything its rules hold |
| **Target value** | What a balancer holding should be worth, expressed in the strategy's quote asset |
| **Tier** | Public or beta - which build of the contract an address may install |
| **Trigger** | The keeper's transaction asking a passport to execute a rule that has come due |
