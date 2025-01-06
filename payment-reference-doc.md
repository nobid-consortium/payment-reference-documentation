# A EUDI way to pay - electronic payments enabled by the European Digital Identity Wallet

Strong Customer Authentication (SCA) is a security protocol required under the European Union’s PSD2 directive to protect electronic payments. Its purpose is to reduce fraud by ensuring that electronic transactions are conducted securely and only by authorized users. SCA relies on a combination of two out of three independent authentication factors:

1. Something the customer knows, such as a password or PIN.
2. Something the customer possesses, such as a smartphone, card reader, or token.
3. Something the customer is, like a fingerprint, facial recognition, or other biometric data.

The process requires that these factors are independent of one another, ensuring a higher level of security for online payments. While some payments, such as recurring subscriptions or low-value transactions, may be exempt from SCA, most will trigger its implementation.

When a customer initiates a transaction, their payment provider determines whether SCA is necessary. If it is, the customer will authenticate the payment using the required factors. Once authenticated, the bank or payment service verifies the information before approving the transaction.

**Dynamic linking** is a crucial component of SCA, particularly for transactions involving specific payees. It ensures that authentication is directly tied to the transaction details, such as the recipient and payment amount, and that these details are protected from tampering.

For dynamic linking, the customer must be shown the relevant payment details—such as the payee and amount—at the time of authentication. A unique code is then generated for the transaction. This code is dynamically linked to the transaction’s details, meaning that any attempt to alter the amount or recipient will invalidate the code, and the payment will not be processed.

For example, if a customer authorizes a payment of €100 to a specific vendor, the system generates an authentication code tied to this exact transaction. If an attacker tries to intercept and modify the transaction to pay €500 instead, the code becomes invalid, and the bank will decline the payment. This ensures that the transaction cannot be tampered with during the process.

When a payment is initiated, SCA ensures the transaction is secure through multi-factor authentication. Dynamic linking further secures the payment by binding the transaction details to the authentication process. The customer authenticates using their credentials, which could include biometrics or a PIN, while reviewing the transaction details. The system then verifies both the customer and the specific payment details. If everything checks out, the transaction is approved; if anything is altered, the system automatically flags and rejects the payment.

Dynamic linking enhances transparency for customers by displaying transaction-specific details during authentication and adds a critical layer of protection against fraud by ensuring that any alteration in payment data invalidates the authorization. Together, SCA and dynamic linking significantly improve the security of electronic payments while ensuring compliance with regulatory requirements.

### eIDAS 2.0

eIDAS 2.0 (Electronic Identification, Authentication, and Trust Services) is an updated framework by the European Union to enhance secure digital identity and trust services across member states. Building on the original eIDAS regulation (2014), eIDAS 2.0 introduces the European Digital Identity Wallet, a standardized and interoperable digital identity tool that empowers individuals and businesses to authenticate their identity, sign documents, and access online services securely across the EU.

The European Identity Wallet is a mobile or digital application provided by EU member states. It allows citizens to store and manage their verified personal information, credentials, and certificates securely. Users can employ the wallet for various purposes, including accessing government services, sharing ID details, and performing secure electronic transactions.

In the context of electronic payments and Strong Customer Authentication, the regulation obliges European payment service providers (PSP) to accept the European Identity Wallet (EUDIW) as a secure mean for authentication providing:

2. **Multi-Factor Authentication:** The wallet can integrate with biometrics (e.g., facial recognition or fingerprints) and possessive factors (e.g., a secure digital key), fulfilling the two-factor authentication mandate for SCA.
3. **Dynamic Linking:** When processing a payment, the wallet ensures dynamic linking by displaying transaction-specific details, such as the recipient and payment amount, and cryptographically linking them to the transaction.

Integrating payment and SCA processes into the wallet may offer a high level of security and interoperability across the EU fostering trust and efficiency in cross-border electronic transactions.

