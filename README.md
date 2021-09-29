# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos: 
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted. 

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

# Contest setup

## 🐺 C4: Set up repos
- [X] Create a new private repo named `YYYY-MM-sponsorname` using this repo as a template.
- [X] Get GitHub handles from sponsor.
- [X] Add sponsor to this private repo with 'maintain' level access.
- [X] Send the sponsor contact the url for this repo to follow the instructions below and add contracts here. 
- [ ] Delete this checklist and wait for sponsor to complete their checklist.

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [x] Name of each contract and:
  - [x] lines of code in each
  - [x] external contracts called in each
  - [x] libraries used in each
- [x] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [x] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [x] Describe anything else that adds any special logic that makes your approach unique
- [x] Identify any areas of specific concern in reviewing the code
- [x] Add all of the code to this repo that you want reviewed
- [ ] Create a PR to this repo with the above changes.

---

# ⭐️ Sponsor: Provide marketing details

- [x] Your logo (URL or add file to this repo - SVG or other vector format preferred)
- [x] Your primary Twitter handle
- [x] Any other Twitter handles we can/should tag in (e.g. organizers' personal accounts, etc.)
- [x] Your Discord URI
- [x] Your website
- [x] Optional: Do you have any quirks, recurring themes, iconic tweets, community "secret handshake" stuff we could work in? How do your people recognize each other, for example? 
- [x] Optional: your logo in Discord emoji format

---

# Contest prep

## 🐺 C4: Contest prep
- [X] Rename this repo to reflect contest date (if applicable)
- [X] Rename contest H1 below
- [X] Add link to report form in contest details below
- [X] Update pot sizes
- [X] Fill in start and end times in contest bullets below.
- [ ] Move any relevant information in "contest scope information" above to the bottom of this readme.
- [X] Add matching info to the [code423n4.com public contest data here](https://github.com/code-423n4/code423n4.com/blob/main/_data/contests/contests.csv))
- [ ] Delete this checklist.

## ⭐️ Sponsor: Contest prep
- [x] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [x] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [x] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [x] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [ ] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [ ] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [x] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [x] Designate someone (or a team of people) to monitor DMs & questions in the C4 Discord (**#questions** channel) daily (Note: please *don't* discuss issues submitted by wardens in an open channel, as this could give hints to other wardens.)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Swivel contest details
- $67,500 worth of ETH main award pot
- $7,500 worth of ETH gas optimization award pot
- Join [C4 Discord](https://discord.gg/EY5dvm3evD) to register
- Submit findings [using the C4 form](https://code423n4.com/2021-09-swivel-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts September 30, 2021 00:00 UTC
- Ends October 6, 2021 23:59 UTC

This repo will be made public before the start of the contest. (C4 delete this line when made public)

# Introduction

Swivel is a yield tokenization protocol that allows LP's, stakers and lenders to separate their yield into two components, zcTokens (which represent the 1-1 claim to deposited tokens upon maturity), and nTokens (which represent the claim to any yield generated). In addition to this base functionality, Swivel provides the infrastructure to facilitate the exchange of these tokens through an orderbook. This allows users to lend with low or no slippage, and liquidity providers to avoid the alpha decay inherent to LPing in derivative markets on an AMM.

Regarding our orderbook infrastructure, the base orderbook functionality is most similar to the original 0x-v3. Users EIP-712 sign an order object which contains information regarding asset, maturity, maker, price, amount, and whether the user is initiating a new position vs exiting/selling a currently held zcToken or nToken position.

A testnet is currently live at https://swivel.exchange .

General Project Docs:https://docs.swivel.finance

Contract Docs: https://docs.swivel.finance/developers/contract 

Recent Video Overview (ETHOnline): https://www.youtube.com/watch?v=hI0Uwd4Xayg .

### **Order Path:**
A taker initiates their own position using `initiate` or `exit` on Swivel.sol, in the process filling another user's order. Swivel.sol handles fund custody and deposits/withdrawals from underlying protocols (compound). Params are routed to Marketplace.sol and according to the `underlying` and `maturity` of an order, a market is identified (asset-maturity combination), and zcTokens and nTokens are minted/burnt/exchanged within that market according to the params.

Order fill amounts and cancellations are tracked on chain based on a keccak of the order itself.

### **nToken and zcToken functionality:**
When a user initiates a new fixed-yield position on our orderbook, or manually calls `splitUnderlying`, an underlying token is split into zcTokens and nTokens. (the fixed-yield comes from immediately selling nTokens).

A zcToken (standard erc-20 + erc-2612) can be redeemed 1-1 for underlying upon maturity. After maturity, if a user has not redeemed their zcTokens, they begin accruing interest from the deposit in compound. 

An nToken (non-standard contract balance) is a balance within a users `vault`(vault.notional) within VaultTracker.sol. nTokens (notional balance) represent a deposit in an underlying protocol (compound), and accrue the interest from this deposit until maturity. This interest can be redeemed at any time.


# Smart Contracts 
| **Contracts**    | **Link** | **LOC** | **LIBS** | **External** |
|--------------|------|------|------|------|
| Swivel       |[Link](https://github.com/Swivel-Finance/gost/blob/v2/test/swivel/Swivel.sol)| 405 | [Abstracts.sol](https://github.com/Swivel-Finance/gost/blob/v2/test/swivel/Abstracts.sol), [Hash.sol](https://github.com/Swivel-Finance/gost/blob/v2/test/swivel/Hash.sol), [Sig.sol](https://github.com/Swivel-Finance/gost/blob/v2/test/swivel/Sig.sol) | [CToken.sol](https://github.com/compound-finance/compound-protocol/blob/master/contracts/CToken.sol) |
| Marketplace  |[Link](https://github.com/Swivel-Finance/gost/blob/v2/test/marketplace/MarketPlace.sol)| 262 | [Abstracts.sol](https://github.com/Swivel-Finance/gost/blob/v2/test/marketplace/Abstracts.sol) |
| VaultTracker |[Link](https://github.com/Swivel-Finance/gost/blob/v2/test/vaulttracker/VaultTracker.sol)| 248 | [Abstracts.sol](https://github.com/Swivel-Finance/gost/blob/v2/test/vaulttracker/Abstracts.sol) | [CToken.sol](https://github.com/compound-finance/compound-protocol/blob/master/contracts/CToken.sol) |

## **Swivel:**
Swivel.sol handles all fund custody, and most all user interaction methods are on Swivel.sol (`initiate`,`exit`,`splitUnderying`,`combineTokens`, `redeemZcTokens`, `redeemVaultInterest`). We categorize all order interactions as either `payingPremium` or `receivingPremium` depending on the params (`vault` & `exit`) of an order filled, and whether a user calls `initiate` or `exit`. 

For example, if `vault` = true, the maker of an order is interacting with their vault, and if `exit` = true, they are selling notional (nTokens) and would be `receivingPremium`. Alternatively, if `vault` = false, and `exit` = false, the maker is initiating a fixed yield, and thus also splitting underlying and selling nTokens, `receivingPremium`. 

Outside of this sorting, the basic order interaction logic is:
1. Check Signatures, Cancellations, Fill availability for order validity
2. Calculate either principalFilled or premiumFilled depending on whether the order is paying/receivingPremium
3. Calculate fee
4. Deposit/Withdraw from compound and/or exchange/mint/burn zcTokens and nTokens through marketplace.sol
5. Transfer fees

Other methods (`splitUnderying`,`combineTokens`, `redeemZcTokens`, `redeemVaultInterest`) largely just handle fund custody from underlying protocols, and shoot burn/mint commands to marketplace.sol.

## **Marketplace:**
Marketplace.sol acts as the central hub for tracking all markets (defined as an asset-matury pair). Markets are stored in a mapping and admins are the only ones that can create markets.

Any orderbook interactions that require zcToken or nToken transfers are handled through marketplace burn/mints in order to avoid requiring approvals.

If a user wants to transfer nTokens are without using our orderbook, they do so directly through the marketplace.sol contract.

## **VaultTracker:**
A user's vault has three properties, `notional` (nToken balance), `redeemable` (underlying accrued to redeem), and `exchangeRate` (compound exchangeRate of last vault interaction).

When a user takes on a floating position and purchases nTokens (vault initiate), they increase the notional balance of their vault (`vault.notional`). Opposingly, if they sell nTokens, this balance decreases.

Every time a user either increases/decreases their nToken balance (`vault.notional`), the marginal interest since the user's last vault interaction is calculated + added to `vault.redeemable`, and a new `vault.exchangeRate` is set.
