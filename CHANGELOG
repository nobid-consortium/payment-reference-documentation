# Change Log

## [v0.3] - 2024-12-19
- Changed OpenID4VP response mode from `direct_post` to `direct_post.jwt` as it is required following the current HAIP specs.
- Introduced `psp` claim holding a PSP Identifier as URI.
- Introduced PSP metadata. Using the identifier given in the PSP claim, a TPP can retrieve the required technical information it needs to process a payment. Therefor these information (`payment_uri`, `payment_status_uri` or `msct_uri` e.g.) don't need to be included in the A2Pay anymore and can be updated withour reissuing the A2Pay.
- Removed `payment_uri`, `currency` and `msct_uri` from A2Pay. 
- Removed the `payment_status_uri` from the `direct_post` and `a2pay-direct` responses.
- Remarks on status polling behaviour.

## [v0.2] - 2024-12-05
- Added section on using MSCT URLs / QR Codes

## [v0.1] - 2024-11-17
- initial release