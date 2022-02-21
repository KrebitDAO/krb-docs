# How to Issue credentials

## Requirements

There is a Minimum KRB balance to become a verifier/issuer. Only members with verified identities (as per a minimum balance of KRB to issue) can validate attestations. When a verifier provides judgement, they gain trust by performing proper due diligence and would presumably be replaced for issuing faulty judgments.

## Verifying Credentials

Flow of attestations:

1. User creates a claim record on Seld.Id. The Claim will be saved in Ceramic’s Seld.Id decentralized protocol, encrypted with the user’s DID identity.

2. User posts claim url to any market of attestators (i.e. krebit Deals)

3. An attestator is matched with some criteria (escrow cost/KRB balance)

4. User adds attestator as recipient of encrypted claim data

5. Attestator validates the data, signs the Verifiable Credential and sends attestation

6. User receives and registers the attestation to the claims record in the Krebit-Token contract

7. Both User and Attestator are rewarded based on formula

An Attestation has the following minimum attributes:

- Verifier/Issuer — responsible for assessing whether the claim is true or false.

- Claim ID: the URL of a Claim specification on IPFS/Ceramic

- Trust Level %: represents how much the Issuer believes that the claim from the subject is valid or true.

- Stake KRB: is used to help discourage spamming of fake Verifiable Credentials and provide a mechanism whereby the creator can be punished for bad behaviour.

Both Claim and Attestations schemas will be available for any app to be able to read and update the user records.

Verifiable Credentials can be revoked by an Issuer, they can expire, and also be deleted by the credential subject. In all these cases both the issuer and subject rewards are also burnt.
