# willowsec

Java security building blocks, organized as a Maven multi-module project:

- **common** — shared utilities and exception types used across modules.
- **crypto** — cryptographic primitives and helpers (encryption, hashing, key management), built on BouncyCastle.
- **iam** — identity and access management: authentication, authorization, and token handling (JWT/JOSE via Nimbus).
- **api-security** — secure API development helpers (request validation, security headers, rate limiting), built on `iam` and `crypto`.

## Requirements

- Java 21
- Maven 3.9+

## Build

```
mvn clean install
```