Further details regarding acceptance of the wallet for SCA is discussed within [this paper](https://lange-hausstein.de/wp-content/uploads/2023/11/231120_DP-No.-1_EUDIW-for-SCA_EN_public.pdf). 

### Architecture Reference Framework

The Architecture Reference Framework (ARF)[^arf] is a foundational document developed under the eIDAS 2.0 initiative to guide the implementation of the European Digital Identity Wallet. It provides a structured framework to ensure interoperability, security, and trust throughout the lifecycle of digital identity interactions, particularly when relying parties (e.g., service providers) interact with the wallet.

The ARF emphasizes:

*	Trust Establishment and Maintenance: Ensuring that all entities interacting with the wallet, such as identity issuers and relying parties, operate within a secure and trusted ecosystem.
*	Interoperability: Facilitating seamless interactions across EU member states by standardizing communication protocols and ensuring cross-border compatibility.
*	User-Centric Design: Enabling users to maintain control of their data while ensuring security and privacy.

A key aspect of the ARF[^arf] is managing trust throughout the lifecycle of a relying party, ensuring that entities like banks, government portals, or e-commerce sites can reliably interact with the wallet to authenticate users, verify Electronic Attestations of Attributes (EAA, see ARF Annex 1[^arf_annex1]), and process transactions.

The European Identity Wallet relies on modern, standardized protocols to manage the secure issuance and presentation of attestations.

**OpenID4VCI** for securely issuing verifiable credentials to a user’s digital wallet, enabling cryptographically protected, tamper-proof storage and future use of those credentials for authentication or sharing.

**OpenID4VP** for enabling users to securely present verifiable attestation from their digital wallet to relying parties, ensuring cryptographic verification and selective disclosure of information.
 
This document focuses on presenting a structured approach to leverage the existing technical standards alongside the eIDAS 2.0 trust infrastructure. The goal is not only to develop and standardize a solution for Strong Customer Authentication (SCA) and electronic payments utilizing the European Identity Wallet but also to integrate payments with other forms of attestations as referenced in ARF, section A.2.3.18 Topic 18 [^arf_annex2]. By doing so, it seeks to unlock the vast potential of the eIDAS ecosystem, enabling seamless interoperability, enhanced trust, and innovative use cases that extend beyond traditional payment processes.

## Terminology

This document defines the following terms:

- **ASPSP**: Account Servicing Payment Service Provider managing payment accounts of the holder.
- **TPP**: Third Party Payment Service Provider or Merchant requesting and processing payments.
- **PSP**: Payment Service Provider. Depending on the context, both ASPSP and TPP can be  referred to as payment service provider. 
- **A2Pay**: SD-JWT VC credential / attestation issued by an ASPSP to a holder referencing an account for payments.  
- **P2Pay**: Presentation of the A2Pay SD-JWT VC credential that is used to authorize a payment.


## Registration
Before the wallet can be used to authorize payments, a registration process must be completed. The registration process, which typically occurs as a one-time setup, establishes a link between the user’s wallet instance and their Account Servicing Payment Service Provider (ASPSP) responsible for managing the payment account. This process is crucial as it allows the ASPSP to authenticate the wallet and verify its legitimacy, thereby establishing a secure and trusted relationship between the two entities. The details of this verification process and the mechanisms for building this trust are further outlined in the Architecture Reference Framework (ARF), Section 6.6.2[^arf].

During the registration process, the ASPSP, also referred to as the issuer, issues dedicated Electronic Attestations of Attributes (EAA) required to facilitate payments and SCA (called Attestation to Pay or A2Pay hereafter) to the wallet. This is achieved using the OpenID4VCI protocol [^openid4vci] in accordance with the OpenID4VC High Assurance Interoperability Profile (HAIP) with SD-JWT VC (Selective Disclosure JSON Web Token Verifiable Credential) [^openid4vc_hip]. OpenID4VCI enables the issuer to issue the A2Pay within an authenticated and authorized context. While the exact method of establishing this context is determined by the issuer and is outside the scope of this document, it can be achieved through mechanisms such as:

* Login credentials (e.g., Online Banking credentials),
* One-Time Passwords (OTPs),
* Two-Factor Authentication (2FA) apps, including proprietary banking applications.

As outlined in ARF sections 6.6.6.3 and 6.6.3.7 [^arf] as well as Annex 2, Section A.2.3.9 Topic 9 [^arf_annex2], the issued A2Pay is cryptographically tied to a private key safeguarded by the wallet’s Secure Cryptographic Applications (WSCA) and Secure Cryptographic Devices (WSCD). 

The private key serves as the primary element for implementing Strong Customer Authentication (SCA) within the wallet. Its security is based on the following assumptions:

1.	Binding to the Wallet’s Secure Cryptographic Device (WSCD):
The private key used to sign payment transactions is securely bound to the WSCD of the wallet and cannot be extracted. This ensures that the private key inherently provides the possession factor required for SCA, as it is uniquely tied to the holder’s wallet.
2.	Access Protection:
Using the private key for signing is always safeguarded by an additional authentication layer, which may include:
    - A PIN, known only to the holder, providing the knowledge factor for SCA, or
    - Biometric verification, such as fingerprint or facial recognition, fulfilling the inherence factor for SCA.

These security measures collectively ensure that the private key remains protected and can only be used by the authorized wallet holder, thus meeting the multi-factor requirements of SCA.

See also requirement specification ARF Annex 2 A.2.3.6 Topic 6 - User Approval RBA10[^arf_annex2] for details. 

### A2Pay - Attestation to pay

The A2Pay (Attestation to Pay) serves as a key component for identifying the wallet holder’s Payment Service Provider (PSP) and the specific account to which the attestation is linked to. An A2Pay defines the following claims:

* `id` REQUIRED: Unique identifier of the issued A2Pay.
* `psp` OPTIONAL: Identifier of the PSP if different from `iss`. The value must be a case-sensitive URL using the HTTPS scheme that contains scheme, host and, optionally, port number and path components, but no query or fragment components.
* `payment-product` OPTIONAL: Payment product or scheme the account is connected 
to (e.g. sct-eu, sct-inst-eu, domestic such as bancomat-pay), that a TPP may use to process the payment.
* `name` OPTIONAL: Name of the account the A2Pay is connected to.

The structure and detailed specifications of the A2Pay are also defined in the JSON schema file [a2pay-schema.json](a2pay-schema.json), which outlines all required attributes and their formats.

Non-normative example of an A2Pay payload:

```json
{
    "_sd": [],
    "iss": "https://bank.example.com/issuer",
    "psp": "https://bank.example.com/issuer",
    "exp": 1883000000,
    "nbf": 1718198433,
    "iat": 1718198433,
    "vct": "https://credentials.example.com/a2pay",
    "_sd_alg": "sha-256",
    "sub": "DE75512108001245126199",
    "id": "8D8AC610-566D-4EF0-9C22-186B2A5ED793",
    "payment-product": "sct-inst-eu",
    "name": "My Account",
    "cnf": {
      "jwk": {
        "crv": "P-256",
        "kty": "EC",
        "x": "NASJ2ADuagOvraLf7O4VxcBMbantzL9dd0jpvMLnBfs",
        "y": "OJY6pqCqRIzpEt78OXasWHGgqV5ZGre_3cHtpNH82gg"
        }
    }
}
```

Non-normative example of an A2Pay as `sd-jwt-vc` according to SD-JWT-based Verifiable Credentials[^sd-jwt-vc]

```
eyJ0eXAiOiJzZCtqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJodHRwczovL2JhbmsuZXhhbXBsZS5jb20vaXNzdWVyIiwicHNwIjoiaHR0cHM6Ly9iYW5rLmV4YW1wbGUuY29tL2lzc3VlciIsImV4cCI6MTg4MzAwMDAwMCwibmJmIjoxNzE4MTk4NDMzLCJpYXQiOjE3MTgxOTg0MzMsInZjdCI6Imh0dHBzOi8vY3JlZGVudGlhbHMuZXhhbXBsZS5jb20vYTJwYXkiLCJfc2RfYWxnIjoiU0hBLTI1NiIsInN1YiI6IkRFNzU1MTIxMDgwMDEyNDUxMjYxOTkiLCJpZCI6IjhEOEFDNjEwLTU2NkQtNEVGMC05QzIyLTE4NkIyQTVFRDc5MyIsInBheW1lbnQtcHJvZHVjdCI6InNjdC1pbnN0LWV1IiwibmFtZSI6Ik15IEFjY291bnQiLCJjbmYiOnsiandrIjp7ImNydiI6IlAtMjU2Iiwia3R5IjoiRUMiLCJ4IjoiTkFTSjJBRHVhZ092cmFMZjdPNFZ4Y0JNYmFudHpMOWRkMGpwdk1MbkJmcyIsInkiOiJPSlk2cHFDcVJJenBFdDc4T1hhc1dIR2dxVjVaR3JlXzNjSHRwTkg4MmdnIn19fQ.sMIO3_5xPqVWC-4dAl4y2GxWluHY5V5xoCXpkPfeFOReCrczkn4aSyN90wzpsRuYfoyGBlOgcNbaBMuWik0kDw~
```

#### PSP Metadata 

PSP Metadata may contain information on the PSP's technical capabilities and/or  supported payment rails. The PSP may publish metadata by making a JSON document available at the path formed by concatenating the string `/.well-known/psp` to the Credential Issuer Identifier `iss` or the PSP Identifier `psp` if present. If the value contains a path component, any terminating `/` must be removed before appending `/.well-known/psp`. 

To fetch the PSP Metadata, the requester must send an HTTP request using the GET method and the path formed following the steps above. The PSP must return a JSON document compliant with this specification using the `application/json` media type and the HTTP Status Code 200.

This document defines the following PSP metadata properties:

* `bic` OPTIONAL: BIC of the PSP
* `payment-products` OPTIONAL: Object containing a list of name/value pairs, where each name is a unique identifier of the supported `payment-product` being described. The value is an object that contains metadata about a specific payment product and must at least contain a `name` property. It may contain additional properties related to the specific payment product.

Non-normative example of a PSP's metadata:

```json
{
  "bic":  "COLSDE33",
  "payment-products": {  
      "sct-inst-eu": {
        "name": "SEPA Inst",
        "payment_uri": "https://bank.example.com/a2pay",
        "payment_status_uri" : "https://bank.example.com/a2pay/status"
      }
    }
  }
}
```

Example of the HTTP GET request to retrieve the PSP metadata:

```
GET /.well-known/psp HTTP/1.1
Host: https://bank.example.com/
```

Example of the HTTP GET response to the PSP meta data request:
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "bic":  "COLSDE33",
  "payment-products": {  
      "sct-inst-eu": {
        "name": "SEPA Inst",
        "payment_uri": "https://bank.example.com/a2pay",
        "payment_status_uri" : "https://bank.example.com/a2pay/status"
      }
    }
  }
}
```

Additional PSP metadata properties may also be used.

## Payment

The process of authenticating and authorizing a payment is carried out by presenting the A2Pay to the Payment Service Provider (PSP) using the OpenID4VP protocol [^openid4vp]. This is implemented following Section 5 of the OpenID4VC High Assurance Interoperability Profile (HAIP)[^openid4vc_hip], which implies the following:

1.	Credential Format: As credential format for the A2Pay SD-JWT VC must be used[^sd-jwt-vc]. The presentation of an A2Pay towards a PSP will henceforth be referred to as **P2Pay**. 
2.	Delivery Mechanism: The P2Pay is transmitted to the PSP using an HTTP POST request with the `response_mode` set to `direct_post.jwt`. Details regarding the payment initiation endpoint and how the HTTP POST request is structured can be found in the [A2Pay API specification](a2pay-api.yml) and in Section 7.3.1 of OpenID4VP [^openid4vp].
3.	Response Handling by PSP: Upon successful receipt and processing of the Authorization Response, the PSP responds with an `HTTP 200` status. The response includes a JSON object containing following properties:
    * `redirect_uri` REQUIRED: The wallet must follow this redirect URI to allow the PSP to continue interacting with the user.
    * `payment_id` OPTIONAL: Unique identifier for the payment.

The required trust relations between the wallet and the PSP during this process is established as outlined in ARF Section 6.6.3 [^arf], which elaborates on the mechanisms for mutual authentication and the secure exchange of information. 


### Dynamic linking

To fulfill the requirement for dynamic linking, the P2Pay must include the transaction details of the payment (referred to as payment request), signed by the wallet. This ensures that the authentication process is securely tied to the specific payment transaction, as mandated by PSD2.

The OpenID4VP protocol [^openid4vp] supports the inclusion of dynamic transaction data in the authorization process using the `transaction_data` parameter. The flow operates as follows:

1.	The relying party includes the transaction-specific details (e.g., payment amount, payee information) in the authorization request using the `transaction_data` parameter.
2. The wallet processes the `transaction_data` values and computes a cryptographic hash of this data. This hash is embedded into the key-binding JWT of the P2Pay, which is then sent back to the PSP as part of the authorization response.
3.	Authentication Code Generation: The computed hash serves as the authentication code required under PSD2 for dynamic linking, tieing the authorization response to the specific payment transaction details.
4.	User Approval Dialogue: To ensure transparency and user consent, the payment request details must be presented to the user within the wallet’s approval interface. This allows the user to review and approve the specific payment details. The user approval dialogue must comply with the requirements specified in ARF Section 6.6.3.4 [^arf] and ARF Annex 2, Section A.2.3.6 Topic 6 [^arf_annex2].

This process ensures compliance with PSD2’s requirement for dynamic linking by securely binding the transaction details to the authentication process. By incorporating the transaction hash into the key-binding JWT of the P2Pay, the wallet guarantees that any tampering with the transaction data invalidates the authentication.

### Payment request object

The payment request object is included within the `transaction_data` array and contains all the details describing the intended payment transaction. The data schema for the payment request is specified in the JSON schema file, [payment-request-schema.json](payment-request-schema.json). 

Non-normative example of a payment request:

```json
{
    "payment-id": "45cc64f0-7190-4e3c-81e6-e791565be643",
    "creditor-account": {
      "iban": "DE75512108001245126199"
    },
    "instructed-amount": "15.49",
    "currency": "EUR",
    "creditor-name": "Merchant A",
    "purpose": "Shopping at Merchant A"
}
```

Based on the OpenID4VP [^openid4vp] section 5.1, additional properties must be appended to the payment request object before performing base64url encoding to ensure its proper inclusion as a transaction data object within the `transaction_data` array.

* `type`
* `credential_ids`
* `transaction_data_hashes_alg`

```json
{
  "type": "PaymentRequest",
  "credential_ids": ["A2Pay"],
  "transaction_data_hashes_alg": "sha-256",
  "payment-id": "45cc64f0-7190-4e3c-81e6-e791565be643",
  "creditor-account": {
    "iban": "DE75512108001245126199"
  },
  "instructed-amount": "15.49",
  "currency": "EUR",
  "creditor-name": "Merchant A",
  "purpose": "Shopping at Merchant A"
}
```

Non-normative example of the `transaction_data` parameter within the authorization request:

```json
{
  "transaction_data": ["ewogICJ0eXBlIjogIlBheW1lbnRSZXF1ZXN0IiwKICAiY3JlZGVudGlhbF9pZHMiOiBbIkEyUGF5Il0sCiAgInRyYW5zYWN0aW9uX2RhdGFfaGFzaGVzX2FsZyI6ICJzaGEtMjU2IiwKICAicGF5bWVudC1pZCI6ICI3RDhBQzYxMC01NjZELTNFRjAtOUMyMi0xODZCMkE1RUQ3OTMiLAogICJjcmVkaXRvci1hY2NvdW50IjogewogICAgImliYW4iOiAiREU3NTUxMjEwODAwMTI0NTEyNjE5OSIKICB9LAogICJpbnN0cnVjdGVkLWFtb3VudCI6ICIxNS40OSIsCiAgImN1cnJlbmN5IjogIkVVUiIsCiAgImNyZWRpdG9yLW5hbWUiOiAiTWVyY2hhbnQgQSIsCiAgInB1cnBvc2UiOiAiU2hvcHBpbmcgYXQgTWVyY2hhbnQgQSIKfQ=="],
  ...
}

