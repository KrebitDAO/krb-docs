# Web3 Developers

## Reputation in your dApp

- **Let your users manage their identity Claims** [using Ceramic's datamodels and Self.ID](developers#ceramic-datamodels)

- **Issue Krebit verifiable credentials** [using the EIP712-vc SDK](developers#eip712-vc-sdk)

- **Register Verifiable credentials on-chain** [using the KRB-Token Smart Contract](krb)

- **Check the aggregated reputation of an identity owner off-chain** [using the KRB-Token Subgraph](developers#contract-subgraph).

### Use Case 1: Decentralized KYC/AML

### Use Case 2: Restrict access to Adult/NSFW pages

### Use Case 3: Anti-Sybil prevention

## Ceramic Datamodels

Krebit uses Ceramic's Self.Id Decentralized Identity (DID) to enable users control their profiles and data-stores.

?> The [ceramic/datamodels](https://github.com/ceramicstudio/datamodels/tree/main/models/verifiable-credentials) repository hosts the [Krebit] Verifiable Credentials registry open data-model.

Data models are open standards created by the community that form the basis of data composability on Ceramic. When multiple applications reuse the same data model, they get access to the same data-store.

### Data Model

The VerifiableCredentials data-model provides 3 definitions for the users's DID-datastore:

- claimedCredentials - [ClaimedCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/ClaimedCredentials.json) : self-issued by the user.
- issuedCredentials - [IssuedCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/IssuedCredentials.json) : issued to other users.
- heldCredentials - [HeldCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/HeldCredentials.json) : received from issuers.

Each of these definitions has an array of [VerifiableCredential](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/VerifiableCredential.json) stream IDs.

### Installation

```console
$ npm install @self.id/web
$ npm install @datamodels/verifiable-credentials
```

### Login with Wallet to DID

```javascript
import { EthereumAuthProvider, SelfID } from "@self.id/web";
import { model as credentialRegistryModel } from "@datamodels/verifiable-credentials";

// The following assumes there is an injected `window.ethereum` provider
const addresses = await window.ethereum.request({
  method: "eth_requestAccounts",
});

// The following configuration assumes your local node is connected to the Clay testnet
const self = await SelfID.authenticate({
  authProvider: new EthereumAuthProvider(window.ethereum, addresses[0]),
  ceramic: "local",
  connectNetwork: "testnet-clay",
  credentialRegistryModel,
});
```

### Storing Claims

Sample streams:

- fullName ClaimedCredential: https://documint.net/kjzl6cwe1jw148duwupih6l1fmqbncvkzquhiie407e1f5kkv5isxvjkig9u3ma
- olderThan ClaimedCredential: https://documint.net/kjzl6cwe1jw14aph1ar5pxyvtlosvfr3h109pg26ucbzqil8an1w27tkgi6x674

### Storing Verifiable Credentials

Sample VerifiableCredentials:

- https://documint.net/kjzl6cwe1jw147ag9phrmzdcsmq2jiwhm6ijl8fa0k12v08zzmibg98tmk4jhg5

Learn more:

- [Ceramic Docs](https://developers.ceramic.network)
- [Seld.ID SDK](https://developers.ceramic.network/reference/self-id/)
- [DID DataStore](https://developers.ceramic.network/tools/glaze/did-datastore/)

## eip712-vc SDK

The [eip712-vc](https://github.com/KrebitDAO/eip712-vc) repository hosts the [Krebit] EIP712-VC tools, based on [W3C-Ethereum-EIP712-Signature-2021-Draft](https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec).

[krebit]: http://krebit.id

It provides functions for creating both W3C compliant Verifiable Credentials, and also a Solidity compatible version that can be used in contracts like [krebit-contracts].

[krebit-contracts]: https://github.com/KrebitDAO/krb-contracts

### Installation

```console
$ npm install -s @krebitdao/eip721-vc
```

### Configuration

Configuring the EIP712 Domain:

```javascript
import {
  EIP712VC,
  VerifiableCredential,
  EIP712VerifiableCredential,
  DEFAULT_CONTEXT,
  EIP712_CONTEXT,
  DEFAULT_VC_TYPE,
  getKrebitCredentialTypes,
  getEIP712Credential,
} from "@krebitdao/eip721-vc";

const eip712Domain = {
  name: "Krebit",
  version: "0.1", // Krebit protocol version
  chainId: 4, //Rinkeby testnet
  verifyingContract: KRB_TOKEN_ADDRESS,
};

const eip712vc = new EIP712VC(eip712Domain);
```

### Create Credential

Creating Krebit Compatible EIP712 Verifiable Credentials:

```javascript
let issuanceDate = Date.now();
let expirationDate = new Date();
expirationDate.setFullYear(expirationDate.getFullYear() + 3);

let credential = {
  "@context": [DEFAULT_CONTEXT, EIP712_CONTEXT],
  type: [DEFAULT_VC_TYPE],
  id: "https://example.org/person/1234",
  issuer: {
    id: "did:issuer",
    ethereumAddress: "0x00000000000000000000000000000000000000000000",
  },
  credentialSubject: {
    id: "did:user",
    ethereumAddress: "0x00000000000000000000000000000000000000000000",
    type: "fullName",
    typeSchema: "ceramic://id",
    value: "encrypted",
    encrypted:
      "0x0c94bf56745f8d3d9d49b77b345c780a0c11ea997229f925f39a1946d51856fb",
    trust: 50,
    stake: 6,
    nbf: Math.floor(issuanceDate / 1000),
    exp: Math.floor(expirationDate.getTime() / 1000),
  },
  credentialSchema: {
    id: "https://example.com/schemas/v1",
    type: "Eip712SchemaValidator2021",
  },
  issuanceDate: new Date(issuanceDate).toISOString(),
  expirationDate: new Date(expirationDate).toISOString(),
};

//Parses the credential for solidity compatibility
// @context -> _context : string
// type -> _type : string
let eip712Credential = getEIP712Credential(credential);

// EIP712 types for Krebit Schema
let krebitTypes = getKrebitCredentialTypes();

const vc = await eip712vc.createEIP712VerifiableCredential(
  eip712Credential,
  krebitTypes,
  async (data) => {
    // Replace this fuction with your own signing code
    return await issuerSigner._signTypedData(
      eip712Domain,
      krebitTypes,
      eip712Credential
    );
  }
);
```

### Verify Credential

Verifying EIP712 Verifiable Credentials:

```javascript
eip712vc.verifyEIP712Credential(
  issuerAddress,
  eip712Credential,
  krebitTypes,
  vc.proof.proofValue,
  async (data: TypedMessage<EIP712MessageTypes>, proofValue: string) => {
    // Replace this fuction with your own signing code
    return recoverTypedSignature_v4({ data, sig: proofValue });
  }
);
```

## Contract Subgraph

How to export Krebit reputation to web2 and web3 sites?

The [krb-subgraph](https://github.com/KrebitDAO/krb-subgraph) repository hosts the KRB Token subgraph, based in [OpenZeppelin subgraphs], that can be used to easily query the KRB token balances of a user, as well as all the Verified Credentials and status from both an Ethereum Address, as well as an issuer or credential subject DID.

[openzeppelin subgraphs]: https://docs.openzeppelin.com/subgraphs/0.1.x/

### Setup

You can use various GraphQL Client libraries to query the subgraph and populate your app with the data indexed by The Graph from the events originated in the KRB token Ethereum transactions.

?> Check The Graph documentation for [Querying from an Application](https://thegraph.com/docs/en/developer/querying-from-your-app/).

### Rinkeby URLs

Explorer: https://thegraph.com/hosted-service/subgraph/krebit/krb-token-v01

Queries (HTTP): https://api.thegraph.com/subgraphs/name/krebit/krb-token-v01

Subscriptions (WS): wss://api.thegraph.com/subgraphs/name/krebit/krb-token-v01

### Query examples

Total supply and biggest token holders:

```javascript
let supply =
  `
  query 
  erc20Contract(id: "` +
  KRB_TOKEN_ADDRESS +
  `") {
    totalSupply {
      value
    }
    balances(first: 5, orderBy: valueExact, orderDirection: desc, where: { account_not: null }) {
      account {
        id
      }
      value
    }
  }
}
`;
```

Roles of a user:

```javascript
let roles = `
  query 
  account(id: "0x9667f70c3648135ad2dc31e1d2d950ca0e299c26") {
    membership {
      accesscontrolrole {
        contract { id }
        role { id }
      }
    }
  }
}
`;
```

Verifiable Credentials:

```javascript
let verifiableCredentials = `
  query {
  verifiableCredentials (where: {credentialSubjectDID: "did:3:kjzl6cwe1jw146bwm1mugh6oyyetl2ks8i1f4sod3vjheb9y287ugh4jghfb0db"} ){
    claimId
    anchorCommit
    claimType
    status
    transaction
    issuerDID
    credentialSubject
    credentialSubjectDID
    issuanceDate
    expirationDate
    trust
    stake
  }
}
`;
```
