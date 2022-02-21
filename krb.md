# KRB Token

## Contract

!> **WARNING:** The KRB contract has not been audited, please read our [Terms of Use](http://krebit.id/#/terms).

- KRB is an ERC-20 Token with [OpenZepellin](https://openzeppelin.com/contracts/)'s extensions to make it Mintable/Burnable (or Cyclical).

- It manages the available and staked balance, as well as the reputation or verifiable credential registry.

- It also limits tokens to only be transferable between accounts with a min balance, to encourage reputation building.

## Reputation Supply

The KRB Token ethereum contract defines how the KRB tokens are mint/burn and all rules for required balances and rewards for registering Verfiable Credentials.

?> Interacting with an Ethereum smart contract has transaction costs based on the Ethereum Blockchain gas, as well as any service fee defined by the smart contract definition.

?> The definition (ABI) for the KRB contract can be found in Etherscan

## Stake \* Trust

The way to earn KRB token rewards is based on the Trust level that Issuer users give to your registered Verifiable Credentials in the platform, and the risk the Issuer defines in KRB tokens. Issuers can only stake up to the min/max KRB tokens balance in the contract.

Rewards are based on the formula:

![krebit-formula-blue](/img/krebit-formula-blue.png ":size=30%")

Inspired in the [CoreTrust1](https://dl.acm.org/doi/10.1145/2389176.2389208) model of Credit Flow, where a mathematical and/or physical relationship among credit, risk, and trust was established so that it can be properly evaluated: credit = risk × trust.

This approach can construct a Web3 of Trust from the interactions in a social network and infer trust values by making use of both generally agreed reliability and subjective individuality in the network.

## Upgrades

?> **Current Deployments** The [.openzeppelin/rinkeby.json](https://github.com/KrebitDAO/krb-contracts/blob/main/.openzeppelin/rinkeby.json) file in [krb-contracts](https://github.com/KrebitDAO/krb-contracts) keeps track of the current deployed version and previously upgraded implementations

## Reference

[filename](_krbToken.md ":include")