```

### Payment status

Wallets and TPPs may use the `payment_status_uri` given in the [PSP metadata](#psp-metadata) to retrieve the status of a payment. 

The enpoint must adhere to the payment status endpoint specified in the [A2Pay API specification](a2pay-api.yml). The payment status is represented using a subset of the payment status code defined in [ISO 20022](https://www.iso20022.org/catalogue-messages/additional-content-messages/external-code-sets).

| Type |               Name              |                                                            Explanation                                                           |
|:----:|:-------------------------------:|:--------------------------------------------------------------------------------------------------------------------------------:|
| RCVD | Received                        | The payment has been received and is awaiting Strong Customer Authentication |
| ACCP | AcceptedCustomerProfile         | The preceding check of technical validation was successful. The customer profile check was also successful. |
| ACSP | Accepted Settlement In Progress | The payment has been sent by the bank but is not yet settled in the creditor account.                                            |
| **ACSC** | Accepted Settlement Completed   | The payment has been sent by the bank and settled in the creditor's account.                                                     |
| **NAUT** | NotAuthorized                   | The end-user has cancelled the payment authorization |
| **RJCT** | Rejected                        | The payment has failed due to insufficient funds in the debtor's account                                                         |
| PDNG | Pending                         | The debtor account holder has edited the payment in their online banking and a new Strong Customer Authentication is now pending |
| **CANC** | Cancelled                       | The payment has been deleted by the end-user |
| PRSY | OnHold                          | The payment initiation was put on hold by the bank                                                                               |
| PATC | Partially Accepted              | The payment requires a second authorization |

**Final status in* **bold** 

Example of a HTTP GET request for the payment status:

```
GET /payment-status/37baddc0-78e7-430f-8567-f4bb48b7e493/38f3fc68-7c57-49d6-9318-1eba22098e56 HTTP/1.1
Host: https://bank.example.com/
```

Example of a payment status HTTP GET response:

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "status-code": "ACSC"
}

```

