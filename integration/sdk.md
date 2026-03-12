# SDK

BlockControl provides an official **Java SDK** for integrating with the platform API. The SDK is auto-generated from the OpenAPI specification and published to a public Maven artifact repository.

---

## Java SDK

### Installation

Add the BlockControl SDK to your Maven `pom.xml`:

```xml
<dependency>
  <groupId>io.blockcontrol</groupId>
  <artifactId>blockcontrol-java-sdk</artifactId>
  <version>1.0.0</version>
</dependency>
```

Or for Gradle:

```groovy
implementation 'io.blockcontrol:blockcontrol-java-sdk:1.0.0'
```

The artifact is published to the public BlockControl artifactory. If your build system requires it, add the repository:

```xml
<repository>
  <id>blockcontrol-public</id>
  <url>https://artifactory.blockcontrol.io/public</url>
</repository>
```

---

## Quick Start

### Initialize the Client

```java
import io.blockcontrol.sdk.BlockControlClient;
import io.blockcontrol.sdk.config.ClientConfig;

ClientConfig config = ClientConfig.builder()
    .baseUrl("https://api.blockcontrol.io/api/v1")
    .accessToken("your_jwt_token")
    .build();

BlockControlClient client = new BlockControlClient(config);
```

### Authenticate

```java
import io.blockcontrol.sdk.auth.AuthApi;
import io.blockcontrol.sdk.model.LoginRequest;
import io.blockcontrol.sdk.model.TokenResponse;

AuthApi authApi = client.auth();

TokenResponse tokens = authApi.login(
    LoginRequest.builder()
        .email("user@mybank.com")
        .password("securepassword")
        .build()
);

String accessToken = tokens.getAccessToken();
```

### Initiate a Transfer

```java
import io.blockcontrol.sdk.transfers.TransfersApi;
import io.blockcontrol.sdk.model.InternalTransferRequest;
import io.blockcontrol.sdk.model.TransferResponse;

TransfersApi transfersApi = client.transfers();

TransferResponse response = transfersApi.internalTransfer(
    InternalTransferRequest.builder()
        .fromAccountId("acc_admin_001")
        .toAccountId("acc_client_042")
        .tokenId("tok_feur_001")
        .amount("500.00")
        .build()
);

System.out.println("Transfer ID: " + response.getTransferId());
System.out.println("Status: " + response.getStatus());
```

### Poll Transfer Status

```java
TransferResponse status = transfersApi.getTransfer(response.getTransferId());

while (status.getStatus().equals("PENDING") || status.getStatus().equals("SUBMITTED")) {
    Thread.sleep(3000);
    status = transfersApi.getTransfer(response.getTransferId());
}

System.out.println("Final status: " + status.getStatus());
System.out.println("TX Hash: " + status.getTransactionHash());
```

---

## SDK Generation

The Java SDK is generated automatically via **GitHub Actions** from the OpenAPI spec:

1. The workflow fetches the latest Swagger spec from the BlockControl API
2. Generates Java client code using the OpenAPI Generator
3. Packages and publishes the artifact to the public repository

To regenerate the SDK after API updates, trigger the `generate-sdk` workflow in the BlockControl SDK GitHub repository.

---

## SDK Repository

The SDK source is available at:

```
https://github.com/blockcontrol/blockcontrol-java-sdk
```

Issues, questions, and contributions are welcome via GitHub Issues.

---

## Other Languages

While the official SDK is Java, the OpenAPI spec can be used to generate clients in any supported language:

```bash
# Generate a Python client
openapi-generator generate \
  -i https://api.blockcontrol.io/api/v1/api-docs \
  -g python \
  -o ./blockcontrol-python-sdk

# Generate a TypeScript/Axios client
openapi-generator generate \
  -i https://api.blockcontrol.io/api/v1/api-docs \
  -g typescript-axios \
  -o ./blockcontrol-ts-sdk
```
