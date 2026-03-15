# JWT format

This page describes the structure and required claims for JWTs used with the BlockControl API — both BlockControl-issued and bank-issued tokens.

---

## JWT structure

A JWT consists of three base64url-encoded parts separated by dots:

```
{header}.{payload}.{signature}
```

**Example (decoded):**

### Header

```json
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "bc-key-2025-01"
}
```

| Field | Value | Description |
|---|---|---|
| `alg` | `RS256` | Algorithm — RSA with SHA-256. Only RS256 is accepted. |
| `typ` | `JWT` | Token type |
| `kid` | string | Key ID — used to select the correct public key from the JWKS endpoint |

---

### Payload (claims)

```json
{
  "iss": "https://api.mybank.com",
  "sub": "emp_001",
  "aud": "https://api.{alias}.blockcontrol.io",
  "exp": 1741952400,
  "iat": 1741951200,
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "role": "USER"
}
```

| Claim | Required | Description |
|---|---|---|
| `iss` | ✅ | Issuer — the URL of the bank's auth server or `blockcontrol.io` for BC-issued tokens |
| `sub` | ✅ | Subject — the employee ID |
| `aud` | ✅ | Audience — must match the BlockControl API base URL for your counterparty |
| `exp` | ✅ | Expiration time (Unix timestamp). Recommended maximum: `iat + 1200` |
| `iat` | ✅ | Issued at (Unix timestamp) |
| `counterparty_guid` | ✅ | The GUID of the counterparty this token is authorized for |
| `role` | ✅ | Employee role: `ADMIN`, `USER`, or `READ_ONLY` |

{% hint style="warning" %}
Tokens with an `exp` value more than **1200 seconds** from `iat` are rejected by BlockControl as a security policy. Configure your token validity period accordingly.
{% endhint %}

---

### Signature

The token is signed using RSA-SHA256 with the issuer's private key (minimum 2048 bits). BlockControl verifies the signature using the registered public key.

---

## Generating a bank JWT (example — Java)

```java
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.security.PrivateKey;
import java.util.Date;

String token = Jwts.builder()
    .setIssuer("https://auth.mybank.com")
    .setSubject("emp_001")
    .setAudience("https://api.mybank.blockcontrol.io")
    .setIssuedAt(new Date())
    .setExpiration(new Date(System.currentTimeMillis() + 1200_000))
    .claim("counterparty_guid", "ac75f1a6-0abe-11ee-be56-0242ac120002")
    .claim("role", "USER")
    .signWith(privateKey, SignatureAlgorithm.RS256)
    .compact();
```

---

## Validating a BlockControl JWT (example — Java)

```java
import io.jsonwebtoken.Jwts;
import java.security.PublicKey;

Claims claims = Jwts.parserBuilder()
    .setSigningKey(blockcontrolPublicKey)
    .build()
    .parseClaimsJws(token)
    .getBody();

String counterpartyGuid = claims.get("counterparty_guid", String.class);
String role = claims.get("role", String.class);
```

Use BlockControl's public key retrieved from the [JWKS endpoint](authorization-via-jwt.md#obtaining-blockcontrols-public-key).
