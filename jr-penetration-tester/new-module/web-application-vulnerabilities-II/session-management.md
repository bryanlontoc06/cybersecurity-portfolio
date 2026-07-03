# 📌 Session Management

## What is Session Management

### Session Management Lifecycle

1. Session Creation
   - Description: The generation of a unique identifier to track user activity. While authentication (providing credentials like a username and password) locks a session to a specific user identity, many applications generate an anonymous session the moment a user visits the site to track pre-authentication behavior (e.g., shopping carts).

   - Developer Focus: Session values must be generated using cryptographically secure pseudo-random number generators (CSPRNG) to prevent predictability and session fixation attacks.

2. Session Tracking
   - Description: The mechanism used to maintain state over the inherently stateless HTTP protocol. The client automatically transmits the session identifier (typically via HTTP Cookie headers) with every subsequent request. The backend intercepts this token and performs a server-side lookup (e.g., in a database or an in-memory cache like Redis) to identify the user and verify their access control roles.

   - Developer Focus: Insecure tracking implementations can expose the application to Session Hijacking, allowing threat actors to impersonate legitimate users if the token is intercepted or predicted.

3. Session Expiry
   - Description: The enforcement of a time-based threshold (Time-to-Live / TTL) on active sessions. Because HTTP is stateless, a web server cannot natively detect if a user has abruptly closed their browser window or lost connection.

   - Developer Focus: Every session must have a defined lifespan. If an incoming request supplies an expired token, the server must invalidate it, deny access, and force a redirection to the login interface to restart the lifecycle.

4. Session Termination
   - Description: The explicit destruction of a session initiated by a user action (e.g., clicking "Logout"). Unlike expiration, termination cuts the lifecycle short regardless of any remaining active time.

   - Developer Focus: True termination must occur on the server side (deleting the session record from the database/cache). Merely clearing the cookie on the client side leaves the token active on the backend, creating a vulnerability where a threat actor can reuse the token for persistent, unauthorized access.

## Authentication vs Authorisation

The IAAA Framework in Session Management

1. Identification
   - Description: The initial process where a user claims a specific identity within the application. This is typically done by providing a unique identifier, such as a username, email address, or account ID.

   - Developer Focus: This stage only establishes an assertion of identity; no trust is granted yet. Identifiers should be handled securely to prevent username enumeration vulnerabilities.

2. Authentication
   - Description: The process of verifying the user's claimed identity by demanding proof. This usually involves presenting a secret known only to the legitimate user, such as a password, biometric data, or a multi-factor authentication (MFA) token.

   - Developer Focus: Once authentication is successful, the server creates an authenticated session and issues a session token. Passwords must be securely hashed on the backend using modern algorithms (e.g., Argon2, bcrypt).

3. Authorization
   - Description: The process of enforcing access controls to ensure the authenticated user has the necessary permissions to execute a requested action (e.g., distinguishing between a regular candidate viewing a job post and an HR manager modifying it).

   - Developer Focus: During the Session Tracking phase, the backend intercepts the session token, retrieves the associated user identity, and evaluates their Role-Based Access Control (RBAC) permissions before serving data or committing database mutations.

4. Accountability
   - Description: The practice of maintaining a permanent, audit-ready record (log) of all user actions performed within the application. This links specific activities back to a precise session and user identity.

   - Developer Focus: Secure logging is critical for incident response and forensics. Application logs must record the session ID, timestamp, IP address, endpoint requested, and action taken, ensuring logs are protected from tampering or deletion.

## Cookies vs Tokens

### Cookie-Based Session Management

[Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)

- Secure - Indicates to the browser that the cookie may only be transmitted over verified HTTPS channels. If there are certificate errors or HTTP is used, the cookie value will not be transmitted.
- HTTPOnly - Indicates to the browser that the cookie value may not be read by client-side JavaScript.
- Expire - Indicates to the browser when a cookie value will no longer be valid and should be removed.
- SameSite - Indicates to the browser whether the cookie may be transmitted in cross-site requests to help protect against CSRF attacks.

### Benefits and Drawbacks

The benefits and drawbacks of each of these methods are directly related, so let's take a look:

| Cookie-Session Management                                                                                                                             | Token-Based Session Management                                                                                                                                            |
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cookie is automatically sent by the browser with each request                                                                                         | Token has to be submitted as a header with each request using client-side JavaScript                                                                                      |
| Cookie attributes can be used to enhance the browser's protection of the cookie                                                                       | Tokens do not have automatic security protections enforced and should, therefore, be safeguarded against disclosures                                                      |
| Cookies can be vulnerable to conventional client-side attacks such as CSRF, where the browser is tricked into making a request on behalf of the user. | As the token is not automatically added to any request and cannot be read from LocalStorage by other domains, conventional client-side attacks such as CSRF are blocked.  |
| As cookies are locked to a specific domain, it can be difficult to use them securely in decentralised web applications.                               | Tokens work well in decentralised web applications, as they are managed through JavaScript and can often contain all the information required to verify the token itself. |

## Securing the Session Lifecycle

📊 Session Management Lifecycle Vulnerabilities

| Lifecycle Phase | Core Vulnerability Type                               | Primary Exploitation Result                                 |
| --------------- | ----------------------------------------------------- | ----------------------------------------------------------- |
| Creation        | Weak ID generation, No Token Rotation, Open Redirects | Session Fixation, Token Forgery, MITM Leakage               |
| Tracking        | Insufficient RBAC/Ownership checks, Poor Audit Trails | Privilege Escalation (Vertical/Horizontal Bypass)           |
| Expiry          | Excessive Time-to-Live (TTL), No Geo/IP monitoring    | Extended attack window for stolen session tokens            |
| Termination     | Client-only Logout, No Global Session Revocation      | Persistent unauthorized access; Failure to remediate breach |

## Conlusion

Defences

To defend against these attacks, it is important to implement a secure session management lifecycle. While several items were touched on in this room, let's take a look at a recap:

- The session's values must be stored securely, regardless of being a cookie or a token.
- The session values themselves must be either sufficiently random and non-guessable or use a signing mechanism to ensure that they cannot be tampered with.
- Sessions should be used to track user actions and perform authorisation checks to ensure the user can perform the requested action.
- Sessions should expire after a set amount of time to prevent them from being used for persistent access.
- If the logout button is pressed, the session should be removed client-side and invalidated server-side. Otherwise, a user would be unable to destroy their session if it was compromised.
