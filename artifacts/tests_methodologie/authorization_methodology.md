# Authorization Testing Plan

## Objective

The objective of this phase is to verify whether the application correctly enforces access control after authentication.

Authorization testing answers the following question:

> Is the authenticated user allowed to access or modify this specific resource or perform this specific action?

---

## Test Accounts Required

To perform proper authorization testing, at least two normal user accounts are required:

* **User A:** legitimate authenticated user
* **User B:** second authenticated user used for comparison

Optional:

* **Admin account:** if available, used to compare normal user and privileged user behavior

---

# 1. Horizontal Access Control Testing

## Goal

Verify whether User A can access resources that belong to User B.

## Test Cases

* Access another user's dashboard
* Access another user's profile
* Access another user's orders list
* Access another user's vehicles
* Access another user's invoices or receipts
* Access another user's messages or notifications

## Methodology

1. Log in as User A.
2. Capture requests in Burp Suite.
3. Identify user-controlled values such as:

```text
userId
accountId
orderId
vehicleId
profileId
basketId
```

4. Replace User A's identifiers with User B's identifiers.
5. Send the modified request.
6. Analyze the server response.

## Expected Secure Behavior

The server should return:

```text
403 Forbidden
401 Unauthorized
404 Not Found
```

or a generic response that does not expose User B's data.

---

# 2. Object-Level Authorization Testing

## Goal

Verify whether the application checks ownership of individual objects before allowing access.

## Test Cases

* View another user's order by changing the order ID
* View another user's vehicle details
* View another user's basket
* View another user's invoice
* Access another user's uploaded files

## Methodology

1. Identify object IDs from normal application usage.
2. Capture requests that access these objects.
3. Change the object ID to another valid object ID.
4. Replay the request.
5. Check whether unauthorized data is returned.

## Example

```http
GET /api/orders/1024
Authorization: Bearer <User_A_Token>
```

Modified request:

```http
GET /api/orders/1025
Authorization: Bearer <User_A_Token>
```

## Expected Secure Behavior

User A should not be able to access objects owned by User B.

---

# 3. Sensitive Data Modification Testing

## Goal

Verify whether User A can modify data belonging to User B.

## Test Cases

* Change another user's email
* Change another user's phone number
* Change another user's password
* Modify another user's profile information
* Delete another user's account
* Update another user's vehicle information

## Methodology

1. Log in as User A.
2. Capture profile update requests.
3. Identify ownership-related fields.
4. Replace User A's identifiers with User B's identifiers.
5. Modify the request body.
6. Send the request.
7. Verify whether User B's data was changed.

## Expected Secure Behavior

The server should reject the request and prevent unauthorized modification.

---

# 4. Vertical Access Control Testing

## Goal

Verify whether a normal user can access privileged or administrative functionality.

## Test Cases

* Access admin dashboard
* Access user management endpoints
* Access system configuration endpoints
* Access internal statistics
* Access all orders
* Access all users
* Modify roles or privileges

## Methodology

1. Log in as a normal user.
2. Identify admin or privileged endpoints.
3. Send requests using the normal user's token.
4. Check whether access is granted.

## Example Endpoints

```text
/admin
/api/admin/users
/api/users/all
/api/orders/all
/api/config
/api/roles
```

## Expected Secure Behavior

Normal users should not be able to access administrative functionality.

---

# 5. Function-Level Authorization Testing

## Goal

Verify whether restricted actions are protected, not just restricted pages.

## Test Cases

* Delete account
* Change user role
* Export user data
* Cancel another user's order
* Approve or reject actions
* Access hidden API functions

## Methodology

1. Find sensitive functions in the frontend or API traffic.
2. Send the request directly using Burp Repeater.
3. Test whether the backend enforces authorization.
4. Do not rely only on frontend visibility.

## Expected Secure Behavior

The backend must block unauthorized functions even if the request is sent manually.

---

# 6. Token and Claim-Based Authorization Testing

## Goal

Analyze whether authorization decisions rely on client-controllable token values.

## Test Cases

* Decode the token and inspect its structure
* Identify claims such as user ID, role, email, or permissions
* Attempt safe token manipulation in the test environment
* Check whether modified claims are trusted by the server

## Claims to Review

```text
userId
role
email
isAdmin
permissions
exp
iat
```

## Expected Secure Behavior

The server should validate token integrity and should not trust modified or unsigned token values.

---

# 7. Business Logic Authorization Testing

## Goal

Verify whether sensitive business actions are enforced server-side.

## Test Cases

* Modify account balance
* Modify order price
* Change payment status
* Apply unauthorized discounts
* Reuse coupons
* Change order owner
* Submit negative quantities
* Access paid features without payment

## Methodology

1. Capture business-related requests.
2. Identify sensitive parameters.
3. Modify values manually.
4. Replay the request.
5. Verify whether the backend accepts unauthorized changes.

## Expected Secure Behavior

Sensitive values must be calculated and validated server-side.

---

# 8. OTP and Account Recovery Authorization Testing

## Goal

Verify whether OTP and recovery flows are properly tied to the correct user and session.

## Test Cases

* Brute force OTP codes
* Reuse expired OTP codes
* Use User A's OTP for User B
* Reset another user's password
* Check rate limiting
* Check OTP expiration
* Check whether OTP is tied to user identity

## Expected Secure Behavior

OTP codes should be:

* Rate-limited
* Time-limited
* Single-use
* Bound to the correct user
* Bound to the correct recovery session

---

# 9. Mass Assignment Testing

## Goal

Verify whether users can modify restricted fields by adding extra parameters to requests.

## Test Cases

* Add `role` field
* Add `isAdmin` field
* Add `balance` field
* Add `verified` field
* Add `userId` or `ownerId` field

## Example

Original request:

```json
{
  "email": "user@example.com",
  "phone": "0600000000"
}
```

Modified request:

```json
{
  "email": "user@example.com",
  "phone": "0600000000",
  "role": "admin",
  "isAdmin": true,
  "balance": 999999
}
```

## Expected Secure Behavior

The backend should ignore or reject restricted fields.

---

# 10. Evidence Collection

For each finding, collect:

* Request before modification
* Modified request
* Server response
* Screenshot if useful
* Affected endpoint
* Affected user role
* Impact
* Recommended fix

---

# Authorization Testing Checklist

* [ ] User A cannot access User B's dashboard
* [ ] User A cannot access User B's orders
* [ ] User A cannot access User B's profile
* [ ] User A cannot access User B's vehicles
* [ ] User A cannot modify User B's data
* [ ] User A cannot change User B's password
* [ ] User A cannot access admin endpoints
* [ ] User A cannot perform admin actions
* [ ] Backend blocks hidden restricted functions
* [ ] Token claims are not trusted if modified
* [ ] Sensitive business values are validated server-side
* [ ] OTP is protected against brute force
* [ ] OTP is tied to the correct user/session
* [ ] Mass assignment does not allow privilege escalation
* [ ] All authorization decisions are enforced server-side

---

# Summary

This authorization phase focuses on verifying whether the application correctly enforces access control at the object, function, user, and business-logic levels.

The main risks tested in this phase include:

* Broken Access Control
* IDOR / BOLA
* Privilege Escalation
* Function-Level Authorization Bypass
* Mass Assignment
* Business Logic Authorization Flaws
* Account Recovery Authorization Weaknesses
