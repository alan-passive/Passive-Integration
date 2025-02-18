# Passive-Integration
Integration into the passive platform can be summarized into the following steps:

1. Login
2. 2-Factor Verification
3. Add Property
4. Add Units
5. Add Tenants
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