With respect to *instant* payments, it is recommended for the wallet to implement an appropriate polling mechanism.  For example, the wallet may poll the status of a payment until a final status is reached but no longer than 30 seconds. Beyond that, the wallet may poll the status only if triggered by a specific user action like pressing a button or updating a transaction overview interface e.g..

### Verification

The successfull verification of the P2Pay signals the PSP to proceed with executing the authorized payment transaction. The verification is done following the guidlines given in Selective Disclosure for JWT[^sd-jwt] section 7 and specifically 7.3 and also section 6.3 of the ARF[^arf]. To verifiy the authentication code ensuring the P2Pay is bound to the intended payment transaction, the PSP must calculate the hash of the base64url encoded transcation data object of the [payment request](payment-request-schema.json) with the chosen `transaction_data_hashes_alg`. For a successful verification, the resulting hash must equal the hash in the `transaction_data_hashes` array included in the key-binding JWT. 

### Flows 

The presentation process outlined above can be adapted to various payment use cases, depending on the specific role of the relying party requesting the P2Pay.

#### Basic PaymentAuth flow

In this flow, the relying party is the ASPSP (usually a bank) of the holder themself. The holder is requesting a payment transaction using an out-of-band mechanism like the banks mobile app, online banking portal or even a third party provider for payment initiation according to PSD2 (PISP). The ASPSP is initiating the flow by requesting the P2Pay they have previously issued to the wallet themself in the prior [registration flow](#registration). 

**This scenario applies to the obligations for PSPs to accept the wallet for SCA with regards to the eIDAS2 regulation.**

```mermaid

sequenceDiagram
    autonumber
    actor user as User<br/>aka Holder
    participant wallet as Wallet 
    participant psp as ASPSP<br/>aka Issuer & Verifier

    Note over psp: Out-of-band payment initiation
    rect rgb(100, 150, 100)
    note over wallet, psp: OpenID4VP
    psp ->> wallet: Authorization Request(transaction_data)
    activate wallet
    wallet ->> user: SHOW approval dialoge
    user ->> user: review transaction
    user ->> wallet: approves presentation
    wallet -->> psp: POST Authorization Response(P2Pay)
    psp -->> wallet: RESP OK, redirect_uri
    end
    wallet -->> user: SHOW presentation status
    deactivate wallet
    wallet ->> psp: GET PSP metadata
    psp -->> wallet: RESP PSP metadata, payment_status_uri
    psp ->> psp: verify P2Pay and execute transaction
    loop
    wallet ->> psp: GET payment-status(a2pay-id, payment-id)

    psp -->> wallet: RESP payment status 
    end
    wallet -->> user: SHOW payment status
    wallet ->> psp: GET follow redirect_uri

```

Out-of-band payment initiation:
The payment process is initiated through an out-of-band mechanism, such as a mobile app, online banking portal, or third-party provider.
1. Authorization Request Object including the `transaction_data` with the payment request. This step is is initiated either by link for a same-device flow or by QR-code for cross-device flow.
2. Wallet asks user for approval:
The wallet authenticates the TPP (ARF[^arf], section 6.6.3.2) and prompts the user to review the payment request and approve the presentation of the A2Pay.
3. User reviews the transaction:
The user evaluates the details of the transaction to ensure its accuracy and validity.
4. User approves the presentation:
After reviewing the transaction details, the user confirms the request in the wallet, granting approval to present the A2Pay (ARF[^arf], section 6.6.3.4).
5. Authorization Response sending P2Pay to ASPSP:
The wallet presents the P2Pay including the `transaction_data_hashes` along with the OpenID4VP authorization response to the ASPSP as HTTP POST request.
6. ASPSP acknowledges receipt of P2Pay:
The ASPSP confirms that the authorization response with the P2Pay has been received successfully and sends back the `redirect_uri` and the `payment_status_uri` to the wallet.
7. Wallet updates user on status presentation:
The wallet provides the user with a status update, indicating that the A2Pay has been successfully sent.
8. TPP request PSP metadata: The TPP requests the ASPSPs metadata using the PSP identifier given in the P2Pay
9. ASPSP send PSP metadata: The ASPSP provides the PSP metadata object including the `payment_status_uri` endpoint
10. ASPSP [verifies](#verification) P2Pay and executes the transaction:
The ASPSP validates the authenticity and integrity of the P2Pay. If valid, the ASPSP proceeds to execute the payment transaction.
11. Wallet queries payment status using the `payment_status_uri` given in the PSP's metadata.
12. ASPSP responds to the wallet with the payment status, indicating whether the transaction was successful or if any issues occurred.
13.	Wallet shows payment status to user:
The wallet communicates the payment status to the user, providing confirmation or detailing any errors.
14. Wallet follows `redirect_uri`	to allow the TPP to continue interacting with the user.

#### Extended PaymentAuth flow

In this flow, the relying party is a third party, such as a merchant or the merchant’s Payment Service Provider (referred to as **TPP** hereafter). TPP initiates the process by requesting an A2Pay issued by an ASPSP. Once the TPP receives the P2Pay, it must forward it, along with the original payment request object, to the ASPSP. The ASPSP then performs the necessary [verification](#verification) and executes the payment transaction.

```mermaid

sequenceDiagram
    autonumber
    actor user as User<br/>aka Holder
    participant wallet as Wallet
    participant psp2 as TPP<br/>aka Verifier
    participant psp as ASPSP<br/>aka Issuer
    rect rgb(100, 150, 100)
    note over wallet, psp2: OpenID4VP
    psp2 ->> wallet: Authorization Request(transaction_data)
    activate wallet
    wallet ->> user: SHOW approval dialoge
    user ->> user: review transaction
    user ->> wallet: approves presentation
    wallet ->> psp2: POST Authorization Response(P2Pay)
    psp2 -->> wallet: RESP OK, redirect_uri
    end
    wallet -->> user: SHOW presentation status
    deactivate wallet
    psp2 ->> psp: GET PSP metadata
    psp -->> psp2: RESP PSP metadata, payment_status_uri, payment_uri
    rect rgb(200, 100, 100)
    note over psp, psp2: implemented by payment rail<br>example using a2pay direct  
    psp2 ->> psp: POST SendPaymentAuthorizationRequest(a2pay-id)
    psp -->> psp2: RESP receiving payment_id OK 
    end
    psp ->> psp: verify P2Pay and execute transaction
    loop
    wallet ->> psp: GET payment-status(a2pay-id, payment-id)
    psp2 ->> psp: GET payment-status(a2pay-id, payment-id)
    psp -->> psp2: RESP payment-status 
    psp -->> wallet: RESP payment-status
    end
    wallet -->> user: SHOW payment status
    wallet ->> psp2: GET follow redirect_uri
```

1. Authorization Request Object including the `transaction_data` with the payment request. This step is is initiated either by link for a same-device flow or by QR-code for cross-device flow.
2.	Wallet asks user for approval:
The wallet authenticates the TPP (ARF[^arf], section 6.6.3.2) and prompts the user to review the payment request and approve the presentation of the A2Pay.
3.	User reviews the transaction:
The user evaluates the details of the payment request to ensure its accuracy and validity.
4.	User approves the presentation:
After reviewing the transaction details, the user confirms the request in the wallet, granting approval to present the A2Pay (ARF[^arf], section 6.6.3.4).
5.	Authorization Response sends P2Pay to TPP:
The wallet presents the P2Pay including the `transaction_data_hashes` along with the OpenID4VP authorisation response to TPP as HTTP POST request.
6.	TPP acknowledges receipt of P2Pay:
TPP confirms that the A2Pay has been successfully received and sends back the `redirect_uri` to the wallet.
7.	Wallet updates user on status presentation:
The wallet provides the user with a status update, indicating that the A2Pay has been successfully sent.
8. TPP request PSP metadata: The TPP requests the ASPSPs metadata using the PSP identifier given in the P2Pay
9. ASPSP send PSP metadata: The ASPSP provides the PSP metadata object including the `payment_status_uri` endpoint
10.	TPP forwards A2Pay to issuing ASPSP:
TPP sends a [payment authorization object](#payment-authorization-object) including the the P2Pay and the original payment request object to the ASPSP for verification and/or execution of the payment using a supported payment rail and/or the [A2Pay Direct endpoint](#a2pay-direct-endpoint).
11.	ASPSP acknowledges receipt of A2Pay:
ASPSP confirms receipt of the forwarded P2Pay.
12.	ASPSP [verifies P2Pay](#verification) and executes the transaction:
ASPSP validates the authenticity and integrity of the P2Pay. If valid, it executes the payment transaction.
13.	Wallet queries the [payment status](#payment-status) using the `payment_uri`, A2Pay `id` and the `payment-id`.
14.	TPP queries the [payment status](#payment-status) using the `payment_uri`, A2Pay `id` and the `payment-id`.
15.	ASPSP responds with [payment status](#payment-status) to TPP.
16.	ASPSP responds with [payment status](#payment-status) to wallet.
17.	Wallet shows payment status to user:
The wallet provides the user with the payment status, indicating whether the transaction was successful or if any issues occurred.
18. Wallet follows `redirect_uri`	to allow the TPP to continue interacting with the user.

To enable the Extended PaymentAuth Flow and allow a TPP to properly route an authorized payment request, the A2Pay must include the following mandatory details as defined in the [A2Pay schema](a2pay-schema.json):

- `payment-product`: Specifies the payment instrument or scheme to be utilized for the transaction.
- `sub`: Account identifier or account alias linked to the A2Pay. This can be represented by an IBAN or mobile phone number for example.

The transport of the P2Pay and the related payment request may be done using the [A2Pay Direct endpoint](#a2pay-direct-endpoint) and/or an according payment rail or scheme like OpenBanking APIs or existing European / domestic schemes e.g., which must implement additional support for processing the A2Pay format, data structures and signatures and may also include additional actors like payment platforms or aquirers e.g.. The specific technical and contractual implementation details for extending individual payment rails fall outside the scope of this document.

### Security considerations

To mitigate replay scenarios where the same P2Pay request is submitted to the ASPSP multiple times, the ASPSP should maintain a record of every processed P2Pay. This can be achieved by storing a hash of each P2Pay until its corresponding KB-JWT expires. To support this mechanism, the wallet should include the `exp` claim within the KB-JWT, specifying a validity period of 24 hours. This ensures that the ASPSP can effectively track and reject duplicate submissions during the lifespan of the KB-JWT.

![Payment](high-level-overview.png)

##### A2Pay Direct Endpoint

Assuming a PSP is obliged to support the [Basic PaymentAuth flow](#direct-payment-flow) to comply with the eIDAS 2.0 regulation with respect to SCA, they must support the `direct_post.jwt` endpoint defined by OpenID4VP, Section 7.3.1[^openid4vp] as it is required by the HAIP[^openid4vc_hip]. To simplify integration efforts, A2Pay Direct defines an HTTP POST endpoint very similar to the one required for the `direct_post.jwt` mode. Instead of the authorization response, this endpoint must accept a [Payment Authorization Object](#payment-authorization-object) encoded as JWT.

In order for an ASPSP to support the Extended PaymentAuth Flow using the A2Pay Direct endpoint, they must include the `payment-uri` property within the [PSP metadata](#psp-metadata) for the given `payment product`. The value must be the URI a TPP can use to send the [Payment Authorization Object](#payment-authorization-object) to. Details for the `a2pay-direct` endpoint are described in the [A2Pay API specification](a2pay-api.yml)

###### Payment Authorization Object

The Payment Authorization Object defines the following claims:

* `payment-request` REQUIRED: Original base64url encoded transaction data object of the [payment request](payment-request-schema.json) as it was included in `transaction_data` array of the authorization request.
* `a2pay` REQUIRED: P2Pay(SD-JWT VC presentation) containing the matching hash of the payment request given in `payment-request` claim within the key-binding JWT (see OpenID4VP section B4.5 [^openid4vp]).
* `creditor-account` CONDITIONAL: String describing the account of the creditor (IBAN e.g.). Might also be contained within the [payment request object](#payment-request-object). 

The Payment Authorization Object must be encoded as a JWT according to [rfc7519](https://datatracker.ietf.org/doc/html/rfc7519) and must be signed by the PSP. This requirement aligns with Article 34 of [Commission Delegated Regulation (EU) 2018/389](https://eba.europa.eu/regulation-and-policy/payment-services-and-electronic-money/regulatory-technical-standards-on-strong-customer-authentication-and-secure-communication-under-psd2), which outlines obligations for secure communication and authentication in the context of PSD2.

To sign the Payment Authorization Object, PSPs can leverage existing qualified eIDAS certificates, which are already widely used by TPPs for PSD2 compliance. Alternatively, PSPs might also be able to use a Relying Party Access Certificate issued by a Relying Party Access Certificate Authority (CA) described in ARF section 6.4[^arf] and ARF Annex 2 A.2.3.27 Topic 27[^arf_annex2]. 

Example of a [Payment Authorization Object](#payment-authorization-object) payload:
```json
{
  "payment-request": "ewogICJ0eXBlIjogIlBheW1lbnRSZXF1ZXN0IiwKICAiY3JlZGVudGlhbF9pZHMiOiBbIkEyUGF5Il0sCiAgInRyYW5zYWN0aW9uX2RhdGFfaGFzaGVzX2FsZyI6ICJzaGEtMjU2IiwKICAicGF5bWVudC1pZCI6ICI3RDhBQzYxMC01NjZELTNFRjAtOUMyMi0xODZCMkE1RUQ3OTMiLAogICJjcmVkaXRvci1hY2NvdW50IjogewogICAgImliYW4iOiAiREU3NTUxMjEwODAwMTI0NTEyNjE5OSIKICB9LAogICJpbnN0cnVjdGVkLWFtb3VudCI6ICIxNS40OSIsCiAgImN1cnJlbmN5IjogIkVVUiIsCiAgImNyZWRpdG9yLW5hbWUiOiAiTWVyY2hhbnQgQSIsCiAgInB1cnBvc2UiOiAiU2hvcHBpbmcgYXQgTWVyY2hhbnQgQSIKfQ==",
  "a2pay": "eyJ0eXAiOiJzZCtqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJodHRwczovL2JhbmsuZXhhbXBsZS5jb20vaXNzdWVyIiwicHNwIjoiaHR0cHM6Ly9iYW5rLmV4YW1wbGUuY29tL2lzc3VlciIsImV4cCI6MTg4MzAwMDAwMCwibmJmIjoxNzE4MTk4NDMzLCJpYXQiOjE3MTgxOTg0MzMsInZjdCI6Imh0dHBzOi8vY3JlZGVudGlhbHMuZXhhbXBsZS5jb20vYTJwYXkiLCJfc2RfYWxnIjoiU0hBLTI1NiIsInN1YiI6IkRFNzU1MTIxMDgwMDEyNDUxMjYxOTkiLCJpZCI6IjhEOEFDNjEwLTU2NkQtNEVGMC05QzIyLTE4NkIyQTVFRDc5MyIsInBheW1lbnQtcHJvZHVjdCI6InNjdC1pbnN0LWV1IiwicGF5bWVudC11cmkiOiJodHRwczovL2JhbmsuZXhhbXBsZS5jb20vcGF5LzdkZmU1NDg0Zzc4IiwibmFtZSI6Ik15IEFjY291bnQiLCJjbmYiOnsiandrIjp7ImNydiI6IlAtMjU2Iiwia3R5IjoiRUMiLCJ4IjoiTkFTSjJBRHVhZ092cmFMZjdPNFZ4Y0JNYmFudHpMOWRkMGpwdk1MbkJmcyIsInkiOiJPSlk2cHFDcVJJenBFdDc4T1hhc1dIR2dxVjVaR3JlXzNjSHRwTkg4MmdnIn19fQ.ILZLvsUx5X-EjKmJmXVfBqa6ADInAwJeEFd5XmIDuKNU7UkJWFy9VCeCC8xbdZKmEjXcFx-mmcJYVBKiRJIKlQ~eyJ0eXAiOiJrYitqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpYXQiOjE3MTAwNjk3MjIsImV4cCI6MTcxMDA3MzcyMiwiYXVkIjoiaHR0cHM6Ly9iYW5rLmV4YW1wbGUuY29tLyIsInNkX2hhc2giOiJFTkFrZUo0TjczMFRvQ21rZXMyc05SMkdTenBNazZfdDJ3a0lhbTUta0wwIiwidHJhbnNhY3Rpb25fZGF0YV9oYXNoZXMiOlsiNzI3MjcxNTZkMThiZDMxM2MwMmRlNTc1MTQ5YjNmNTZkZDVhNjgyZWNkOTZlY2M3Y2RhNjYwMjAyNzg2MGVlMyJdfQ.UkWOpguDo6KYfJ74SbYQ7EA-Aa-b7awo3WcNwVArjxwbGGlhXggf5geBGKe2MQyIrrgNYF63T7ZGZgA8rUyXcQ"
}
```

The response is a JSON object with the following properties defined:

* `payment_status` REQUIRED: Payment status code as defined in [ISO 20022](https://www.iso20022.org/catalogue-messages/additional-content-messages/external-code-sets). See also section [Payment status](#payment-status).

Example of a HTTP POST response send by the ASPSP:

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "payment_status": "ACSC"
}
```

##### MSCT Payments
 
In order to leverage payment solution based on Mobile Initiated SEPA (Instant) Credit Transfers (MSCT), the wallet must be enabled to process [MSCT URLs](https://www.europeanpaymentscouncil.eu/sites/default/files/kb/file/2022-06/EPC024-22v1.0%20Standardisation%20of%20QR-codes%20for%20MSCTs_0.pdf) as defined by the [EPC](https://www.europeanpaymentscouncil.eu/what-we-do/other-sepa-payments/sepa-goes-mobile/ad-hoc-multi-stakeholder-group-mobile-initiated), which can be used in an interoperability framework in domestic environment or between payment schemes and where the information contained in the URL must be used to obtain an actual OpenID4VP authorization request. This process relates to the payee-presented QR code containing a Token/MerchantID. Other scenarios where the MSCT URL contains any payment details in "clear" are not supported.
 
The following rules apply for the support of MSCT payments:
 
- The `payment-product` property of the A2Pay should be used to identify the MSCT interoperability framework or scheme
- The PSP metadata of for the given `payment-product` must also include a `msct_uri`, which refers to an endpoint the wallet must use to optain the actual OpenID4VP authorization request for a MSCT URL not in openID format.  
- While scanning / processing an MSCT URL, the wallet must check for an compatible A2Pay based on the `payment-product` property. If multiple A2Pays are available, the user must select one. If there are no A2Pays available, the wallet must indicate to the customer that the A2Pay method must be enrolled first.
- The wallet must exchange the token given in the MSCT URL for an OpenID4VP authorization request using the endpoint given in the `msct_uri` property of the PSP metadata. The endpoint must implement the token exchange endpoint defined in [MSCT API specification](msct-api.yml)
 
Example of an MSCT URL:
 
```
https://p.bcmt.it/1/m/?<payload>
https://go.bluecode.biz/1/m/oa/?oa=<payload>
https://xy.empsa.org/1/m/?<payload>
```
 
Example of an A2Pay supporting MSCT:
 
```json
{
    "_sd": [],
    "iss": "https://msct.example.com/",
    "psp": "https://msct.example.com/",
    "exp": 1883000000,
    "nbf": 1718198433,
    "iat": 1718198433,
    "vct": "https://credentials.example.com/a2pay",
    "_sd_alg": "sha-256",
    "sub": "IT12A1234512345123456789012",
    "id": "fd3f70a7-53b0-453e-9317-c7fc41a2f7ec",
    "payment-product": "Bancomat",
    "name": "My Account",
    "cnf": {
      "jwk": {
        "crv": "P-256",
        "kty": "EC",
        "x": "NASJ2ADuagOvraLf7O4VxcBMbantzL9dd0jpvMLnBfs",
        "y": "OJY6pqCqRIzpEt78OXasWHGgqV5ZGre_3cHtpNH82gg"
        }
    }
}
```

Example of the HTTP GET request to retrieve the PSP metadata from a PSP supporting MSCT:

```
GET /.well-known/psp HTTP/1.1
Host: https://msct.example.com/
```

Example of the HTTP GET response to the PSP meta data request:
```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "payment-products": {  
      "bancomat-pay": {
        "name": "Bancomat",
        "payment_status_uri" : "https://bank.example.com/a2pay/status",
        "msct_uri": "https://msct.example.com/exchange"
      }
    }
  }
}


```


```mermaid
 
sequenceDiagram
    autonumber
    actor user as User<br/>aka Holder
    participant wallet as Wallet
    participant psp as TPP<br/>aka Verifier
    participant msct as MSCT<br/>Gateway
    participant aspsp as ASPSP<br>aka Issuer
    psp ->> msct: intiate transaction
    msct -->> psp: MSCT URL
    psp ->> wallet: SHOW MSCT URL as QR code
    wallet ->> psp: scan QR code
    opt multiple compatible A2Pay available
    wallet ->> user: ask for A2pay
    user -->> wallet: selects A2Pay
    end
    wallet ->> wallet: process token with A2Pay
    wallet ->> aspsp: GET PSP metadata
    aspsp -->> wallet: RESP PSP metadata
    wallet  ->> msct: POST token, A2Pay ID
    msct -->> wallet : Authorization Request
    note over wallet,msct: ... extended PaymentAuth Flow continues
```
1. TPP initiates a payment by sending the payment transaction details to an MSCT token provider to request a token.
2. Token providers tokenizes the transaction details and returns an MSCT URL including a token to the TPP.
3. The TPP shows the MSCT URL as QR code.
4. Wallet scans the QR code and checks if it is holding a MSCT supporting A2Pay.
5. If the wallet hold multiple compatible A2Pays, it must ask the user to select one.
6. User selects the A2Pay to use.
7. Wallet processes the payment using the selected A2Pay.
8. Wallet request the PSP metadata using the PSP Identifier included in the selected A2Pay.
9. Wallet receives the PSP metadata.
10. Wallet sends the MSCT URL to the MSCT provider using the `msct-uri` given in the PSP's metadata.
11. MSCT provider responds with an OpenID4VP authorization request.

### Combined presentations

Relying on the presentation flows described above and also the requirements defined for the wallet in ARF Annex 2 section A.2.3.18 Topic 18[^arf_annex2], a PSP may combine the request for an P2Pay with additional attestations that may reside in the wallet. Therby they can leverage the additional information to build more advanced use-case like combining payment with proof of age or loyalty cards e.g..

>[!CAUTION]
>The capability to request multiple attestations also introduces the potential for multiple A2Pay requests, which could result in the user unintentionally authorizing the same payment transaction multiple times. To mitigate this risk, the wallet must implement safeguards to ensure that each payment request is uniquely associated with a single presentation. Once a payment request is linked to a presentation, the wallet should prevent it from being reused, thereby guaranteeing that the same transaction cannot be authorized more than once.

### Screenflow

Same-device screenflow of the payment process:

![Screenflow](screenflow.png)

1. Mechant app initiates the process by requesting the presentation of an A2Pay.
2. Activate the EUDI Wallet and authenticate user.
3. Review payment details
4. Authorize the transaction by providing biometrics.
5. Query status of the payment.
6. Final payment status on the wallet.
7. Final payment status on merchant app.


[^openid4vp]: [OpenID4VP](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html)
[^openid4vci]: [OpenID4VCI](https://openid.github.io/OpenID4VCI/openid-4-verifiable-credential-issuance-wg-draft.html)
[^arf]:[Architecture Reference Framework](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/arf.md)
[^arf_annex1]: [ARF Annex 1](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-1/annex-1-definitions.md)
[^arf_annex2]: [ARF Annex 2](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2-high-level-requirements.md)
[^openid4vc_hip]: [OpenID4VC High Assurance Interoperability Profile](https://openid.net/specs/openid4vc-high-assurance-interoperability-profile-1_0.html)
[^sd-jwt-vc]: [SD-JWT-based Verifiable Credentials](https://www.ietf.org/archive/id/draft-ietf-oauth-sd-jwt-vc-08.html)
[^sd-jwt]: [Selective Disclosure for JWTs](https://www.ietf.org/archive/id/draft-ietf-oauth-selective-disclosure-jwt-14.html)
