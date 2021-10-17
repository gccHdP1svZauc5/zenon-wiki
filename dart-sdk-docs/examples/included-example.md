---
layout: default
title: included example
parent: examples
grand_parent: dart SDK documentation
nav_order: 1
---
# Breakdown of the example included in the Zenon Dart SDK

This example.dart was provided in the first public release of the Zenon Network Dart SDK. Here is an explanation of what it does.

## Summary

This example takes a hard-coded mnemonic phrase, and shows the results of running some of the KeyStore functions on it. Essentially, it provides the information used in initializing a new wallet.

## Imports 

```dart
    import 'package:hex/hex.dart';
    import 'package:znn_sdk_dart/znn_sdk_dart.dart';
```

This example imports the whole SDK in one go, but it uses only functions from KeyStore and KeyPair.

## Main part

This is the main routine of the example: 
    
```dart
    Future<void> main() async {
      final mnemonic =
          'route become dream access impulse price inform obtain engage ski believe awful absent pig thing vibrant possible exotic flee pepper marble rural fire fancy';
    
      var keyStore = KeyStore.fromMnemonic(mnemonic);
      var keyPair = keyStore.getKeyPair(0);
      var privateKey = keyPair.getPrivateKey();
      var publicKey = await keyPair.getPublicKey();
      var address = await keyPair.address;
    
      print('entropy: ${keyStore.entropy}');
      print('private key: ${HEX.encode(privateKey!)}');
      print('public key: ${HEX.encode(publicKey!)}');
      print('address: $address');
      print('core bytes: ${HEX.encode(address!.core!)}');
    }
```

A line-by-line breakdown follows below.

### Function declaration

```dart
    Future<void> main() async {
```       

This function is [asynchronous](https://dart.dev/codelabs/async-await). In Dart this is declared by specifying a `Future<T>` return type and including the `async` keyword. It is necessary for this simple-looking function to be asynchronous because it obtains output from other asynchronous functions in the SDK.

```dart
    final mnemonic =
        'route become dream access impulse price inform obtain engage ski believe awful absent pig thing vibrant possible exotic flee pepper marble rural fire fancy';
```		

This mnemonic will be used to calculate some values.

### Key derivation

```dart
    var keyStore = KeyStore.fromMnemonic(mnemonic);
```

Initializes a KeyStore using the mnemonic as a seed. KeyStore is a class which simply holds the information about the key, in various equivalent formats: The mnemonic (list of words as in BIP39), the [entropy](https://www.cs.utexas.edu/users/moore/acl2/manuals/current/manual/index-seo.php/BITCOIN____BIP39-ENTROPY) from which the seed would have been derived (simply a list of bits), and the seed (the entropy represented as a hexadecimal number).

```dart
    var keyPair = keyStore.getKeyPair(0);
```

Generates a public and private key pair from the supplied seed, using [Ed25519](https://en.wikipedia.org/wiki/EdDSA).

```dart
    var privateKey = keyPair.getPrivateKey();
    var publicKey = await keyPair.getPublicKey();
```	

Stores the results of the new key pair. The `await` keyword indicates that the function should wait until the public key derivation is complete to get it.

```dart
    var address = await keyPair.address;
```

Gets the address associated with the public key, which is a [SHA-3](https://en.wikipedia.org/wiki/SHA-3) hash of the key, with a `z` prepended to distinguish it as a Zenon address.

### Display of results

```dart
    print('entropy: ${keyStore.entropy}');
    print('private key: ${HEX.encode(privateKey!)}');
    print('public key: ${HEX.encode(publicKey!)}');
    print('address: $address');
    print('core bytes: ${HEX.encode(address!.core!)}');
```

The output of these functions is then printed to the console. The last line simply produces the address encoded as a hexadecimal number.

### Output

The expected output is:

    entropy: bc827d0a00a72354dce4c44a59485288500b49382f9ba88a016351787b7b15ca
    private key: d6b01f96b566d7df9b5b53b1971e4baeb74cc64167a9843f82d04b2194ca4863
    public key: 3e13d7238d0e768a567dce84b54915f2323f2dcd0ef9a716d9c61abed631ba10
    address: z1qqjnwjjpnue8xmmpanz6csze6tcmtzzdtfsww7
    core bytes: 0025374a419f32736f61ecc5ac4059d2f1b5884d
