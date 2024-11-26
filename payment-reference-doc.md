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

In the context of electronic payments and Strong Customer Authentication, the regulation obliges European payment service providers (PSP) to accept the European Identity Wallet (EUDIW) as a secure mean for authenticatiion and authorization providing:

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
 
This document focuses on presenting a structured approach to leverage the existing technical standards alongside the eIDAS 2.0 trust infrastructure. The goal is not only to develop and standardize a solution for Strong Customer Authentication (SCA) and electronic payments utilizing the European Identity Wallet but also to integrate payments with other forms of attestations as referenced in ARF, section A.2.3.20 Topic 20[^arf_annex2]. By doing so, it seeks to unlock the vast potential of the eIDAS ecosystem, enabling seamless interoperability, enhanced trust, and innovative use cases that extend beyond traditional payment processes.

## Registration
Before the wallet can be used to authorize payments, a registration process must be completed. The registration process, which typically occurs as a one-time setup, establishes a link between the user’s wallet instance and their Payment Service Provider (PSP) responsible for managing the payment account. This process is crucial as it allows the PSP to authenticate the wallet and verify its legitimacy, thereby establishing a secure and trusted relationship between the two entities. The details of this verification process and the mechanisms for building this trust are further outlined in the Architecture Reference Framework (ARF), Section 6.6.2[^arf].

During the registration process, the Payment Service Provider (PSP), also referred to as the issuer, issues dedicated Electronic Attestations of Attributes (EAA) required to facilitate payments and SCA (called Attestation to Pay or A2Pay hereafter) to the wallet. This is achieved using the OpenID4VCI protocol [^openid4vci] in accordance with the OpenID4VC High Assurance Interoperability Profile (HAIP) with SD-JWT VC (Selective Disclosure JSON Web Token Verifiable Credential) [^openid4vc_hip]. OpenID4VCI enables the issuer to issue the A2Pay within an authenticated and authorized context. While the exact method of establishing this context is determined by the issuer and is outside the scope of this document, it can be achieved through mechanisms such as:

* Login credentials (e.g., Online Banking credentials),
* One-Time Passwords (OTPs),
* Two-Factor Authentication (2FA) apps, including proprietary banking applications.

As outlined in ARF sections 6.6.6.3 and 6.6.3.7 [^arf] as well as Annex 2, Section A.2.3.9 Topic 9 [^arf_annex2], the issued A2Pay is cryptographically tied to a private key safeguarded by the wallet’s Secure Cryptographic Applications (WSCA) and Secure Cryptographic Devices (WSCD). During the credential issuance process, the corresponding public key is securely transmitted to the PSP, which must store it along with the holder’s account details. This public key is essential for verifying payment transactions that the wallet holder authorizes or digitally signs using their private key.


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

The A2Pay (Attestation to Pay) serves as a key component for identifying the wallet holder’s Payment Service Provider (PSP) and the specific account to which the attestation is linked. Beyond merely associating the holder with their PSP and account, the A2Pay can also specify a `payment-product` — the network or system used to process the payment- along with the additional properties, which can be utilized by relying parties, such as merchants, to initiate and process payment transactions seamlessly.

The structure and detailed specifications of the A2Pay are defined in the JSON schema file [a2pay-schema.json](a2pay-schema.json), which outlines all required attributes and their formats. This schema ensures interoperability and standardization of the attestation across different implementations and use cases.

Non-normative example of an A2Pay payload:

```json
{
    "_sd": [],
    "iss": "https://bank.example.com/issuer",
    "exp": 1883000000,
    "nbf": 1718198433,
    "iat": 1718198433,
    "vct": "https://credentials.example.com/a2pay",
    "_sd_alg": "sha-256",
    "sub": "DE75512108001245126199",
    "id": "8D8AC610-566D-4EF0-9C22-186B2A5ED793",
    "payment-product": "sct-inst-eu",
    "payment-uri": "https://bank.example.com/pay/7dfe5484g78",
    "currency": "EUR",
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
eyJ0eXAiOiJzZCtqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJodHRwczovL2JhbmsuZXhhbXBsZS5jb20vaXNzdWVyIiwiZXhwIjoxODgzMDAwMDAwLCJuYmYiOjE3MTgxOTg0MzMsImlhdCI6MTcxODE5ODQzMywidmN0IjoiaHR0cHM6Ly9jcmVkZW50aWFscy5leGFtcGxlLmNvbS9hMnBheSIsIl9zZF9hbGciOiJTSEEtMjU2Iiwic3ViIjoiREU3NTUxMjEwODAwMTI0NTEyNjE5OSIsImlkIjoiOEQ4QUM2MTAtNTY2RC00RUYwLTlDMjItMTg2QjJBNUVENzkzIiwicGF5bWVudC1wcm9kdWN0Ijoic2N0LWluc3QtZXUiLCJwYXltZW50LXVyaSI6Imh0dHBzOi8vYmFuay5leGFtcGxlLmNvbS9wYXkvN2RmZTU0ODRnNzgiLCJjdXJyZW5jeSI6IkVVUiIsIm5hbWUiOiJNeSBBY2NvdW50IiwiY25mIjp7Imp3ayI6eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6Ik5BU0oyQUR1YWdPdnJhTGY3TzRWeGNCTWJhbnR6TDlkZDBqcHZNTG5CZnMiLCJ5IjoiT0pZNnBxQ3FSSXpwRXQ3OE9YYXNXSEdncVY1WkdyZV8zY0h0cE5IODJnZyJ9fX0.4hv178s6B98WcthUTKj90mkMny2Obg64kvuuQNzI6vL-HgT7_0VRbJT_-SwDwc-vy-g7YVCYKe93Yg-PIGp7yw~
```

## Payment

