# Halite

[![Build Status](https://travis-ci.org/paragonie/halite.svg?branch=master)](https://travis-ci.org/paragonie/halite)

> **Note**: This is unreleased development code.

Halite is a high-level cryptography interface that relies on [libsodium](https://pecl.php.net/package/libsodium)
for all of its underlying cryptography operations.

Halite was created by [Paragon Initiative Enterprises](https://paragonie.com) as
a result of our continued efforts to improve the ecosystem and make [cryptography in PHP](https://paragonie.com/blog/2015/09/state-cryptography-in-php)
safer and easier to implement.

It's released under the GPLv3 license. [Commercial licenses are available](https://paragonie.com/contact) from
Paragon Initiative Enterprises if you wish to implement Halite in an application
without making your source code available under a GPL-compatible license.

## Using Halite in Your Applications

1. [Install Libsodium and the PHP Extension](https://paragonie.com/book/pecl-libsodium/read/00-intro.md#installing-libsodium)
2. `composer require paragonie/halite`

**Halite Version 3 requires PHP 7.0.0 or newer!**

## Using Halite in Your Project

Check out the [documentation](doc). The basic Halite API is designed for simplicity:

  * Encryption
    * Symmetric
       * `Symmetric\Crypto::encrypt`([`HiddenString`](doc/Classes/HiddenString.md), [`EncryptionKey`](doc/Classes/Symmetric/EncryptionKey.md), `bool?`): `string`
       * `Symmetric\Crypto::decrypt`(`string`, [`EncryptionKey`](doc/Classes/Symmetric/EncryptionKey.md), `bool?`): [`HiddenString`](doc/Classes/HiddenString.md)
    * Asymmetric
       * Anonymous
         * `Asymmetric\Crypto::seal`([`HiddenString`](doc/Classes/HiddenString.md), [`EncryptionPublicKey`](doc/Classes/Asymmetric/EncryptionPublicKey.md), `bool?`): `string`
         * `Asymmetric\Crypto::unseal`(`string`, [`EncryptionSecretKey`](doc/Classes/Asymmetric/EncryptionSecretKey.md), `bool?`): [`HiddenString`](doc/Classes/HiddenString.md)
       * Authenticated
         * `Asymmetric\Crypto::encrypt`([`HiddenString`](doc/Classes/HiddenString.md), [`EncryptionSecretKey`](doc/Classes/Asymmetric/EncryptionSecretKey.md), [`EncryptionPublicKey`](doc/Classes/Asymmetric/EncryptionPublicKey.md), `bool?`): `string`
         * `Asymmetric\Crypto::decrypt`(`string`, [`EncryptionSecretKey`](doc/Classes/Asymmetric/EncryptionSecretKey.md), [`EncryptionPublicKey`](doc/Classes/Asymmetric/EncryptionPublicKey.md), `bool?`): [`HiddenString`](doc/Classes/HiddenString.md)
  * Authentication
    * Symmetric
       * `Symmetric\Crypto::authenticate`(`string`, [`AuthenticationKey`](doc/Classes/Symmetric/AuthenticationKey.md), `bool?`): `string`
       * `Symmetric\Crypto::verify`(`string`, [`AuthenticationKey`](doc/Classes/Symmetric/AuthenticationKey.md), `string`, `bool?`): `bool`
    * Asymmetric
       * `Asymmetric\Crypto::sign`(`string`, [`SignatureSecretKey`](doc/Classes/Asymmetric/SignatureSecretKey.md), `bool?`): `string`
       * `Asymmetric\Crypto::verify`(`string`, [`SignaturePublicKey`](doc/Classes/Asymmetric/SignaturePublicKey.md), `string`, `bool?`): `bool`

### Example: Encrypting a message with a password-derived key

```php
<?php
use ParagonIE\Halite\HiddenString;
use ParagonIE\Halite\KeyFactory;
use ParagonIE\Halite\Symmetric\Crypto as Symmetric;

$passwd = new HiddenString('correct horse battery staple');
// Use random_bytes(16); to generate the salt:
$salt = "\xdd\x7b\x1e\x38\x75\x9f\x72\x86\x0a\xe9\xc8\x58\xf6\x16\x0d\x3b";

$encryptionKey = KeyFactory::deriveEncryptionKey($passwd, $salt);

$message = new HiddenString('This is a confidential message for your eyes only.');
$ciphertext = Symmetric::encrypt($message, $encryptionKey);
echo $ciphertext, "\n";
```

This should produce something similar to:

    MUIDAEpQznohvNlQ-ZRk-ZZ59Mmox75D_FgAIrXY2cUfStoeL-GIeAe0m-uaeURQdPsVmc5XxRw3-2x5ZAsZH_es37qqFuLFjUI-XK9uG0s30YTsorWfpHdbnqzhRuUOI09c-cKrfMQkNBNm0dDDwZazjTC48zWikRHSHXg8NXerVDebzng1aufc_S-osI_zQuLbZDODujEnpbPZhMMcm4-SWuyVXcBPdGZolJyT