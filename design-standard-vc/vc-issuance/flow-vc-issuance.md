# Verifiable Credential Issuance (aka Registration of Attestation to Pay) 

## References

[OpenID for Verifiable Credential Issuance - Editor's draft](https://openid.github.io/OpenID4VCI/openid-4-verifiable-credential-issuance-wg-draft.html)

## Development Environment

| Endpoint              | URI                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------|
| Issuance page         | https://api.signicat.dev/wallet-poc/issuer/qr-code?credentialType=NobidPidNO-Citizen |
| credential_offer_uri  | https://api.signicat.dev/wallet-poc/issuer/credential-offer/XXX                      |
| credential_issuer (A) | https://api.signicat.dev/wallet-poc/issuer/.well-known/openid-credential-issuer      |
| credential_issuer (B) | https://api.signicat.dev/wallet-poc/issuer/.well-known/openid-configuration          |
| token_endpoint        | https://api.signicat.dev/wallet-poc/issuer/token                                     |
| credential_endpoint   | https://api.signicat.dev/wallet-poc/issuer/credential                                |



## Preconditions

This flow assumes that the End-User has activated Wallet with `PID` document already issued. 

Wallet VC list:
* PID (John Smith, ...)

## Postconditions

Wallet VC list:
* PID (John Smith, ...)
* Test PID NO Citizen (Oslo, 1970, male, ...)


## Flow sequence

```mermaid

sequenceDiagram
    autonumber
    actor user as End-User
    participant browser as Browser
    participant wallet as Wallet
    participant psp as Issuer<br>(PSP)
    participant auth as Authorization Server<br>(PSP)

    browser ->> psp: visits issuance page
    psp ->> psp: generates Credential Offer<br> by Reference<br> as QR Code
    psp -->> wallet: Credential Offer<br/> as scanned QR Code<br> (credential_offer_uri)
    wallet ->> psp: Request the<br>Credential Offer
    psp -->> wallet: Credential Offer<br>(pre-authorized_code,<br>tx_code {descriptor only})
    wallet ->> psp: Request the<br>Credential Issuer Metadata A
    psp -->> wallet: Credential Issuer Metadata A<br>(credential_endpoint)
    wallet ->> psp: Request the<br>Credential Issuer Metadata B
    psp -->> wallet: Credential Issuer Metadata B<br>(token_endpoint, authorization_endpoint)
    user -->> wallet: Consent to Issue new VC
    user -->> wallet: Transaction Code (tx_code) 
    wallet ->> auth: Token Request<br>(tx_code, pre-authorized_code)
    auth -->> wallet: Token Response  <br>(access_token)
    wallet ->> psp: Credential Request<br> (jwt(access_token))
    psp -->> wallet: Credential Response
```



## Sequence details

### 1. Issuance Web page

    URL: https://api.signicat.dev/wallet-poc/issuer/qr-code?credentialType=NobidPidNO-Citizen


### 3. Credential Offer Reference as scanned QR Code

    openid-credential-offer://?credential_offer_uri=https://api.signicat.dev/wallet-poc/issuer/credential-offer/076824e2-fbfc-47c0-a75f-a47af5522165


### 4. Request the Credential Offer

The request path is value of `credential_offer_uri` paramter taken from previous step (QR code).

    HTTP GET: https://api.signicat.dev/wallet-poc/issuer/credential-offer/076824e2-fbfc-47c0-a75f-a47af5522165


### 5. Credential Offer Response

JSON body:

    {
        "credential_issuer":"https://api.signicat.dev/wallet-poc/issuer",
        "credential_configuration_ids":[
            "NobidPidNO-Citizen"
        ],
        "grants":{
            "urn:ietf:params:oauth:grant-type:pre-authorized_code":{
                "pre-authorized_code": "eyJraWQiOiJkaWQ6a2V5Ono3eXZ2TFVQS0ZjUTYxQjlXUFJHQTVNM25YNlR6aUUxbmFXMXhRcUxiWXlzUm9pRkQ4c2N5eFpzUVNGaG5hOW5EVjRMd0ZmOGc4WldwVmdEYnZBbkR3OGNTbmtNbktHNnN1eWpZZUt0SGZEVkRUS3d3NHozeVNleERwa1Azd1hjQXB4Z3ZFVWc3elNGOXNQZ1RESkdWaTE0ZXkxWlhTcjVYNHZHU2tvRE5iQjFxWWhHcnpzTkVrWFRYcWVvdXpOekFtUDZEOHBrZ3dBUXFHenNINGdIaUNuNERMMk0xWUptTFI0dG05ZGVZVzgjejd5dnZMVVBLRmNRNjFCOVdQUkdBNU0zblg2VHppRTFuYVcxeFFxTGJZeXNSb2lGRDhzY3l4WnNRU0ZobmE5bkRWNEx3RmY4ZzhaV3BWZ0RidkFuRHc4Y1Nua01uS0c2c3V5alllS3RIZkRWRFRLd3c0ejN5U2V4RHBrUDN3WGNBcHhndkVVZzd6U0Y5c1BnVERKR1ZpMTRleTFaWFNyNVg0dkdTa29ETmJCMXFZaEdyenNORWtYVFhxZW91ek56QW1QNkQ4cGtnd0FRcUd6c0g0Z0hpQ240REwyTTFZSm1MUjR0bTlkZVlXOCIsInR5cCI6IkpXVCIsImFsZyI6IkVTMzg0In0.eyJhdWQiOiJkaWQ6a2V5Ono3eXZ2TFVQS0ZjUTYxQjlXUFJHQTVNM25YNlR6aUUxbmFXMXhRcUxiWXlzUm9pRkQ4c2N5eFpzUVNGaG5hOW5EVjRMd0ZmOGc4WldwVmdEYnZBbkR3OGNTbmtNbktHNnN1eWpZZUt0SGZEVkRUS3d3NHozeVNleERwa1Azd1hjQXB4Z3ZFVWc3elNGOXNQZ1RESkdWaTE0ZXkxWlhTcjVYNHZHU2tvRE5iQjFxWWhHcnpzTkVrWFRYcWVvdXpOekFtUDZEOHBrZ3dBUXFHenNINGdIaUNuNERMMk0xWUptTFI0dG05ZGVZVzgiLCJzdWIiOiJkaWQ6a2V5Ono3eXZ2TFVQS0ZjUTYxQjlXUFJHQTVNM25YNlR6aUUxbmFXMXhRcUxiWXlzUm9pRkQ4c2N5eFpzUVNGaG5hOW5EVjRMd0ZmOGc4WldwVmdEYnZBbkR3OGNTbmtNbktHNnN1eWpZZUt0SGZEVkRUS3d3NHozeVNleERwa1Azd1hjQXB4Z3ZFVWc3elNGOXNQZ1RESkdWaTE0ZXkxWlhTcjVYNHZHU2tvRE5iQjFxWWhHcnpzTkVrWFRYcWVvdXpOekFtUDZEOHBrZ3dBUXFHenNINGdIaUNuNERMMk0xWUptTFI0dG05ZGVZVzgiLCJuYmYiOjE3MzI1MzE4MTIsImlzcyI6ImRpZDprZXk6ejd5dnZMVVBLRmNRNjFCOVdQUkdBNU0zblg2VHppRTFuYVcxeFFxTGJZeXNSb2lGRDhzY3l4WnNRU0ZobmE5bkRWNEx3RmY4ZzhaV3BWZ0RidkFuRHc4Y1Nua01uS0c2c3V5alllS3RIZkRWRFRLd3c0ejN5U2V4RHBrUDN3WGNBcHhndkVVZzd6U0Y5c1BnVERKR1ZpMTRleTFaWFNyNVg0dkdTa29ETmJCMXFZaEdyenNORWtYVFhxZW91ek56QW1QNkQ4cGtnd0FRcUd6c0g0Z0hpQ240REwyTTFZSm1MUjR0bTlkZVlXOCIsImNyZWRlbnRpYWxfb2ZmZXJfaWQiOiIwNzY4MjRlMi1mYmZjLTQ3YzAtYTc1Zi1hNDdhZjU1MjIxNjUiLCJleHAiOjE3MzI2MTgyMTIsImlhdCI6MTczMjUzMTgxMn0.Swd8x1KTsVLmuN7dM-QNNBiUnIX4u2GZZXJ8ZV9F3iylILfejrJ7Cd0z_67oy-tRov3MW2LEm1066qp6WaRWukcVLhxQ-BS3plBbIfZPSHKo-BHbNx4dLjK6sUeRsRil",
                "tx_code":{
                    "length":4,
                    "input_mode":"numeric",
                    "description":"Please enter the secret key"
                }
            }
        }
    }


### 6. Request the Credential Issuer Metadata A

The request path is built based on `credential_issuer` taken from previous step.


    HTTP GET: https://api.signicat.dev/wallet-poc/issuer/.well-known/openid-credential-issuer


### 7. Credential Issuer Metadata A Response

JSON body (shortened):

    {
        "credential_issuer": "https://api.signicat.dev/wallet-poc/issuer",
        "authorization_servers": [
            "https://api.signicat.dev/wallet-poc/issuer"
        ],
        "credential_endpoint": "https://api.signicat.dev/wallet-poc/issuer/credential",
        "deferred_credential_endpoint": "https://api.signicat.dev/wallet-poc/issuer/credential-deferred",
        "credential_configurations_supported": {
            "NobidPidNO-Citizen": {
                "format": "mso_mdoc",
                "scope": "NobidPidNO-Citizen",
                "doctype": "NobidPidNO-Citizen",
                "display": [
                    {
                        "name": "Test PID NO Citizen",
                        "locale": "en-GB",
                        "background_color": "#12107c",
                        "text_color": "#FFFFFF"
                    }
                ],
                "claims": {
                    "NO_person_id": {},
                    "age_birth_year": {},
                    ...
                },
                ...
            },
            ...
        }
    }

Full version: [Click here](./06-CredentialIssuerMetadata-response.json)


### 8. Request the Credential Issuer Metadata B

The request path is built based on `credential_issuer` taken from previous step.


    HTTP GET: https://api.signicat.dev/wallet-poc/issuer/.well-known/openid-configuration


### 9. Credential Issuer Metadata B Response

JSON body:

    {
        "authorization_endpoint": "https://api.signicat.dev/wallet-poc/issuer/authorize",
        "token_endpoint": "https://api.signicat.dev/wallet-poc/issuer/token",
        "issuer": "https://api.signicat.dev/wallet-poc/issuer",
        "jwks_uri": "https://api.signicat.dev/wallet-poc/issuer/jwks",
        "response_types_supported": [
            "code",
            "vp_token",
            "id_token"
        ],
        "token_endpoint_auth_methods_supported": [
            "none"
        ],
        "request_uri_parameter_supported": true,
        "pre-authorized_grant_anonymous_access_supported": true,
        "id_token_signing_alg_values_supported": [
            "ES256"
        ],
        "subject_types_supported": [
            "public"
        ]
    }


### 12. Token Request

The request path is value of `token_endpoint` parameter taken from previous step.

    HTTP POST: https://api.signicat.dev/wallet-poc/issuer/token


Request Body (shortened):

    grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Apre-authorized_code
    &tx_code=1111
    &pre-authorized_code=eyJra...Ua0L
    &client_id=wallet-dev

Full version: [Click here](12-Token-request.txt)


### 13. Token Response

JSON body:

    {
        "access_token": "eyJraWQiOiIzNDViODdiNy1iZmMwLTQyZjUtYmU4YS02YmY5MGZlOWY3ZDUiLCJ0eXAiOiJKV1QiLCJhbGciOiJFUzM4NCJ9.eyJhdWQiOiJkaWQ6a2V5Ono3eXZ2TFVQS0ZjUTYxQjlXUFJHQTVNM25YNlR6aUUxbmFXMXhRcUxiWXlzUm9pRkQ4c2N5eFpzUVNGaG5hOW5EVjRMd0ZmOGc4WldwVmdEYnZBbkR3OGNTbmtNbktHNnN1eWpZZUt0SGZEVkRUS3d3NHozeVNleERwa1Azd1hjQXB4Z3ZFVWc3elNGOXNQZ1RESkdWaTE0ZXkxWlhTcjVYNHZHU2tvRE5iQjFxWWhHcnpzTkVrWFRYcWVvdXpOekFtUDZEOHBrZ3dBUXFHenNINGdIaUNuNERMMk0xWUptTFI0dG05ZGVZVzgiLCJuYmYiOjE3MzI1MzMxNjUsImlzcyI6ImRpZDprZXk6ejd5dnZMVVBLRmNRNjFCOVdQUkdBNU0zblg2VHppRTFuYVcxeFFxTGJZeXNSb2lGRDhzY3l4WnNRU0ZobmE5bkRWNEx3RmY4ZzhaV3BWZ0RidkFuRHc4Y1Nua01uS0c2c3V5alllS3RIZkRWRFRLd3c0ejN5U2V4RHBrUDN3WGNBcHhndkVVZzd6U0Y5c1BnVERKR1ZpMTRleTFaWFNyNVg0dkdTa29ETmJCMXFZaEdyenNORWtYVFhxZW91ek56QW1QNkQ4cGtnd0FRcUd6c0g0Z0hpQ240REwyTTFZSm1MUjR0bTlkZVlXOCIsImNyZWRlbnRpYWxfb2ZmZXJfaWQiOiJhZmI1YWRjZC1hMTYzLTRiMjQtYTNlMC0xODI1NzRmNzBmYTQiLCJleHAiOjE3MzI2MTk1NjUsImlhdCI6MTczMjUzMzE2NSwibm9uY2UiOiJldnVSUzVicWVMUVpVUFhNcWZ1UmtHOVk4bnFmdUVBNHFESzg5bFlvaDM0In0.1L5bKBP0om8Uaz3yGeFGCapiVUyGh3EYDrEZF56I3oGGCTKXCEUy7xete8WO6QqkY3Xyjk1Cac0Cz0sG4QAA2Vz5Zd1PhqfM8NmE6WBPJPLWZ5AoR-NozDLHFcs3EtiQ",
        "token_type": "bearer",
        "expires_in": 1732619565,
        "scope": null,
        "id_token": null,
        "c_nonce": "evuRS5bqeLQZUPXMqfuRkG9Y8nqfuEA4qDK89lYoh34",
        "c_nonce_expires_in": 1732619565,
        "authorization_details": [
            {
                "type": "openid_credential",
                "credential_configuration_id": "NobidPidNO-Citizen",
                "credential_identifiers": [
                    "NobidPidNO-Citizen"
                ]
            }
        ]
    }


### 14. Credential Request

The request path is value of `credential_endpoint` paramter taken from "Credential Issuer Metadata A Response".

    HTTP POST: https://api.signicat.dev/wallet-poc/issuer/credential

`jwt` consist of `access_token` and more...

JSON body (shortened):

    {
        "proof": {
            "jwt": "eyJhbGciOiJFUzI1NiIsImp3ayI6eyJhbGciOiJFUzI1NiIsImNydiI6IlAtMjU2Iiwia2lkIjoiOTdGNzlGNUQtRURDOC00RDkyLTk5OUMtNDlBQjE3NTg5QkI5Iiwia3R5IjoiRUMiLCJ1c2UiOiJzaWciLCJ4IjoiMGZHNTQ5ZkRvN2NfTzREWVRmbEFBTVBSS01zMzVaS0QzU2lsVjVFamtRZyIsInkiOiJQUEozT0R5elVwbHJTRWxibWJaU3paVFVpOUdXeXR4WkFmZWpoZUZrVFJjIn0sInR5cCI6Im9wZW5pZDR2Y2ktcHJvb2Yrand0In0.eyJub25jZSI6ImV2dVJTNWJxZUxRWlVQWE1xZnVSa0c5WThucWZ1RUE0cURLODlsWW9oMzQiLCJpYXQiOjE3MzI1MzM0MTYsImF1ZCI6Imh0dHBzOlwvXC9hcGkuc2lnbmljYXQuZGV2XC93YWxsZXQtcG9jXC9pc3N1ZXIiLCJpc3MiOiJ3YWxsZXQtZGV2In0.U-5JRwq7bLyi1hC4BPGx4g-NVNwmfQSv7VCE8V-lX2qYgoJd2EvtjHGZVF7kYvIWn7lzzOXiMvCKrX_zFxiOJg",
            "proof_type": "jwt"
        },
        "doctype": "NobidPidNO-Citizen",
        "format": "mso_mdoc"
    }


### 15. Credential Response

JSON body (shortened):

    {
        "credential": "ompuYW1lU...00nXI"
    }

Full version: [Click here](./15-Credential-response.json)


TODO: decode the credential
