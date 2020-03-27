# evan.network DID Method Specification

| Name                          | Author                           | Version |
|-------------------------------|----------------------------------|---------|
| evan.network DID method specification | Sebastian Wolfram, Philip Kaiser | 0.9     |

evan.network is a blockchain for digitalization and automation of business transactions. The network members create digital twins for their machines and products and develop standards for cross-company transactions. The open technology allows integration into existing business models. evan.network guarantees 100% reliable and permanently secured information.

This document specifies the DID method for evan.network, including how an evan.network DID is assembled, several sample documents, details about CRUD operations on DID, as well as security and privacy considerations, as required by the W3C DID specification. For more information regarding DID, please refer to the [W3C DID working draft.](https://w3c.github.io/did-core/#introduction)

## Method-specific DID syntax

The following ABNF defines the evan.network-specific DID scheme:

```ABNF
evan-did = "did:evan:" evan-specific-identifier
evan-specific-identifier = [ evan-network ":" ] evan-identifier
evan-network = "core" / "testcore"
evan-identifier = evan-account / evan-asset
evan-account = "0x"20*HEXDIG
evan-asset = "0x"32*HEXDIG
```
evan.network DIDs can point to either the core network or the testcore network. If no network is specified, "core" is assumed.

evan identifiers are lowercased hex-encoded strings. There are two types of identifiers differing in their length and the properties of the entity behind the address in the network. *Account* DIDs have a length of 20 bytes while *asset* DIDs have a length of 32 bytes. The evan identifier is the address that is also used on the evan.network blockchain to reference accounts and assets.


## CRUD Operations

### Create
An evan.network DID is depending on the type of the identity. There are three types of identities:<br>

**Self-sovereign Identity (SSI)**<br>
Self-sovereign identities (SSIs) can either be a company, a device, or a user. These identities are created, owned, and managed by a person via an externally owned account/private key. They have their own means of authentication, signing and can issue verified credentials. Changes to the DID document itself can only be performed by the owner or another SSI referenced via the `controller` property of the DID document. Alongside this, each DID document holds a proof which is generated using the owner's key. SSIs fully feature key revocation and key rotation.

The following sample shows an SSI's DID document. The SSI provides their own means of authentication by providing a public key.
```js
{
  "did": {
    "@context": "https://w3id.org/did/v1",
    "id": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906",
    "publicKey": [
      {
        "id": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1",
        "type": "Secp256k1VerificationKey2018",
        "controller": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906",
        "ethereumAddress": "0xcd5e1dbb5552c2baa1943e6b5f66d22107e9c05c"
      }
    ],
    "authentication": [
      "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1"
    ],
    "created": "2020-03-24T08:24:54.398Z",
    "updated": "2020-03-24T08:24:57.546Z",
    "proof": {
      "type": "EcdsaPublicKeySecp256k1",
      "created": "2020-03-24T08:24:57.556Z",
      "proofPurpose": "assertionMethod",
      "verificationMethod": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1",
      "jws": "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NkstUiJ9.eyJpYXQiOjE1ODUwMzgyOTcsImRpZERvY3VtZW50Ijp7IkBjb250ZXh0IjoiaHR0cHM6Ly93M2lkLm9yZy9kaWQvdjEiLCJpZCI6ImRpZDpldmFuOnRlc3Rjb3JlOjB4MGQ4NzIwNGMzOTU3ZDczYjY4YWUyOGQwYWY5NjFkM2M3MjQwMzkwNiIsInB1YmxpY0tleSI6W3siaWQiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYja2V5LTEiLCJ0eXBlIjoiU2VjcDI1NmsxVmVyaWZpY2F0aW9uS2V5MjAxOCIsImNvbnRyb2xsZXIiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYiLCJldGhlcmV1bUFkZHJlc3MiOiIweGNkNWUxZGJiNTU1MmMyYmFhMTk0M2U2YjVmNjZkMjIxMDdlOWMwNWMifV0sImF1dGhlbnRpY2F0aW9uIjpbImRpZDpldmFuOnRlc3Rjb3JlOjB4MGQ4NzIwNGMzOTU3ZDczYjY4YWUyOGQwYWY5NjFkM2M3MjQwMzkwNiNrZXktMSJdLCJjcmVhdGVkIjoiMjAyMC0wMy0yNFQwODoyNDo1NC4zOThaIiwidXBkYXRlZCI6IjIwMjAtMDMtMjRUMDg6MjQ6NTcuNTQ2WiIsInNlcnZpY2UiOlt7ImlkIjoiZGlkOmV2YW46dGVzdGNvcmU6MHgwZDg3MjA0YzM5NTdkNzNiNjhhZTI4ZDBhZjk2MWQzYzcyNDAzOTA2I3JhbmRvbVNlcnZpY2UiLCJ0eXBlIjoicmFuZG9tU2VydmljZS00MDA4MTY0NDIiLCJzZXJ2aWNlRW5kcG9pbnQiOiJodHRwczovL29wZW5pZC5leGFtcGxlLmNvbS80MDA4MTY0NDIifV19LCJpc3MiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYifQ.B4AWY0qTnWmrWa9KJjtDUbWMcGXNITFCJY9xcYhJ5XZi7zY6EJCcVBPwKNBp25mJ3A1z43g5vgaykJNj7XnsQQA"
    } 
  }
}  
```

**Asset Identity**<br>
Asset identities, digital twins, or simply assets, are created and owned by a SSI and have no own means of authentication. The owner of the asset is also the `controller` of the DID document and is exclusively allowed to make changes to the it. The owner is also registered with the identity via the `authentication` field of the DID document and is thus allowed to authenticate in its name.

The following sample shows an asset identity's DID document. The asset has no `authentication` means and only points to its owner's SSI which is delegated to authenticate in this asset identity's name, as well as apply changes to the DID document (via the `controller` property).

```js
{ '@context': 'https://w3id.org/did/v1',
  id:
   'did:evan:testcore:0xd2537e2ec4a554b1b1d224fe42a81bd7a571e307e3a10863ca447fba261fc388',
  controller:
   'did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906',
  authentication:
   [ 'did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1' ],
  created: '2020-03-24T08:31:33.644Z',
  updated: '2020-03-24T08:31:33.644Z',
  proof:
   { type: 'EcdsaPublicKeySecp256k1',
     created: '2020-03-24T08:31:33.750Z',
     proofPurpose: 'assertionMethod',
     verificationMethod:
      'did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1',
     jws:
      'eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NkstUiJ9.eyJpYXQiOjE1ODUwMzg2OTMsImRpZERvY3VtZW50Ijp7IkBjb250ZXh0IjoiaHR0cHM6Ly93M2lkLm9yZy9kaWQvdjEiLCJpZCI6ImRpZDpldmFuOnRlc3Rjb3JlOjB4ZDI1MzdlMmVjNGE1NTRiMWIxZDIyNGZlNDJhODFiZDdhNTcxZTMwN2UzYTEwODYzY2E0NDdmYmEyNjFmYzM4OCIsImNvbnRyb2xsZXIiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYiLCJhdXRoZW50aWNhdGlvbiI6WyJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYja2V5LTEiXSwiY3JlYXRlZCI6IjIwMjAtMDMtMjRUMDg6MzE6MzMuNjQ0WiIsInVwZGF0ZWQiOiIyMDIwLTAzLTI0VDA4OjMxOjMzLjY0NFoifSwiaXNzIjoiZGlkOmV2YW46dGVzdGNvcmU6MHgwZDg3MjA0YzM5NTdkNzNiNjhhZTI4ZDBhZjk2MWQzYzcyNDAzOTA2In0.qIO4j_d5pMV_1eidi1XQdaJUGk5C0rhVFpBVclfhf1ZzWfSG34SBNFSkYlZmX0T39yPwL7vAOiG71Rv0HJfRYAA' } 
}
```

**External Reference Identities**<br>
In evan.network you can create skeleton identities that reference external ressources. This allows you to create verifications over ressources or entities outside of the blockchain. These identities can be addressed via their DID just like every other identity and are managed like an SSI.

### Read / Resolve

To resolve a DID to a DID document on-chain, you can query the DID resolver. For a given valid DID the according DID document is then retrieved automatically.
If you want to retrieve a DID document for an evan.network DID outside the evan.network blockchain, you can use the evan.network DID Resolver REST service at
```
https://testcore.evan.network/did/{did}
```
You can find a working sample [here](https://testcore.evan.network/did/did:evan:testcore:0x126E901F6F408f5E260d95c62E7c73D9B60fd734)
We also provide a very lean [node package](https://github.com/evannetwork/did-resolver) to consume the REST service.

### Update
Changes to DID documents can be done via the evan.network UI or the [evan.network API](https://github.com/evannetwork/api-blockchain-core).
Allowed updates include changing the identity's controller, and adding and removing delegates.
It is also possible to add and remove service endpoints that are to be associated with your DID.
You must be either the owner or the controller of the identity to apply modifications and updates can only be performed by SSIs.

```javascript
{
  "did": {
    "@context": "https://w3id.org/did/v1",
    "id": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906",
    "publicKey": [
      {
        "id": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1",
        "type": "Secp256k1VerificationKey2018",
        "controller": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906",
        "ethereumAddress": "0xcd5e1dbb5552c2baa1943e6b5f66d22107e9c05c"
      }
    ],
    "authentication": [
      "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1"
    ],
    "created": "2020-03-24T08:24:54.398Z",
    "updated": "2020-03-24T08:24:57.546Z",
    "proof": {
      "type": "EcdsaPublicKeySecp256k1",
      "created": "2020-03-24T08:24:57.556Z",
      "proofPurpose": "assertionMethod",
      "verificationMethod": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#key-1",
      "jws": "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NkstUiJ9.eyJpYXQiOjE1ODUwMzgyOTcsImRpZERvY3VtZW50Ijp7IkBjb250ZXh0IjoiaHR0cHM6Ly93M2lkLm9yZy9kaWQvdjEiLCJpZCI6ImRpZDpldmFuOnRlc3Rjb3JlOjB4MGQ4NzIwNGMzOTU3ZDczYjY4YWUyOGQwYWY5NjFkM2M3MjQwMzkwNiIsInB1YmxpY0tleSI6W3siaWQiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYja2V5LTEiLCJ0eXBlIjoiU2VjcDI1NmsxVmVyaWZpY2F0aW9uS2V5MjAxOCIsImNvbnRyb2xsZXIiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYiLCJldGhlcmV1bUFkZHJlc3MiOiIweGNkNWUxZGJiNTU1MmMyYmFhMTk0M2U2YjVmNjZkMjIxMDdlOWMwNWMifV0sImF1dGhlbnRpY2F0aW9uIjpbImRpZDpldmFuOnRlc3Rjb3JlOjB4MGQ4NzIwNGMzOTU3ZDczYjY4YWUyOGQwYWY5NjFkM2M3MjQwMzkwNiNrZXktMSJdLCJjcmVhdGVkIjoiMjAyMC0wMy0yNFQwODoyNDo1NC4zOThaIiwidXBkYXRlZCI6IjIwMjAtMDMtMjRUMDg6MjQ6NTcuNTQ2WiIsInNlcnZpY2UiOlt7ImlkIjoiZGlkOmV2YW46dGVzdGNvcmU6MHgwZDg3MjA0YzM5NTdkNzNiNjhhZTI4ZDBhZjk2MWQzYzcyNDAzOTA2I3JhbmRvbVNlcnZpY2UiLCJ0eXBlIjoicmFuZG9tU2VydmljZS00MDA4MTY0NDIiLCJzZXJ2aWNlRW5kcG9pbnQiOiJodHRwczovL29wZW5pZC5leGFtcGxlLmNvbS80MDA4MTY0NDIifV19LCJpc3MiOiJkaWQ6ZXZhbjp0ZXN0Y29yZToweDBkODcyMDRjMzk1N2Q3M2I2OGFlMjhkMGFmOTYxZDNjNzI0MDM5MDYifQ.B4AWY0qTnWmrWa9KJjtDUbWMcGXNITFCJY9xcYhJ5XZi7zY6EJCcVBPwKNBp25mJ3A1z43g5vgaykJNj7XnsQQA"
    },
    "service": [
      {
        "id": "did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906#randomService",
        "type": "randomService-400816442",
        "serviceEndpoint": "https://openid.example.com/400816442"
      }
    ]
  }
}
```

### Deactivate / Revoke
To deactivate an evan.network DID document the controller or the owner has to deactivate the DID document using the [evan.network API](https://api-blockchain-core.readthedocs.io/en/latest/profile/did.html#deactivateDidDocument).  
DID documents for deactivated DIDs can be recognized by both an empty authentication and a public key array.

## Security Considerations

| Status                          | Last update                           |
|-------------------------------|----------------------------------|
| Work in progress | 12.12.2019 |

### Key management
In general, DID document owners and controllers always are SSIs.
SSIs are controlled by a person via a private key.
When creating the SSI and the private key, a [mnemonic](https://evannetwork.github.io/docs/first_steps/create-identity.html) is created with which the private key can be restored.
Losing this private key or exposing it to other parties means losing or giving away control over the identity and thus the DID document.
However, evan.network allows to give trusted identities control over your identity to enable recovery capabilities in case of a complete loss of both the private key and the mnemonic.

### Authorization
The DID registry is responsible for both providing lookup mechanisms for existing DID documents, and storing new DID documents registered to the respective DID.
This registry is implemented via a smart contract that only allows the owner of an identity to store a DID document associated to this identity's DID.

### REST Endpoint
DIDs can be queried off-chain using the evan.network DID resolver smart agent.
This smart agent communicates via HTTPS and thus ensures transport layer security.

<!--
Mandatory to discuss in the spec:
- eavesdropping
- replay attacks
- message insertion
- deletion & modification
- man-in-the-middle
- potential denial of service attacks
- residual risks (such as the risks from compromise in a related protocol, incorrect implementation, or cipher) after threat mitigation has been deployed
- integrity protection and update authentication for all operations
- method-specific endpoint authentication
- policy mechanism by which DIDs are proven to be uniquely assigned
-->


## Privacy Considerations

| Status                          | Last update                           |
|-------------------------------|----------------------------------|
| Work in progress | 12.12.2019 |

### Identifiers
IDs and, therefore, DIDs are created in a random manner and thus do not allow an attacker to derive any patterns to inherently deduce the identity of the owner.
However, keep in mind that these identifiers are only pseudonyms and do not guarantee anonymity.

### Custom data
A DID document can contain custom payload data that the identity owner or controller can write into the document at his own discretion.
Users need to be aware of the public nature of DID documents.
Everybody can resolve every (valid) DID and receive the corresponding DID document, including all its custom payload data.
