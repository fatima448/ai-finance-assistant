# Security Test Results

**Date:** 19 November 2026

## Test command

    py manage.py test accounts finances

## Result

    Ran 14 tests in 25.112s
    OK

## Verified requirements

| Requirement | Evidence |
|---|---|
| Only owners can access financial records | Tests verify another user cannot list, retrieve, update, or delete another user’s transaction. |
| Server assigns transaction ownership | Test verifies new transactions belong to the authenticated user, not a user selected by the client. |
| Login and logout | Tests verify valid login creates a session and logout removes it. |
| Invalid login rejection | Test verifies an incorrect password is rejected. |
| Account lockout | Test verifies five failed login attempts prevent login, including with the correct password. |
| Lockout recovery | Test verifies an administrator can clear a lockout using `axes_reset_username`. |
| Password recovery | Test verifies a password-reset link changes the password. |
| Single-use reset links | Test verifies a used password-reset link is rejected when reused. |
| Inactivity timeout | Test verifies a session expires after more than 15 minutes without activity. |
| Meaningful activity | Test verifies an authenticated application request refreshes the activity timestamp. |
| Background polling | Test verifies background polling does not refresh the activity timestamp. |

## Notes

Authentication uses Django sessions. Financial API views filter records by the authenticated user and assign ownership on the server. Passwords use Argon2id. django-axes enforces lockout after five failed login attempts, with a 15-minute cooldown and administrator-assisted recovery.
