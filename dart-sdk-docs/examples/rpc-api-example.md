---
layout: default
title: making rpc calls with the sdk api functions
parent: examples
grand_parent: dart SDK documentation
nav_order: 3
---
# Example: Making calls to the znnd RPC from the Dart SDK API functions

## Summary

This is an example showing how the Zenon SDK communicates with the [Zenon daemon](https://testnet.znn.space/#!downloads.md) `znnd` from within a Dart program to make calls through the RPC. The daemon communicates with the network and can be used to obtain information. As an example we will obtain a list of the first ten pillars on the network, and the cost in QSR to register a pillar, in two different ways. First, by making calls directly to the websocket client, and second, by using the functions in one of the API classes.

**Note:** it's not best practice to access these functions this way - they have another layer of wrapping in a complete class in `zenon.dart`. Don't use the code from this example for a real app - it's for understanding how the SDK works internally. To see how to use the SDK in an app, see [this example](sdk-communication-example.md).

## Setup

You should have [Dart](https://dart.dev/get-dart) installed, be familiar with running a Dart program, and obtain the [Zenon Dart SDK](https://testnet.znn.space/#!downloads.md).

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

```json
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

We are using the Zenon Dart SDK. We will make the same requests in two different ways: by sending the name of the method, and its parameters, to a websocket client directly, and by using the SDK API functions.

```dart
    import 'package:znn_sdk_dart/znn_sdk_dart.dart';
    import 'package:znn_sdk_dart/src/api/api.dart';
```

### Initialization

We need an asynchronous function to be able to wait for the results of RPC calls. 

```dart
    Future<void> main() async {
```

First, we will define a websocket client. WsClient is defined in package:znn_sdk_dart/src/client/websocket.dart
```dart	
      var client = WsClient();
      var initialized =
          await client.initialize('ws://127.0.0.1:35998', retry: false);
      print('Websocket connection initialized: $initialized \n\n----\n');
	  
If we can't establish a connection, just quit the program:

```dart	  
      if (initialized == false) {
        print('exiting');
        return (null);
      }
```

First, we will translate the request into an appropriate Dart variables. Then we can use `Client.sendRequest(String method, [dynamic parameters])` to make requests. The websocket client interface is defined in package:znn_sdk_dart/src/client/interfaces.dart. We are going to obtain the first page, with ten pillars on it.

```dart
      var request1 = 'embedded.pillar.getAll';
      var page = 0;
      var numberPerPage = 10;
```	  

Then we can direct the websocket client to obtain this information:

```dart    
      var requestResult1 = await client.sendRequest(request1, [page, numberPerPage]);
```

The list of pillars contains a lot of information. The JSON result is automatically converted to a Dart `Map`, a type analogous to a `Dict` in some other popular languages. This means we can extract fields from it with the index (`[]`) operators. We will extract just the names of the pillars.

```dart
      var pillarNames = [];
      for (var p in requestResult1['list']) {
        pillarNames.add(p['name']);
      }
```

For our second request we will get the current QSR cost to register a pillar. This one doesn't require any parameters.

```dart
      var request2 = 'embedded.pillar.getQsrRegistrationCost';
      var requestResult2 = await client.sendRequest(request2);
```

And then we can display the output:

```dart    
      print('Output from direct client requests: ');
      print('There are ${requestResult1["count"]} pillars');
      print('First 10 pillars: ${pillarNames}');
      print(
          'Pillar QRS registration cost: ${requestResult2 / 100000000}\n\n----\n');
```
		  
Now let's do the same thing, but with the built-in API access functions. First, we should initialize the API to use our established client:

```dart
      var embeddedApi = EmbeddedApi();
      embeddedApi.setClient(client);
```

Then we can use the methods in `EmbeddedApi` (package:znn_sdk_dart/src/api/embedded.dart) to make the same calls. We are only getting results from `EmbeddedApi.pillar` in this example, but the `EmbeddedApi` class in the SDK allows access to all of the RPC endpoints described in the documentation referenced above. 

```dart    
      var apiRequestResult1 =
          await embeddedApi.pillar.getAll(pageIndex: page, pageSize: numberPerPage);
```

Format the pillar names in the same way:	

```dart		  
      var apiPillarNames = [];
      for (var p in apiRequestResult1.list) {
        apiPillarNames.add(p.name);
```

Obtain the QSR cost to register a pillar:

```dart 
      var apiRequestResult2 = await embeddedApi.pillar.getQsrRegistrationCost();
```

Print out the results:

```dart    
      print('Output from EmbeddedApi calls:');
      print('There are ${apiRequestResult1.count} pillars');
      print('First 10 pillars: $apiPillarNames');
      print('Pillar QRS registration cost: ${apiRequestResult2 / 100000000}');
```

And when we are done, close the websocket connection to end the program:

```dart
      client.stop();
    }
```

## Expected output 

This is the output as of 12 October 2021. The values might change, but should look something like this:

    Websocket connection initialized: true 
    
    ----
    
    Output from direct json RPC requests: 
    There are 89 pillars
    First 10 pillars: [LeokidZenon, zzzNODA, TheAncientzOne, tophata, MUZZO, apuDAOarray, alekspillar, m9snikoda, DARKSIDE, Greyz]
    Pillar QRS registration cost: 1010000.0
    
    ----
    
    Output from EmbeddedApi calls:
    There are 89 pillars
    First 10 pillars: [LeokidZenon, zzzNODA, TheAncientzOne, tophata, MUZZO, apuDAOarray, alekspillar, m9snikoda, DARKSIDE, Greyz]
    Pillar QRS registration cost: 1010000.0
	

## Complete code
```dart
    import 'package:znn_sdk_dart/znn_sdk_dart.dart';
    import 'package:znn_sdk_dart/src/api/api.dart';
    
    Future<void> main() async {
      var client = WsClient();
      var initialized =
          await client.initialize('ws://127.0.0.1:35998', retry: false);
      print('Websocket connection initialized: $initialized \n\n----\n');
      if (initialized == false) {
        print('exiting');
        return (null);
      }
    
      var request1 = 'embedded.pillar.getAll';
      var page = 0;
      var numberPerPage = 10;
    
      var requestResult1 =
          await client.sendRequest(request1, [page, numberPerPage]);
      var pillarNames = [];
      for (var p in requestResult1['list']) {
        pillarNames.add(p['name']);
      }
    
      var request2 = 'embedded.pillar.getQsrRegistrationCost';
      var requestResult2 = await client.sendRequest(request2);
    
      print('Output from direct client requests: ');
      print('There are ${requestResult1["count"]} pillars');
      print('First 10 pillars: ${pillarNames}');
      print(
          'Pillar QRS registration cost: ${requestResult2 / 100000000}\n\n----\n');
    
      var embeddedApi = EmbeddedApi();
      embeddedApi.setClient(client);
    
      var apiRequestResult1 =
          await embeddedApi.pillar.getAll(pageIndex: page, pageSize: numberPerPage);
      var apiPillarNames = [];
      for (var p in apiRequestResult1.list) {
        apiPillarNames.add(p.name);
      }
      var apiRequestResult2 = await embeddedApi.pillar.getQsrRegistrationCost();
    
      print('Output from EmbeddedApi calls:');
      print('There are ${apiRequestResult1.count} pillars');
      print('First 10 pillars: $apiPillarNames');
      print('Pillar QRS registration cost: ${apiRequestResult2 / 100000000}');
    
      client.stop();
    }
```    