---
layout: default
title: making rpc calls with the sdk
parent: examples
grand_parent: dart SDK documentation
nav_order: 2
---
# Example: Using the Zenon Dart SDK to get data from the network

## Summary

This is an example showing how to use the the [Zenon Dart SDK](https://testnet.znn.space/#!downloads.md) in a very simple application. As an example we will obtain a list of the first ten pillars on the network, and the cost in QSR to register a pillar, the same information as obtained in the [RPC API example](rpc-api-example.md) where we exploit some of the SDK's internal methods.

## Setup

You should have [Dart](https://dart.dev/get-dart) installed, be familiar with running a Dart program, and obtain the [Zenon Dart SDK](https://testnet.znn.space/#!downloads.md). To import the SDK into your Dart project, the dependencies section of your `pubspec.yaml` file should include where to find the SDK in:

```yaml
dependencies:
  znn_sdk_dart:
    path: [PATH TO SDK FOLDER]
```

You should have `znnd` running on your local machine. When starting `znnd` you should see output that looks something like this (some lines are omitted for clarity):

    Starting znnd.
    current time is 2021-10-12 12:39:47
    version: v4.0.0
    git-commit-hash: edc201129f8b65e63a03a8e3f8508c564c8273cb
    znnd will use at most 8 cpu-cores
    ...
    Using the following znnd config: {
        "DataPath": "[REDACTED]",
        "WalletPath": "[REDACTED]",
        "GenesisFile": "",
        "Name": "znn-node",
        "LogLevel": "info",
        "PoWLinksURL": "",
        "Pillar": null,
        "Sentinel": null,
        "RPC": {
            "EnableHTTP": false,
            "EnableWS": false,
            "EnableIPC": true,
            "HTTPHost": "127.0.0.1",
            "HTTPPort": 35997,
            "WSHost": "127.0.0.1",
            "WSPort": 35998,
            "IPCPath": "[REDACTED]",
            "Endpoints": null,
            "HTTPVirtualHosts": null,
            "HTTPCors": [
                "*"
            ],
            ....
        },
	...
    znnd successfully prepared
    znnd successfully started
    *** Node status ***
    * No pillar configured for current node
    * No sentinel configured for current node
    znnd RPC service successfully started
	
While the service is running, it should be possible to connect to it on your local machine from your Dart script as long as `EnableIPC` is `true`.

We will make the following calls, as described in the [official documentation](https://testnet.znn.space/#!api.md):

pillar.getAll:

```json
    {
        "jsonrpc": "2.0",
        "id": 3,
        "method": "embedded.pillar.getAll",
        "params": [0, 5]
    }
```	

The parameters for `pillar.getAll` are the page number and the number of pillars per page to return.

```jaon
pillar.getQsrRegistrationCost

    {
        "jsonrpc": "2.0",
        "id": 2,
        "method": "embedded.pillar.getQsrRegistrationCost",
        "params": []
    }
```

## Breakdown

### Imports

We are using the Zenon Dart SDK:

```dart
    import 'package:znn_sdk_dart/znn_sdk_dart.dart';
```

### Initialization

We need an asynchronous function to be able to wait for the results of RPC calls. Then, we initialize a Zenon object, and direct its websocket client to connect to the RPC on our local machine at 'ws://127.0.0.1:35998'.

```dart
    Future<void> main() async {
      var zenon = Zenon();
    
      await zenon.wsClient.initialize(
        'ws://127.0.0.1:35998',
        retry: false,
      );
```

Now we can direct our Zenon object to make API calls for us. The `await` keyword indicates that the result should be received before the variables `pillarData` and `pillarCost` can be used.

```dart
    var pillarData = await zenon.embedded.pillar.getAll(pageIndex: 0, pageSize: 10);
    var pillarCost = await zenon.embedded.pillar.getQsrRegistrationCost();
```

The list of pillars contains a lot of information. To be able to display it nicely, we will extract just the names:

```dart
    var pillarNames = [];
    for (var p in pillarData.list) {
        pillarNames.add(p.name);
    }
```

Then we can print out our results:

```dart
    print('There are ${pillarData.count} pillars');
    print('First 10 pillars: $pillarNames');
    print('Pillar QRS registration cost: ${pillarCost / 100000000}');
```

Then we close the connection and end the program:

```dart
    zenon.wsClient.stop();
    }
```

## Expected output

    There are 90 pillars
    First 10 pillars: [LeokidZenon, zzzNODA, TheAncientzOne, tophata, MUZZO, apuDAOarray, alekspillar, m9snikoda, DARKSIDE, Greyz]
    Pillar QRS registration cost: 1020000.0

## Complete code

```dart
    import 'package:znn_sdk_dart/znn_sdk_dart.dart';

    Future<void> main() async {
    var zenon = Zenon();

    await zenon.wsClient.initialize(
        'ws://127.0.0.1:35998',
        retry: false,
    );

    var pillarData =
        await zenon.embedded.pillar.getAll(pageIndex: 0, pageSize: 10);
    var pillarCost = await zenon.embedded.pillar.getQsrRegistrationCost();

    var pillarNames = [];
    for (var p in pillarData.list) {
        pillarNames.add(p.name);
    }
    print('There are ${pillarData.count} pillars');
    print('First 10 pillars: $pillarNames');
    print('Pillar QRS registration cost: ${pillarCost / 100000000}');

    zenon.wsClient.stop();
}
```