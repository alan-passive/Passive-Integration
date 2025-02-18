# Passive-Integration
Integration into the passive platform can be summarized into the following steps:

1. Login
2. 2-Factor Verification
3. Add Property
4. Add Units
5. Add Tenants and Request Deposit
6. Fetch Deposit Status
For testing purpose, use our development endpoint: `https://api.dev.joinpassive.com/`
## Login

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/login`
- Request Body: `{"email": "string", "password": "string"}`
- Successful Response: 
```
{
  "id": "User-UUID",
  "created_at": "2025-02-18T05:50:49.396Z",
  "last_updated_at": "2025-02-18T05:50:49.396Z",
  "deleted_at": "2025-02-18T05:50:49.396Z",
  "name": "string",
  "email": "string",
  "property_manager": "string"
}
```
By hitting the above endpoint, you will automatically recieve login-codes via phone number. However if you did not, can try resend(there is a 30sec resend interval) via:

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/totp/resend`
- Request Body: `{"user_id": "User-UUID"}`

## 2-Factor Verification

After successfully retrieving the verification code from step 1, you verify it via:

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/totp/verify`
- Request Body: `{"code": "your-6-digit-code", "user_id": "User-UUID"}`
- Successful Response: `{"access_token": "some-bearer-token"}`

Your response contains a Bearer token. Here on out, to complete any operations you must add this to your request header. Also note that our tokens are **generally** set to a week expiration. Use JWT decoder to find more accurate expire date should anything changes.

## Add Property

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/properties`
- Request Body: `
  {
    "address_line_1": "1 ABC St",
    "city": "Boston",
    "state": "MA",
    "zip_code": "02127",
    "deposit_types": "full"
}`
- Successful Response: `{"id": "Property-UUID"}`

Note that we currently only support `deposit_types: full`

## Add Units

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/properties/<Property-UUID>/units`
- Request Body: `[{"identifier": "Unit 1"}, {"identifier": "Apt 2"}, ....]`

To Retrieve the `Unit-UUID`:
- Method: Get
- Endpoint: https://api.dev.joinpassive.com/properties/<Property-UUID>

## Add Tenants and Request Deposit

- Method: Post
- Endpoint: `https://api.dev.joinpassive.com/deposits`
- Request body: `{
    "name": "Hello World",
    "email": "hello.world@passive.rent",
    "unit_id": "452f909c-958b-4480-8561-b32969483549",
    "start_date": "2025-02-05",
    "end_date": "2026-02-04",
    "deposit_amount": "5100.00",
    "last_month_amount": "700.10"
}`
- Successful Response: `{"id": "Deposit-UUID"}`

By hitting this endpoint, the client will receive an email to complete the security deposit steps. Should the clients not receive the email you are able to get the link via:

- Method: Get
- Endpoint: `https://api.dev.joinpassive.com/deposits/<Deposit-UUID>/link`
- Successful Response: `{"link": "some-link"}`

## Fetch Deposit Status

- Method: Get
- Endpoint: `https://api.dev.joinpassive.com/deposits/<Deposit-UUID>/`

There is a status field within the returned JSON. The four statuses are awaiting tenant Setup, active, pending closure, and closed. 

