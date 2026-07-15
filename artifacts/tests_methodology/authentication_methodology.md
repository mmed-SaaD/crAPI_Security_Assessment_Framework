# Authentication Testing Plan

## Objective

The objective of this phase is to verify whether the application correctly identifies users and protects authentication-related functions.

Authentication testing answers the following question:

> Can a user prove their identity securely, and can attackers bypass or abuse the login and recovery mechanisms?

---

# 1. Authentication Surface Mapping

## Goal

Identify all authentication-related endpoints and features.

## Areas to Identify

* Login
* Registration
* Logout
* Password reset
* Change password
* Email verification
* OTP verification
* Token refresh
* Session validation
* Account recovery

## Methodology

1. Browse the application normally.
2. Capture all authentication-related requests in Burp Suite.
3. Document endpoints, methods, parameters, and responses.
4. Identify whether the application uses cookies, JWTs, refresh tokens, or session IDs.

## Evidence to Collect

* Endpoint URL
* HTTP method
* Request body
* Response status code
* Authentication token or cookie behavior

---

# 2. Login Function Testing

## Goal

Verify whether the login mechanism properly validates user credentials.

## Test Cases

* Login with valid credentials
* Login with invalid email
* Login with invalid password
* Login with empty fields
* Login with malformed email
* Login with SQL injection payloads
* Login with very long input
* Login with special characters

## Example Payloads

```text
' OR '1'='1'--
admin'--
" OR "1"="1"--
test@example.com
```

## Expected Secure Behavior

The application should reject invalid credentials and should not reveal whether the email or password was incorrect.

---

# 3. Username and Email Enumeration

## Goal

Check whether the application reveals valid accounts through error messages or response differences.

## Test Cases

* Login with existing email and wrong password
* Login with non-existing email
* Password reset with existing email
* Password reset with non-existing email
* Registration using an already registered email

## Indicators of Enumeration

* Different error messages
* Different status codes
* Different response times
* Different response sizes

## Expected Secure Behavior

The application should use generic messages such as:

```text
Invalid email or password.
```

or:

```text
If this account exists, a reset email has been sent.
```

---

# 4. Password Policy Testing

## Goal

Verify whether the application enforces strong password requirements.

## Test Cases

Try registering or changing password using:

* Very short password
* Common password
* Password equal to username/email
* Numeric-only password
* Lowercase-only password
* Password without special characters
* Very long password

## Example Weak Passwords

```text
123456
password
qwerty123
test123
admin123
```

## Expected Secure Behavior

The application should enforce a reasonable password policy and reject weak passwords.

---

# 5. Brute Force Protection

## Goal

Check whether the application protects login and OTP endpoints against repeated attempts.

## Test Cases

* Multiple failed login attempts against one account
* Multiple failed login attempts using different accounts
* Multiple OTP attempts
* Multiple password reset attempts
* Check for account lockout
* Check for CAPTCHA
* Check for rate limiting

## Indicators of Weakness

* No delay after many failed attempts
* No lockout
* No CAPTCHA
* No rate limiting
* Same response after unlimited attempts

## Expected Secure Behavior

The application should apply rate limiting, temporary lockout, or other brute force protections.

---

# 6. Session Management Testing

## Goal

Verify whether authenticated sessions are securely created, maintained, and destroyed.

## Test Cases

* Check whether session/token is created after login
* Check whether logout invalidates the session
* Try reusing token after logout
* Check token expiration
* Check refresh token behavior
* Check whether session persists after password change
* Check whether session persists after email change

## Expected Secure Behavior

* Tokens should expire properly
* Logout should invalidate the active session
* Password change should invalidate old sessions
* Session tokens should not be predictable

---

# 7. JWT / Token Analysis

## Goal

Analyze whether authentication tokens are securely generated and validated.

## Test Cases

* Decode JWT token
* Inspect token claims
* Check expiration time
* Check algorithm
* Test whether unsigned or modified tokens are accepted
* Check whether sensitive data is stored in token

## Claims to Review

```text
userId
email
role
isAdmin
permissions
iat
exp
```

## Expected Secure Behavior

The server should reject modified tokens and should not expose sensitive information inside client-side tokens.

---

# 8. Password Reset Testing

## Goal

Verify whether the password reset flow is secure.

## Test Cases

* Request reset for valid account
* Request reset for invalid account
* Reuse reset token
* Use expired reset token
* Use reset token for another user
* Try predictable reset tokens
* Check whether reset token is leaked in URL, response, or logs

## Expected Secure Behavior

Reset tokens should be:

* Random
* Single-use
* Time-limited
* Bound to the correct user
* Invalidated after password reset

---

# 9. OTP Testing

## Goal

Verify whether OTP verification is secure.

## Test Cases

* Brute force OTP
* Reuse old OTP
* Use expired OTP
* Use User A's OTP for User B
* Check OTP length
* Check OTP expiration time
* Check OTP rate limiting
* Check whether OTP is tied to session/user

## Expected Secure Behavior

OTP codes should be:

* Time-limited
* Single-use
* Rate-limited
* Bound to the correct user and session

---

# 10. Registration Testing

## Goal

Verify whether account creation is secure and properly validated.

## Test Cases

* Register with valid data
* Register with existing email
* Register with weak password
* Register with malformed email
* Register with missing required fields
* Register with extra fields such as `role` or `isAdmin`
* Register with long input values

## Expected Secure Behavior

The application should validate all fields server-side and reject unauthorized fields.

---

# 11. Logout Testing

## Goal

Verify whether logout properly terminates the authenticated session.

## Test Cases

* Logout normally
* Reuse old token after logout
* Use browser back button after logout
* Access authenticated endpoints after logout
* Check whether refresh token remains valid

## Expected Secure Behavior

After logout, the old session/token should no longer grant access.

---

# 12. Evidence Collection

For each finding, collect:

* Original request
* Modified request
* Server response
* Screenshot if useful
* Affected endpoint
* Impact
* Risk level
* Recommended fix

---

# Authentication Testing Checklist

* [🟢] Authentication endpoints are mapped
* [🟢] Login validates credentials properly
* [🟢] SQL injection login bypass is tested
* [🔴] Error messages do not reveal valid users
* [🟢] Password policy is enforced
* [ ] Brute force protection exists
* [ ] OTP is rate-limited
* [ ] OTP is time-limited and single-use
* [ ] Password reset tokens are secure
* [🔴] Logout invalidates session/token => Logout does not invalidate session token
* [ ] Tokens expire properly
* [ ] Modified tokens are rejected
* [ ] Sensitive data is not exposed in tokens
* [ ] Registration validates input server-side
* [ ] Extra fields such as `role` or `isAdmin` are rejected

---

# Summary

This authentication phase focuses on verifying whether the application securely identifies users and protects login, registration, password reset, OTP, token, and session-related mechanisms.

The main risks tested in this phase include:

* Authentication Bypass
* Weak Password Policy
* Username Enumeration
* Brute Force Weakness
* Insecure Session Management
* Insecure JWT Handling
* Password Reset Vulnerabilities
* OTP Weaknesses
* Account Takeover Risks
