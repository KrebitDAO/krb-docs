# Overview

## What is Krebit?

> [Krebit.id](https://krebit.id) is an open identity verification protocol, DAO and marketplace for Web3 Verifiable Credentials.

Decentralized Reputation (DeRep) has great challenges: sovereign-identities, reputation calculation, sybil attacks, governance, vote buying, vote swings, vote apathy, plutocracy, etc. Krebit mitigates all of them and creates **a pseudonymous economy for users to prove things about them without revealing any unnecessary information.**

## Self Sovereign-Identity

Krebit is a **user-centic protocol**. It uses Ceramic's Self.Id Decentralized Identity (DID) to enable users control their profiles and data-stores.

A user can login with a **Non-Custodial ethereum wallet** and take ownership of their data. Ceramic also supports the main public blockchains, making the user data effectively **portable across the Web3**. An example of a DID is `did::example:123456abcdef`.

By using this **open, multi-chain data-model**, Krebit users are able to manage their identities and Web3 developers are able to obtain real-time reputation of an identity owner. Web3 dApps are also able to create claims, verify them and register them, all from common known data-models.

Learn more:

- [Login with Wallet](get_verified#login-with-wallet)

## Verifiable Credentials

![Web3-Verifiable-Credentials](/img/Web3-Verifiable-Credentials.png)

A Claim is a statement about a subject, e.g. about their identity, location, accomplishments, owned property, etc. A [W3C-Verifiable-Credential](https://www.w3.org/TR/vc-data-model/) is a cryptographically signed attestation made by an Issuer to verify the validity of a claim.

With Krebit the user **private Claim data is stored off-chain in the Ceramic Network** encrypted and controlled by their DID. After a user manages their claim information, they can request judgement from a verifier based on a fee that they are willing to pay.

A verifier with enough KRB Tokens validates the data, signs the Verifiable Credential and sends the attestation back to the user.

**Both users and attestators are rewarded when users register the Verifiable Credential on-chain in the $KRB Token ethereum smart contract.**

Learn more:

- [Get Verified](get_verified#claim-credentials)
- [Become an Issuer](issuer)

## Krebit DAO

The Krebit protocol is governed by an open DAO, where any member can **dispute a Verifiable Credential via proposal**. Once raised, disputes must be resolved by voting and the KRB rewards could get burnt and the verifier’s stake could get slashed.

Learn more:

- [Krebit DAO](dao)

## Web3 Stack

Krebit uses a Triple Layer architecture of scalable, decentralized, privacy preserving Web3 technologies:

- P2P Storage and Chat

- Off-Chain Signed Data

- Blockchain Contracts

![Krebit-Architecture](/img/Krebit-Architecture.png ":size=50%")

Learn More:

- [KRB Token](krb)
- [Ceramic Datamodels](developers#ceramic-datamodels)
- [EIP12 Verifiable Credentials **SDK**](developers#eip712-vc-sdk)
- [KRB Token Subgraph](developers#contract-subgraph)
