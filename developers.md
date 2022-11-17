# Web3 Developers

## Reputation Passport SDK

?> Using the [krebitdao/reputation-passport](https://www.npmjs.com/package/@krebitdao/reputation-passport) SDK package, you can easily read and write [Krebit] Verifiable Credentials for your users.

It provides functions for creating off-chain [Verifiable-Credentials] in Ceramic that can be verified on-chain with [krebit-contracts].

[krebit-contracts]: https://github.com/KrebitDAO/krb-contracts
[verifiable-credentials]: https://github.com/ceramicstudio/datamodels/tree/main/models/verifiable-credentials

### Code examples

https://github.com/KrebitDAO/krebit/tree/develop/examples

### Installation

```console
$ npm install -s @krebitdao/reputation-passport
```

Peer dependencies for use on frontend(browwser):

```console
npm i ethers siwe @lit-protocol/sdk-browser @krebitdao/eip712-vc @krebitdao/reputation-passport
```

Peer dependencies for use on backend(nodejs):

```console
npm i ethers siwe @lit-protocol/sdk-nodejs @krebitdao/eip712-vc @krebitdao/reputation-passport
```

### Defalut config parameters

Polygon testnet:

```javascript
const initialConfigTestnet = {
  network: "mumbai",
  rpcUrl:
    "https://rpc-mumbai.maticvigil.com/v1/5de1e8fc6cabc2e7782450d3a1a2135b2710c50c",
  graphUrl: "https://api.thegraph.com/subgraphs/name/krebit/krb-mumbai-v01",
  ensGraphUrl: "https://api.thegraph.com/subgraphs/name/ensdomains/ens",
  ceramicUrl: "https://ceramic-clay.3boxlabs.com",
  publicUrl: "https://testnet.krebit.id",
  biconomyKey: "", // for gasless transactions
};
```

Polygon mainnet:

```javascript
const initialConfigMainnet = {
  network: "polygon",
  rpcUrl:
    "https://rpc-mainnet.maticvigil.com/v1/5de1e8fc6cabc2e7782450d3a1a2135b2710c50c",
  graphUrl: "https://api.thegraph.com/subgraphs/name/krebit/krb-matic-v1",
  ensGraphUrl: "https://api.thegraph.com/subgraphs/name/ensdomains/ens",
  ceramicUrl: "https://node1.orbis.club",
  biconomyKey: "", // for gasless transactions
};
```

### Read-Only Passport

```javascript
import krebit from "@krebitdao/reputation-passport";

const passport = new krebit.core.Passport({
  ceramicUrl: "https://ceramic-clay.3boxlabs.com", //overriding default config
});
passport.read(address);

const profile = await passport.getProfile();
console.log("profile: ", profile);

const credentials = await passport.getCredentials();
console.log("credentials: ", credentials);

const reputation = await passport.getReputation();
console.log("reputation: ", reputation);

const stamps = await passport.getStamps(10, "DigitalProperty");
console.log("stamps: ", stamps);
```

### Initialize Ethereum Provider

```javascript
import krebit from '@krebitdao/reputation-passport';

// Example on Browser:
const connectWeb3 = async () => {
  if (!window) return;
  const ethereum = (window as any).ethereum;

  if (!ethereum) return;

  const addresses = await ethereum.request({
    method: 'eth_requestAccounts'
  });
  const address = addresses[0];
  const ethProvider = await Krebit.lib.ethereum.getWeb3Provider();
  const wallet = ethProvider.getSigner();
  return { address, wallet, ethProvider };
};

// NODE JS Example:
export const connect = async () => {
  try {
    const ethProvider = Krebit.lib.ethereum.getProvider();

    let wallet: ethers.Wallet;

    try {
      // Create wallet from ethereum seed
      const unlockedWallet = ethers.Wallet.fromMnemonic(SERVER_ETHEREUM_SEED);
      // Connect wallet with provider for signing the transaction
      wallet = unlockedWallet.connect(ethProvider);
    } catch (error) {
      console.error('Failed to use local Wallet: ', error);
    }
    if (wallet && wallet.address) {
      console.log('address: ', wallet.address);
      ethProvider.setWallet(wallet);
      return { wallet, ethProvider };
    }
    return undefined;
  } catch (error) {
    throw new Error(error);
  }
};
```

### Initialize Issuer

```javascript
import krebit from "@krebitdao/reputation-passport";
const { wallet, ethProvider } = await connect();

const Issuer = new krebit.core.Krebit({
  wallet,
  ethProvider,
  network: "mumbai",
  address: wallet.address,
  ceramicUrl: "https://ceramic-clay.3boxlabs.com",
});
const did = await Issuer.connect();
```

### Issue Credential

```javascript
const getClaim = async (toAddress: string) => {
  const badgeValue = {
    communityId: 'My Community',
    name: 'Community Badge Name',
    imageUrl: 'ipfs://asdf',
    description: 'Badge for users that meet some criteria',
    skills: [{ skillId: 'participation', score: 100 }],
    xp: '1'
  };

  const expirationDate = new Date();
  const expiresYears = 3;
  expirationDate.setFullYear(expirationDate.getFullYear() + expiresYears);
  console.log('expirationDate: ', expirationDate);

  const claim = {
    id: `quest-123`,
    ethereumAddress: toAddress,
    did: `did:pkh:eip155:1:${toAddress}`
    type: 'questBadge',
    value: badgeValue,
    tags: ['quest', 'badge', 'Community'],
    typeSchema: 'https://github.com/KrebitDAO/schemas/questBadge',
    expirationDate: new Date(expirationDate).toISOString()
  };
};

const claim = await getClaim(toAddress);
const issuedCredential = await Issuer.issue(claim);
```

### Verify Credential

```javascript
console.log(
  "Verifying credential:",
  await Issuer.checkCredential(issuedCredential)
);
```

### Add Credential to My Passport

```javascript
import krebit from "@krebitdao/reputation-passport";
const { wallet, ethProvider } = await connectWeb3();

const passport = new krebit.core.Passport({
  ethProvider: ethProvider.provider,
  address,
  ceramicUrl: "https://ceramic-clay.3boxlabs.com",
});
await passport.connect();

const addedCredentialId = await passport.addCredential(issuedCredential);
console.log("addedCredentialId: ", addedCredentialId);
```

### Issue Ecrypted Credential

With Lit protocol:

```javascript
import krebit from '@krebitdao/reputation-passport';
import LitJsSdk from "@lit-protocol/sdk-browser"; // Added Lit

const { wallet, ethProvider } = await connectWeb3();

const Issuer = new krebit.core.Krebit({
        wallet,
        ethProvider: ethProvider.provider,
        address,
        ceramicUrl: 'https://ceramic-clay.3boxlabs.com',
        litSdk: LitJsSdk // Added Lit
      });

const getEncryptedClaim = async (toAddress: string) => {
  const privateValue = {
    secretValue: 'My Secret',
  };

  const expirationDate = new Date();
  const expiresYears = 3;
  expirationDate.setFullYear(expirationDate.getFullYear() + expiresYears);
  console.log('expirationDate: ', expirationDate);

  const claim = {
    id: `custom-123`,
    ethereumAddress: toAddress,
    type: 'custom',
    value: privateValue,
    tags: ['tag1', 'tag2'],
    typeSchema: '<type url>',
    expirationDate: new Date(expirationDate).toISOString()
    encrypt: 'lit' as 'lit' // Added Lit
  };
};

const claim = await getEncryptedClaim(toAddress);
const issuedCredential = await Issuer.issue(claim);
```

### Decrypt Credential

```javascript
const decrypted = await Issuer.decryptClaim(issuedCredential);
console.log("Decrypted:", decrypted);
```

## Other libraries

- **Let your users manage their identity Claims** [using Ceramic's datamodels and Self.ID](developers#ceramic-datamodels)

- **Issue Krebit verifiable credentials** [using the EIP712-vc SDK](developers#eip712-vc-sdk)

- **Register Verifiable credentials on-chain** [using the KRB-Token Smart Contract](krb)

- **Check the aggregated reputation of an identity owner off-chain** [using the KRB-Token Subgraph](developers#contract-subgraph).

### Ceramic Datamodels

Krebit uses Ceramic's Self.Id Decentralized Identity (DID) to enable users control their profiles and data-stores.

?> The [ceramic/datamodels](https://github.com/ceramicstudio/datamodels/tree/main/models/verifiable-credentials) repository hosts the [Krebit] Verifiable Credentials registry open data-model.

Data models are open standards created by the community that form the basis of data composability on Ceramic. When multiple applications reuse the same data model, they get access to the same data-store.

#### Data Model

The VerifiableCredentials data-model provides 3 definitions for the users's DID-datastore:

- claimedCredentials - [ClaimedCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/ClaimedCredentials.json) : self-issued by the user.
- issuedCredentials - [IssuedCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/IssuedCredentials.json) : issued to other users.
- heldCredentials - [HeldCredentials](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/HeldCredentials.json) : received from issuers.

Each of these definitions has an array of [VerifiableCredential](https://github.com/ceramicstudio/datamodels/blob/main/models/verifiable-credentials/schemas/VerifiableCredential.json) stream IDs.

#### Installation

```console
$ npm install @self.id/web
$ npm install @datamodels/verifiable-credentials
```

#### Login with Wallet to DID

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

#### Storing Claims

Sample streams:

- fullName ClaimedCredential: https://documint.net/kjzl6cwe1jw148duwupih6l1fmqbncvkzquhiie407e1f5kkv5isxvjkig9u3ma
- olderThan ClaimedCredential: https://documint.net/kjzl6cwe1jw14aph1ar5pxyvtlosvfr3h109pg26ucbzqil8an1w27tkgi6x674

#### Storing Verifiable Credentials

Sample VerifiableCredentials:

- https://documint.net/kjzl6cwe1jw147ag9phrmzdcsmq2jiwhm6ijl8fa0k12v08zzmibg98tmk4jhg5

Learn more:

- [Ceramic Docs](https://developers.ceramic.network)
- [Seld.ID SDK](https://developers.ceramic.network/reference/self-id/)
- [DID DataStore](https://developers.ceramic.network/tools/glaze/did-datastore/)

### eip712-vc SDK

The [eip712-vc](https://github.com/KrebitDAO/eip712-vc) repository hosts the [Krebit] EIP712-VC tools, based on [W3C-Ethereum-EIP712-Signature-2021-Draft](https://w3c-ccg.github.io/ethereum-eip712-signature-2021-spec).

[krebit]: http://krebit.id

It provides functions for creating both W3C compliant Verifiable Credentials, and also a Solidity compatible version that can be used in contracts like [krebit-contracts].

[krebit-contracts]: https://github.com/KrebitDAO/krb-contracts

#### Installation

```console
$ npm install -s @krebitdao/eip721-vc
```

#### Configuration

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

#### Create Credential

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

#### Verify Credential

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

### Contract Subgraph

How to export Krebit reputation to web2 and web3 sites?

The [krb-subgraph](https://github.com/KrebitDAO/krb-subgraph) repository hosts the KRB Token subgraph, based in [OpenZeppelin subgraphs], that can be used to easily query the KRB token balances of a user, as well as all the Verified Credentials and status from both an Ethereum Address, as well as an issuer or credential subject DID.

[openzeppelin subgraphs]: https://docs.openzeppelin.com/subgraphs/0.1.x/

#### Setup

You can use various GraphQL Client libraries to query the subgraph and populate your app with the data indexed by The Graph from the events originated in the KRB token Ethereum transactions.

?> Check The Graph documentation for [Querying from an Application](https://thegraph.com/docs/en/developer/querying-from-your-app/).

#### Polygon URLs

Mainnet: https://thegraph.com/hosted-service/subgraph/krebit/krb-matic-v1

Mumbai (testnet): https://thegraph.com/hosted-service/subgraph/krebit/krb-mumbai-v01

#### Query examples

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
