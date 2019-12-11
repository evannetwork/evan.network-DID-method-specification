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
evan.network DIDs can point to either the core network or the testcore network. If no network is specified, "core" is assumend.

evan identifiers are lowercased hex-encoded strings. There are two types of identifiers differing in their length and the properties of the entity behind the address in the network. *Account* DIDs have a length of 20 bytes while *asset* DIDs have a length of 32 bytes. The evan identifier is the address that is also used on the evan.network blockchain to reference accounts and assets.


## CRUD Operations

### Create
An evan.network DID is depending on the type of the identity. There are three types of identities:<br>

**Self-sovereign Identity (SSI)**<br>
Self-sovereign identities (SSIs) can either be a company, a device, or a user. These identities are created, owned, and managed by a person via an externally owned account/private key and have their own means of authentication and signing and can issue verified credentials. Changes to the DID document itself can only be performed by the owner or another SSI referenced via the `controller` property of the DID document. Upon creation the `controller` property is set to the owner of the identity so that the owner has the full controll over the identity. SSIs fully feature key revocation and key rotation.

The following sample shows an SSI's DID document. The SSI provides their own means of authentication by providing a public key.
```js
{
    '@context': 'https://w3id.org/did/v1',
    id: 'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33',
    publicKey: [
        {
            id: 'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33#key-1',
            type: [ 'Secp256k1SignatureVerificationKey2018', 'ERC725ManagementKey' ],
            publicKeyHex: '0x35e97ef6169c82a0f6f0fe287a103f04103f7ccd83c8583af30ea9914d6e76a0'
        }
    ],
    authentication: [
         'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33#key-1'
    ],
    service: []
}
```

**Asset Identity**<br>
Asset identities, digital twins, or simply assets, are created and owned by a SSI and have no own means of authentication. The owner of the asset is also the `controller` of the DID document and is exclusively allowed to make changes to the it. The owner is also registered with the identity via the `authentication` field of the DID document and is thus allowed to authenticate in its name.

The following sample shows an asset identity's DID document. The asset has no `authentication` means and only points to its owner's SSI which is delegated to authenticate in this asset identity's name, as well as apply changes to the DID document (via the `controller` property).

```js
{
    '@context': 'https://w3id.org/did/v1',
    id: 'did:evan:0x071be9398b832d711b2e6278eba372ec968d8073e33a9cac9289c3ed06fc0701',
    controller: 'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33',
    authentication: [
        'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33#key-1'
    ],
    service: []
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
We also provide a very lean [node package](https://github.com/evannetwork/did-resolver) to consume the REST service.

### Update
Changes to DID documents can be done via the evan.network UI or the [evan.network API](https://github.com/evannetwork/api-blockchain-core).
Allowed updates include changing the identity's controller, and adding and removing delegates.
It is also possible to add and remove service endpoints that are to be associated with your DID.
You must be either the owner or the controller of the identity to apply modifications and updates can only be performed by SSIs.

```javascript
{
    '@context': 'https://w3id.org/did/v1',
    id: 'did:evan:0x071be9398b832d711b2e6278eba372ec968d8073e33a9cac9289c3ed06fc0701',
    controller: 'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33',
    authentication: [
        'did:evan:0xb9A7884DEf4F8E503C50068dCB03d2393cBDFA33#key-1'
    ],
    service: [{
        "id": "did:evan:0x071be9398b832d711b2e6278eba372ec968d8073e33a9cac9289c3ed06fc0701#messaging",
        "type": "MessagingService",
        "serviceEndpoint": "https://message.me.example.com/"
    }]
}
```

### Deactivate / Revoke
To deactivate a evan.network DID document the controller or the owner has to delete the DID document using the [evan.network API](https://github.com/evannetwork/api-blockchain-core).

## Security Considerations

TBD
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

TBD
