# Overview

## What is Krebit?

> [Krebit.id](https://krebit.id) is an open identity verification protocol, DAO and marketplace for Web3 Verifiable Credentials.

Decentralized Reputation (DeRep) has great challenges: sovereign-identities, reputation calculation, sybil attacks, governance, vote buying, vote swings, vote apathy, plutocracy, etc. Krebit mitigates all of them and creates **a pseudonymous economy for users to prove things about them without revealing any unnecessary information.**

## Self Sovereign-Identity

Krebit is a **user-centic protocol**. It uses Ceramic's Decentralized Identity (DID) to enable users control their profiles and data-stores.

A user can login with a **Non-Custodial ethereum wallet** and take ownership of their data. Ceramic also supports the main public blockchains, making the user data effectively **portable across the Web3**. An example of a DID is `did::pkh:123456abcdef`.

By using this **open, multi-chain data-model**, Krebit users are able to manage their identities and Web3 developers are able to obtain real-time reputation of an identity owner. Web3 dApps are also able to create claims, verify them and register them, all from common known data-models.

Learn more:

- [Login with Wallet](get_verified#login-with-wallet)

## Verifiable Credentials

![Web3-Verifiable-Credentials](/img/Web3-Verifiable-Credentials.png)

A Claim is a statement about a subject, e.g. about their identity, location, accomplishments, owned property, etc. A [W3C-Verifiable-Credential](https://www.w3.org/TR/vc-data-model/) is a cryptographically signed attestation made by an Issuer to verify the validity of a claim.

With Krebit the user **private Claim data is stored off-chain in the Ceramic Network** encrypted and controlled by their DID. After a user manages their claim information, they can request judgement from a verifier based on a fee that they are willing to pay.

A verifier with enough KRB Tokens validates the data, signs the Verifiable Credential and sends the attestation back to the user.

**Both users and attestators are rewarded when users register the Verifiable Credential on-chain in the $KRB Token ethereum smart contract.**

Verifiable Credentials can be revoked by an Issuer, they can expire, and also be deleted by the credential subject. In all these cases both the issuer and subject rewards are also burnt.

The Verifiable Credentials expiration date ensures that reputation scores represent recent contributions to Krebit.

Learn more:

- [Get Verified](get_verified#claim-credentials)
- [Become an Issuer](issuer)

## Krebit DAO

The Krebit protocol is governed by an open DAO, where any member can **dispute a Verifiable Credential via proposal**.

This allows the platform to be operated with maximal transparency, minimal required trust in centralized operators, and no centralized attack surfaces.

Once raised, disputes must be resolved by voting and the KRB rewards could get burnt and the verifier’s stake could get slashed.

Learn more:

- [Krebit DAO](dao)

## Web3 Stack

Krebit uses a Triple Layer architecture of scalable, decentralized, privacy preserving Web3 technologies:

- P2P Storage and Chat

Ceramic Netork offers a mutable data stream storage system on top of IPFS.

Data availability among nodes is achieved through libp2p pubsub so that as long as one node subscribed to the pubsub topic has the needed commit log, data will be available for query among all the nodes.

Long-term data retention is guaranteed through Ceramic’s blockchain anchoring and an IPFS data pinning service.

- Off-Chain Signed Data

EIP712 vc

- Blockchain Contracts

Solidity: EVM and zkEVM

![Krebit-Architecture](/img/Krebit-Architecture.png ":size=500")

## NFT Credentials

- Opensea: https://opensea.io/collection/krebit-verifiable-credentials
- Rarible: https://rarible.com/krebit-verifiable-credentials

Mapping of Credentials vs ERC1155 NFT tokens:

https://node401.krebit.id/metadata/all

Example:

```javascript
    "AgeGT18": {
        "name": "Age > 18",
        "tokenId": "67635111694905454750656545562169396067333546840103698301069856292090375322325"
    },
    "Email": {
        "name": "Email",
        "tokenId": "40021678008535640475191734029493822943777967446419437373838192638048352067564"
    },
    "PhoneNumber": {
        "name": "Phone Number",
        "tokenId": "17262999309544526619391335994326136167734383564354599749789590225102621843354"
    },
    "Twitter": {
        "name": "Twitter",
        "tokenId": "77063689935824096560045109234073832972855346599201458371832518884974849092031"
    },
    "TwitterFollowersGT1K": {
        "name": "Twitter Followers > 1K",
        "tokenId": "89738448909262774762831709705569279227343536779743166698674193014930532264406"
    },
...
    "Discord": {
        "name": "Discord",
        "tokenId": "109892553069226592861552127612525159059191555100102304293041004114913656473745"
    },
    "DiscordGuildOwner": {
        "name": "Discord Guild Owner",
        "tokenId": "93304639584400428281907059752986528361531082486641418130415124711700498336882"
    },
    "DiscordGuildMember": {
        "name": "Discord Guild Member",
        "tokenId": "4190401451428534333843180428082502793665468334666159661981442097195148040013"
    },
    "Github": {
        "name": " Github",
        "tokenId": "65519100179299519584334791948139958903579500130658539743567381861186592867363"
    },
    "GithubFollowersGT10": {
        "name": " Github Followers > 10",
        "tokenId": "31363295818865202011757849885724201111393346844268475356973931666873542272415"
    },
...
```

Learn More:

- [KRB Token](krb)
- [Ceramic Datamodels](developers#ceramic-datamodels)
- [EIP12 Verifiable Credentials **SDK**](developers#eip712-vc-sdk)
- [KRB Token Subgraph](developers#contract-subgraph)