The process of authenticating and authorizing a payment is carried out by presenting the A2Pay (called A2Pay' hereafter) to the Payment Service Provider (PSP) using the OpenID4VP protocol [^openid4vp]. This is implemented following Section 5 of the OpenID4VC High Assurance Interoperability Profile (HAIP) with SD-JWT VC [^openid4vc_hip], which specifies the following key requirements:

1.	Presentation Format: A2Pay' is formatted as a Self-Disclosure JWT (SD-JWT) presentation, adhering to the standards defined by Self Diclosure for JWTs[^sd-jwt].
2.	Delivery Mechanism: The A2Pay' is transmitted to the PSP using an HTTP POST request with the `response_mode` set to `direct_post`. Details regarding the payment initiation endpoint and how the HTTP POST request is structured can be found in the [A2Pay API specification](a2pay-api.yml) and in Section 7.2 of OpenID4VP [^openid4vp].
3.	Response Handling by PSP: Upon successful receipt and processing of the Authorization Request, the PSP responds with an `HTTP 200` status. The response includes a JSON object containing the `redirect_uri` property. The given URI may be used to query the [payment status](#payment-status).

The required trust relations between the wallet and the PSP during this process is established as outlined in ARF Section 6.6.3 [^arf], which elaborates on the mechanisms for mutual authentication and the secure exchange of information. 


### Dynamic linking

To fulfill the requirement for dynamic linking, the A2Pay' must include the transaction details of the payment (referred to as payment request), signed by the wallet. This ensures that the authentication process is securely tied to the specific payment transaction, as mandated by PSD2.

The OpenID4VP protocol [^openid4vp] supports the inclusion of dynamic transaction data in the authorization process using the `transaction_data` parameter. The flow operates as follows:

1.	The relying party includes the transaction-specific details (e.g., payment amount, payee information) in the authorization request using the `transaction_data` parameter.
2. The wallet processes the `transaction_data` values and computes a cryptographic hash of this data. This hash is embedded into the key-binding JWT of the A2Pay', which is then sent back to the PSP along with the authorization response.
3.	Authentication Code Generation: The computed hash serves as the authentication code required under PSD2 for dynamic linking, tieing the authorization response to the specific payment transaction details.
4.	User Approval Dialogue: To ensure transparency and user consent, the payment request details must be presented to the user within the wallet’s approval interface. This allows the user to review and approve the specific payment details. The user approval dialogue must comply with the requirements specified in ARF Section 6.6.3.4 [^arf] and ARF Annex 2, Section A.2.3.6 Topic 6 [^arf_annex2].

This process ensures compliance with PSD2’s requirement for dynamic linking by securely binding the transaction details to the authentication process. By incorporating the transaction hash into the key-binding JWT of the A2Pay', the wallet guarantees that any tampering with the transaction data invalidates the authentication. The reliance on OpenID4VP `transaction_data` feature ensures a standardized and secure implementation of this critical SCA requirement.

### Payment request object

The payment request object is included with in the `transaction_data` array and contains all the details describing the intended payment transaction. The data schema for the payment request is specified in the JSON schema file, [payment-request-schema.json](payment-request-schema.json). 

Non-normative example of a payment request:

```json
{
    "payment-id": "7D8AC610-566D-3EF0-9C22-186B2A5ED793",
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
  "payment-id": "7D8AC610-566D-3EF0-9C22-186B2A5ED793",
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

The A2Pay is transmitted to the PSP via an HTTP POST request, as specified by the `direct_post` response mode in OpenID4VP[^openid4vp]. The PSP must respond with a JSON object containing  the `redirect_uri` property as descibed in section 7.2, which allows the PSP to continue the interaction with the End-User on the device where the Wallet resides after the Wallet has sent the Authorization Response.

To communicate the actual status of the payment, the PSP must include an additional `payment_status_uri` parameter.  

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "redirect_uri": "https://bank.example.org/payment-status/091535f699ea575c7937fa5f0f454aee",
  "payment_status_uri": "https://bank.example.org/api/payment-status/091535f699ea575c7937fa5f0f454aee"
}
```

The `payment_status_uri` can subsequently be utilized to query the payment status, as it must adhere to the payment status endpoint specified in the [A2Pay API specification](a2pay-api.yml). The payment status is represented using [ISO 20022](https://www.iso20022.org/catalogue-messages/additional-content-messages/external-code-sets) payment status codes.

| Type |               Name              |                                                            Explanation                                                           |
|:----:|:-------------------------------:|:--------------------------------------------------------------------------------------------------------------------------------:|
| RCVD | Received                        | The payment has been received and is awaiting Strong Customer Authentication                                                     |
| ACCP | AcceptedCustomerProfile         | The preceding check of technical validation was successful. The customer profile check was also successful.                      |
| ACSP | Accepted Settlement In Progress | The payment has been sent by the bank but is not yet settled in the creditor account.                                            |
| ACSC | Accepted Settlement Completed   | The payment has been sent by the bank and settled in the creditor's account.                                                     |
| NAUT | NotAuthorized                   | The end-user has cancelled the payment authorization.                                                                            |
| RJCT | Rejected                        | The payment has failed due to insufficient funds in the debtor's account                                                         |
| PDNG | Pending                         | The debtor account holder has edited the payment in their online banking and a new Strong Customer Authentication is now pending |
| CANC | Cancelled                       | The payment has been deleted by the end-user                                                                                     |
| PRSY | OnHold                          | The payment initiation was put on hold by the bank                                                                               |
| PATC | Partially Accepted              | The payment requires a second authorization                                                                                      |                                                                               |   |   |

Example of a payment status response:

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "status-code": "ACSC"
}

```

### Verification

The successfull verification of the A2Pay' signals the PSP to proceed with executing the authorized payment transaction. The verification of the A2Pay is done following the guidlines given in Self Disclosure for JWT[^sd-jwt] section 7 and specifically 7.3. To verfiy the authentication code ensuring the A2Pay' is bound to the intended payment transaction, the PSP must calculate the hash of the base64url encoded transcation data object of the [payment request](payment-request-schema.json) with the chosen `transaction_data_hashes_alg`. For a successful verification, the resulting hash must equal the hash in the `transaction_data_hashes` array included in the key-binding JWT. 

The PSP may also use the `payment-id` within the payment request to ensure the payment is processed only once.

### Flows 

The presentation process outlined above can be adapted to various real-world use cases, depending on the specific role of the relying party requesting the A2Pay'.

#### Basic PaymentAuth flow

In this flow, the relying party is the PSP (usually a bank) of the holder themself. The holder is requesting a payment transaction using an out-of-band mechanism like the banks mobile app, online banking portal or even a third party provider for payment initiation according to PSD2 (PISP). The holders PSP is initiating the flow by requesting the A2Pay' they have previously issued to the wallet themself in the prior registration flow. 

**This scenario applies to the obligations for PSPs to accept the wallet for SCA with regards to the eIDAS2 regulation.**

```mermaid

sequenceDiagram
    autonumber
    actor user as User
    participant wallet as Wallet
    participant psp as PSP

    Note over psp: Out-of-band payment initiation
    rect rgb(100, 150, 100)
    note over wallet, psp: OpenID4VP
    psp ->> wallet: Authorization Request(transaction_data)
    activate wallet
    wallet ->> user: SHOW approval dialoge
    user ->> user: review transaction
    user ->> wallet: approves presentation
    wallet -->> psp: POST Authorization Response(A2Pay')
    psp -->> wallet: RESP receiving A2Pay' OK
    end
    wallet -->> user: SHOW presentation status
    deactivate wallet
    psp ->> psp: verify A2Pay' and execute transaction
    wallet ->> psp: GET payment status
    psp -->> wallet: RESP payment status 
    wallet -->> user: SHOW payment status
    psp -->> user: SHOW payment status
```

Out-of-band payment initiation:
The payment process is initiated through an out-of-band mechanism, such as a mobile app, online banking portal, or third-party provider.
1.	Authorization Request requesting A2Pay:
The Payment Service Provider (PSP) sends an OpenID4VP authorization request including the `transaction_data` with the payment request to the user’s wallet to obtain the A2Pay credential required for the transaction. Note: *Details of passing the authorization request by reference are omitted for readability reasons.* This step is done either by link for a same-device flow or by QR-code for cross-device flow.
2.	Wallet asks user for approval:
The wallet prompts the user to review and approve the transaction.
3.	User reviews the transaction:
The user evaluates the details of the transaction to ensure its accuracy and validity.
4.	User approves the presentation:
After reviewing, the user provides their approval to proceed with the transaction by confirming it in the wallet.
5.	Authorization Response sending A2Pay' to PSP:
The wallet presents the A2Pay' including the `transaction_data_hashes` along with the OpenID4VP authorization response to the PSP as HTTP POST request.
6.	PSP acknowledges receipt of A2Pay':
The PSP confirms that the authorization response with the A2Pay' has been received successfully and sends back the `redirect_uri` and the `payment_status_uri` to the wallet.
7.	PSP verifies A2Pay and executes the transaction:
The PSP validates the authenticity and integrity of the A2Pay'. If valid, the PSP proceeds to execute the payment transaction.
8.	Wallet queries payment status:
The wallet initiates a query to the PSP using the `payment_status_uri` to retrieve the current status of the payment.
9.	PSP provides payment status to wallet:
The PSP responds to the wallet with the payment status, indicating whether the transaction was successful or if any issues occurred.
10.	Wallet shows payment status to user:
The wallet communicates the payment status to the user, providing confirmation or detailing any errors.
11.	PSP shows payment status to user:
As an additional step, the PSP also informs the user of the payment status, offering direct confirmation from their side.

#### Extended PaymentAuth flow

In this flow, the relying party is a third party, such as a merchant or the merchant’s Payment Service Provider (referred to as PSP’ hereafter). PSP' initiates the process by requesting the A2Pay issued by the holder’s PSP. Once PSP' receives the A2Pay', it is obligated to forward it, along with the original payment request object, to the issuing PSP. The issuing PSP then performs the necessary verification and/or executes the payment transaction.

```mermaid

sequenceDiagram
    autonumber
    actor user as User
    participant wallet as Wallet
    participant psp2 as PSP'
    participant psp as PSP
    rect rgb(100, 150, 100)
    note over wallet, psp2: OpenID4VP
    psp2 ->> wallet: Authorization Request(transaction_data)
    activate wallet
    wallet ->> user: SHOW approval dialoge
    user ->> user: review transaction
    user ->> wallet: approves presentation
    wallet -->> psp2: Authorization Response(A2Pay')
    psp2 -->> wallet: RESP receiving A2Pay' OK
    end
    wallet -->> user: SHOW presentation status
    deactivate wallet
    rect rgb(200, 100, 100)
    note over psp, psp2: implemented by payment rail 
    psp2 ->> psp: forwarding A2Pay'
    psp -->> psp2: receiving A2Pay' OK
    end
    psp ->> psp: verify A2Pay' and execute transaction
    wallet ->> psp2: GET payment-status(payment-id)
    psp2 ->> psp: GET payment-status(payment-id)
    psp -->> psp2: RESP payment-status 
    psp2 -->> wallet: RESP payment-status
    wallet -->> user: SHOW payment status
    psp2 -->> user: SHOW payment status
```

1.	Authorization Request requesting A2Pay:
The merchant’s PSP (PSP') sends an openID4VP authorization request including the `transaction_data` with the payment request to the user’s wallet to obtain the A2Pay issued by the user’s PSP. *Note: Details of passing the authorization request by reference are omitted for readability reasons.* This step is done either by link for a same-device flow or by QR-code for cross-device flow.
2.	Wallet asks user for approval:
The wallet prompts the user to review the payment request and approve the presentation of the A2Pay.
3.	User reviews the transaction:
The user evaluates the details of the payment request to ensure its accuracy and validity.
4.	User approves the presentation:
After reviewing the transaction details, the user confirms the request in the wallet, granting approval to present the A2Pay.
5.	Authorization Response sends A2Pay' to PSP':
The wallet presents the A2Pay' including the `transaction_data_hashes` along with the OpenID4VP authorisation response to PSP' as HTTP POST request.
6.	PSP' acknowledges receipt of A2Pay':
PSP’ confirms that the A2Pay has been successfully received and sends back the `redirect_uri` and `payment_status_uri` to the wallet.
7.	Wallet updates user on status presentation:
The wallet provides the user with a status update, indicating that the A2Pay has been successfully sent.
8.	PSP' forwards A2Pay to issuing PSP:
PSP’ sends a [payment authorization object](#payment-authorization-object) including the the A2Pay' and the original payment request object to the user’s PSP for verification and/or execution of the payment using a supported payment rail and/or the [A2Pay Direct endpoint](#a2pay-direct-endpoint).
9.	Issuing PSP acknowledges receipt of A2Pay:
The user’s PSP confirms receipt of the forwarded A2Pay' as defined [here](#payment-status) and sends back the `payment_status_uri` to the PSP'.
10.	Issuing PSP [verifies A2Pay'](#verification) and executes the transaction:
The user’s PSP validates the authenticity and integrity of the A2Pay'. If valid, it executes the payment transaction.
11.	Wallet queries [payment status](#payment-status) from PSP':
The wallet initiates a query to PSP' using the `payment_status_uri` to retrieve the current status of the payment.
12.	PSP' queries [payment status](#payment-status) from issuing PSP:
PSP' initiates a query to PSP using the `payment_status_uri` to retrieve the current status of the payment.
13.	Issuing PSP responds with [payment status](#payment-status) to PSP':
The user’s PSP sends the [payment status](#payment-status) back to PSP'.
14.	PSP' forwards [payment status](#payment-status) to wallet:
PSP' relays the payment status received from the issuing PSP to the wallet.
15.	Wallet shows payment status to user:
The wallet provides the user with the payment status, indicating whether the transaction was successful or if any issues occurred.
16.	PSP' shows payment status to user:
PSP' provides an additional confirmation of the payment status to the user, ensuring they are informed directly using a channel linked to the PSP' like a merchant / bank app or website.

To enable the Extended PaymentAuth Flow and allow a PSP' to properly route an authorized payment request, the A2Pay must include the following mandatory details as defined in the [A2Pay schema](a2pay-schema.json):

- `payment-product`: Specifies the payment instrument or scheme to be utilized for the transaction.
- `sub`: Account identifier or account alias linked to the A2Pay. This can be represented by an IBAN or mobile phone number for example.

The transport of the A2Pay' and the related payment request may be done using the [A2Pay Direct endpoint](#a2pay-direct-endpoint) and / or an according payment rail or scheme like OpenBanking APIs or existing European / domestic schemes e.g., which must implement additional support for processing the A2Pay format, data structures and signatures and may also include additional actors like payment platforms or aquirers e.g.. The specific technical implementation details for extending individual payment rails fall outside the scope of this document.

![Payment](high-level-overview.png)

##### A2Pay Direct Endpoint

Assuming a PSP is obliged to support the [Basic PaymentAuth flow](#direct-payment-flow) to comply with the eIDAS 2.0 regulation with respect to SCA, they must support the `direct_post` endpoint defined by OpenID4VP, Section 7.2[^openid4vp] as it is required by the HAIP[^openid4vc_hip]. To simplify integration efforts, A2Pay Direct defines an endpoint very similar to the one required for the `direct_post` mode. Instead of a `vp_token`, this endpoint must accept an JWT encoded [Payment Authorization Object](#payment-authorization-object).

In order for a PSP to support the Extended PaymentAuth Flow using the A2Pay Direct endpoint, they must include the `payment-uri` property within the A2Pay during [registration](#registration). The value must be the URL a PSP' can use to send the Payment Authorization Object to.

Details for the `a2pay-direct` endpoint are described in the [A2Pay API specification](a2pay-api.yml)

###### Payment Authorization Object

The Payment Authorization Object is defined as a JWT according to [rfc7519](https://datatracker.ietf.org/doc/html/rfc7519) having the following claims:

* `payment-request` REQUIRED: Original base64url encoded transaction data object of the [payment request](payment-request-schema.json) as it was included in the authorization request.
* `a2pay` REQUIRED: A2Pay'(SD-JWT VC presentation) containing the matching hash of the payment request given in `payment-request` claim within the key-binding JWT (see OpenID4VP section B4.5 [^openid4vp]). 

The JWT must be signed by the PSP' including a certificate that reliably identifies the PSP' as it is required by article 34 of [Commission Delegated Regulation (EU) 2018/389](https://eba.europa.eu/regulation-and-policy/payment-services-and-electronic-money/regulatory-technical-standards-on-strong-customer-authentication-and-secure-communication-under-psd2). Existing qualified eIDAS certificates already used by Third Party Providers (TPPs) or the relying party access certificate issued by a Relying Party Access Certificate Authority (CA) described in ARF section 6.4[^arf] and ARF Annex 2 A.2.3.27 Topic 27[^arf_annex2] may be used.

The JWT must be digitally signed by the PSP, including a certificate that clearly and reliably identifies the PSP. This requirement aligns with Article 34 of [Commission Delegated Regulation (EU) 2018/389](https://eba.europa.eu/regulation-and-policy/payment-services-and-electronic-money/regulatory-technical-standards-on-strong-customer-authentication-and-secure-communication-under-psd2), which outlines obligations for secure communication and authentication in the context of PSD2.

To fulfill this requirement, PSPs can leverage existing qualified eIDAS certificates, which are already widely used by Third Party Providers (TPPs) for PSD2 compliance. Alternatively, PSPs might also be able to use a Relying Party Access Certificate issued by a Relying Party Access Certificate Authority (CA) described in ARF section 6.4[^arf] and ARF Annex 2 A.2.3.27 Topic 27[^arf_annex2]. 

Example of a [Payment Authorization Object](#payment-authorization-object) payload:
```json
{
  "payment-request": "ewogICJ0eXBlIjogIlBheW1lbnRSZXF1ZXN0IiwKICAiY3JlZGVudGlhbF9pZHMiOiBbIkEyUGF5Il0sCiAgInRyYW5zYWN0aW9uX2RhdGFfaGFzaGVzX2FsZyI6ICJzaGEtMjU2IiwKICAicGF5bWVudC1pZCI6ICI3RDhBQzYxMC01NjZELTNFRjAtOUMyMi0xODZCMkE1RUQ3OTMiLAogICJjcmVkaXRvci1hY2NvdW50IjogewogICAgImliYW4iOiAiREU3NTUxMjEwODAwMTI0NTEyNjE5OSIKICB9LAogICJpbnN0cnVjdGVkLWFtb3VudCI6ICIxNS40OSIsCiAgImN1cnJlbmN5IjogIkVVUiIsCiAgImNyZWRpdG9yLW5hbWUiOiAiTWVyY2hhbnQgQSIsCiAgInB1cnBvc2UiOiAiU2hvcHBpbmcgYXQgTWVyY2hhbnQgQSIKfQ==",
  "a2pay": "eyJ0eXAiOiJzZCtqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJodHRwczovL2JhbmsuZXhhbXBsZS5jb20vaXNzdWVyIiwiZXhwIjoxODgzMDAwMDAwLCJuYmYiOjE3MTgxOTg0MzMsImlhdCI6MTcxODE5ODQzMywidmN0IjoiaHR0cHM6Ly9jcmVkZW50aWFscy5leGFtcGxlLmNvbS9hMnBheSIsIl9zZF9hbGciOiJTSEEtMjU2Iiwic3ViIjoiREU3NTUxMjEwODAwMTI0NTEyNjE5OSIsImlkIjoiOEQ4QUM2MTAtNTY2RC00RUYwLTlDMjItMTg2QjJBNUVENzkzIiwicGF5bWVudC1wcm9kdWN0Ijoic2N0LWluc3QtZXUiLCJwYXltZW50LXVyaSI6Imh0dHBzOi8vYmFuay5leGFtcGxlLmNvbS9wYXkvN2RmZTU0ODRnNzgiLCJjdXJyZW5jeSI6IkVVUiIsIm5hbWUiOiJNeSBBY2NvdW50IiwiY25mIjp7Imp3ayI6eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6Ik5BU0oyQUR1YWdPdnJhTGY3TzRWeGNCTWJhbnR6TDlkZDBqcHZNTG5CZnMiLCJ5IjoiT0pZNnBxQ3FSSXpwRXQ3OE9YYXNXSEdncVY1WkdyZV8zY0h0cE5IODJnZyJ9fX0.4hv178s6B98WcthUTKj90mkMny2Obg64kvuuQNzI6vL-HgT7_0VRbJT_-SwDwc-vy-g7YVCYKe93Yg-PIGp7yw~eyJ0eXAiOiJrYitqd3QiLCJhbGciOiJFUzI1NiJ9.eyJpYXQiOjE3MTAwNjk3MjIsImF1ZCI6ImRpZDpleGFtcGxlOjEyMyIsIm5vbmNlIjoiazh2ZGYwbmQ2Iiwic2RfaGFzaCI6Im85MU5qUTNJVmh5U2NmSWtkcTFTXzl0LW52WVZLcmlIQUF0Q3lUdk9MUzQiLCJ0cmFuc2FjdGlvbl9kYXRhX2hhc2hlcyI6WyI3MjcyNzE1NmQxOGJkMzEzYzAyZGU1NzUxNDliM2Y1NmRkNWE2ODJlY2Q5NmVjYzdjZGE2NjAyMDI3ODYwZWUzIl19._Ssn-DojR6cN1kUROzmrQk-Y80UW8_VP_RiZKF-cSUOEG66hmb9-LR6KwhyYfamzs7T2RpHjOK5dYZjOm0NWeQ"
}
```

### Combined presentations

Relying on the presentation flows described above and also the requirements defined for the wallet in ARF Annex 2 section A.2.3.18 Topic 18[^arf_annex2], a PSP may combine the request for an A2Pay' with additional attestations that may reside in the wallet. Therby they can leverage the additional information to build more advanced use-case like combining payment with proof of age e.g..

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
[^openid4vc_hip]: [OpenID4VC High Assurance Interoperability Profile with SD-JWT VC ](https://openid.net/specs/openid4vc-high-assurance-interoperability-profile-sd-jwt-vc-1_0.html)
[^sd-jwt-vc]: [SD-JWT-based Verifiable Credentials](https://datatracker.ietf.org/doc/draft-ietf-oauth-sd-jwt-vc/05/)
[^sd-jwt]: [Selective Disclosure for JWTs](https://datatracker.ietf.org/doc/draft-ietf-oauth-selective-disclosure-jwt/13/)
