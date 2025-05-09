# Encrypting data using the {{ yandex-cloud }} SDK

You can use {{ kms-full-name }} with the {{ yandex-cloud }}  SDK. The SDK is available for [Java](https://github.com/yandex-cloud/java-sdk), [Go](https://github.com/yandex-cloud/go-sdk), [Python](https://github.com/yandex-cloud/python-sdk), and [Node.js](https://github.com/yandex-cloud/nodejs-sdk).

The {{ yandex-cloud }} SDK is most convenient for encrypting small amounts of data (the limit on the size of plaintext is 32 KB). To encrypt larger amounts of data, we recommend using the [AWS Encryption SDK](../../../kms/tutorials/encrypt/aws-encryption-sdk.md) or [Google Tink](../../../kms/tutorials/encrypt/google-tink.md). They encrypt data using [envelope encryption](../../../kms/concepts/envelope.md).

## Adding dependencies {#dependency}

Before you start, you need to add dependencies.

{% list tabs group=programming_language %}

- Java {#java}

    Add dependencies using [Apache Maven](https://maven.apache.org/):

    ```java
    <dependency>
        <groupId>com.yandex.cloud</groupId>
        <artifactId>java-sdk-services</artifactId>
        <version>2.4.2</version>
    </dependency>
    ```

- Go {#go}

    Install the SDK:

    ```go
    go get github.com/yandex-cloud/go-sdk
    ```

{% endlist %}

## Authentication {#auth}

You can authenticate using: 
* [Service account linked to the {{ yandex-cloud }}](#vm) VM.
* [Any service account](#sa).
* [Yandex account](#yandex-acc).

### Authentication using the service account linked to the {{ yandex-cloud }} VM {#vm}

{% list tabs group=programming_language %}

- Java {#java}

    Get authenticated using the service account linked to the VM:

    ```java
    CredentialProvider credentialProvider = Auth.computeEngineBuilder().build();
    ```

- Go {#go}

    Authenticate using the service account linked to the VM:

    ```Go
    credentials := ycsdk.InstanceServiceAccount()
    ```

{% endlist %}

### Authentication using any service account {#sa}

`key.json` must contain the authorized service account key. For information on how to create an authorized key, see [{#T}](../../../iam/operations/authentication/manage-authorized-keys.md#create-authorized-key).

{% list tabs group=programming_language %}

- Java {#java}

    Authenticate using any service account:

    ```java
    CredentialProvider credentialProvider = Auth.apiKeyBuilder().fromFile(Paths.get("key.json")).build();

    ```

- Go {#go}

    Authenticate using any service account:

    ```Go
    authorizedKey, err := iamkey.ReadFromJSONFile("key.json")
    if err != nil {...}
    credentials, err := ycsdk.ServiceAccountKey(authorizedKey)
    if err != nil {...}
    ```

{% endlist %}


### Authentication using a Yandex account {#yandex-acc}

The `token` variable is your [OAuth token](../../../iam/concepts/authorization/oauth-token.md).

{% list tabs group=programming_language %}

- Java {#java}

    Authenticate using a Yandex account:

    ```java
    CredentialProvider credentialProvider = Auth.oauthTokenBuilder().build();
    ```

- Go {#go}

    Authenticate using a Yandex account:

    ```Go
    credentials := ycsdk.OAuthToken(token)
    ```

{% endlist %}


## Data encryption and decryption {#enc-dec}

Use the `encrypt` and `decrypt` methods to encrypt and decrypt data. The code uses the following variables: 
* `endpoint`: `{{ api-host }}:443`.
* `keyId`: ID of the [KMS key](../../../kms/concepts/key.md).
* `plaintext`: Plain text (no more than 32 KB).
* `ciphertext`: Ciphertext.
* `aad`: [AAD context](../../../kms/concepts/symmetric-encryption.md#add-context).

{% list tabs group=programming_language %}

- Java {#java}

    ```Java
    SymmetricCryptoServiceBlockingStub symmetricCryptoService = ServiceFactory.builder()
        .endpoint(endpoint)
        .credentialProvider(credentialProvider)
        .build()
        .create(
            SymmetricCryptoServiceBlockingStub.class,
            SymmetricCryptoServiceGrpc::newBlockingStub
        );

    ...

    byte[] ciphertext = symmetricCryptoService.encrypt(SymmetricEncryptRequest.newBuilder()
        .setKeyId(keyId)
        .setPlaintext(ByteString.copyFrom(plaintext))
        .setAadContext(ByteString.copyFrom(aad))
        .build()
    ).getCiphertext().toByteArray();

    ...

    byte[] plaintext = symmetricCryptoService.decrypt(SymmetricDecryptRequest.newBuilder()
        .setKeyId(keyId)
        .setCiphertext(ByteString.copyFrom(ciphertext))
        .setAadContext(ByteString.copyFrom(aad))
        .build()
    ).getPlaintext().toByteArray();

    ```

- Go {#go}

    ```Go
    sdk, err := ycsdk.Build(context, ycsdk.Config{
      Endpoint:    endpoint,
      Credentials: credentials,
    })
    if err != nil {...}
    
    ...
    
    response, err := sdk.KMSCrypto().SymmetricCrypto().Encrypt(context, &kms.SymmetricEncryptRequest{
      KeyId:      keyId,
      Plaintext:  plaintext,
      AadContext: aad,
    })
    if err != nil {...}
    ciphertext := response.Ciphertext
    
    ...
    
    response, err := sdk.KMSCrypto().SymmetricCrypto().Decrypt(context, &kms.SymmetricDecryptRequest{
      KeyId:      keyId,
      Ciphertext: ciphertext,
      AadContext: aad,
    })
    if err != nil {...}
    plaintext := response.Plaintext
    ```

{% endlist %}

#### See also {#see-also}

* [{{ yandex-cloud }} Java SDK](https://github.com/yandex-cloud/java-sdk).
* [Examples of how to use {{ kms-short-name }} with the Java SDK](https://github.com/yandex-cloud/java-sdk/tree/master/java-sdk-examples/src/main/java/yandex/cloud/sdk/examples/kms).
* [{{ yandex-cloud }} Go SDK](https://github.com/yandex-cloud/go-sdk).
