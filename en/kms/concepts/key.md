# Symmetric key in {{ kms-short-name }}

A symmetric key is a set of [versions](version.md), each of which defines an algorithm and cryptographic material for data encryption or decryption operations.
A symmetric key is created along with its first version that becomes the primary one. It is used by default in key operations unless you specify a different version in the input parameters.
When [rotating keys](version.md#rotate-key), the parameters of new versions are inherited from the key parameters.

You can change the primary version of the symmetric key at any time by specifying any previous version. For additional security of your data, rotate keys on a regular basis and only use previous versions to decrypt data. This limits the lifetime of cryptographic material.

## Key parameters {#parameters}

A {{ kms-short-name }} symmetric key may have the following parameters:

* ID: Unique key identifier in {{ yandex-cloud }}. It is used for working with keys via the SDK, API, and CLI.
* Name: Non-unique key name. It can be used to work with keys in the CLI if the folder only contains a single key with this name.
* Encryption algorithm: The algorithm to be used for encryption in new versions of the key. [GCM](https://en.wikipedia.org/wiki/Galois/Counter_Mode) supports the following symmetric encryption algorithms:

    * `AES-128`: AES algorithm with 128-bit keys.
    * `AES-192`: AES algorithm with 192-bit keys.
    * `AES-256`: AES algorithm with 256-bit keys.
    * `AES-256 HSM`: AES algorithm with 256-bit keys. Encryption keys are created and cryptographic operations handled in a [Hardware Security Module (HSM)](hsm.md).

* Rotation period: Time span between automatic key rotations.
* Deletion protection: Pprevents accidental key deletion. When enabled, you cannot delete the key without disabling this option first.
* Status: Current state of the key. The following statuses are possible:

    * `Creating`: Key is being created.
    * `Active`: Key can be used for encryption and decryption.
    * `Inactive`: Key cannot be used.

    You can change the key status from `Active` to `Inactive` and vice versa using the [update](../api-ref/SymmetricKey/update) method.

## Using a symmetric key {#use}

You can use a symmetric key in data encryption and decryption operations if you have the appropriate [roles](../security/index.md#roles-list) assigned. You can temporarily disable operations with a key by revoking roles or changing its status to `Inactive`. For more information, see [{#T}](../security/index.md).

## Deleting a key {#delete}

If you delete a key or its parent resource (folder or cloud), this destroys the cryptographic material contained in it. After that, you will not be able to decrypt the data encrypted with that key.

## Use cases {#examples}

* [{#T}](../tutorials/encrypt/cli-api.md)
* [{#T}](../tutorials/encrypt/sdk.md)
* [{#T}](../tutorials/encrypt/aws-encryption-sdk.md)
* [{#T}](../tutorials/encrypt/google-tink.md)
* [{#T}](../tutorials/kms-k8s.md)
* [{#T}](../tutorials/terraform-key.md)
* [{#T}](../tutorials/terraform-secret.md)
* [{#T}](../tutorials/vault-secret.md)
* [{#T}](../tutorials/secure-password-script.md)