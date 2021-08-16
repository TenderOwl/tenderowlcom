---
title: "Server microframework for the Dart language"
date: 2020-12-18T11:26:21+03:00
draft: false
description: "High performance, multithreaded, extensible Dart server framework."
---


Laska is a server-side microframework for Dart.
Currently, in development, not for production use.
<!--more-->

## Features


- [x] Dynamic routing with placeholders and wildcards
- [x] Concurrency via Isolates
- [x] Extensible Middleware support
- [ ] Template rendering
- [ ] Logging


## How to use it

```dart
import 'dart:io';

import 'package:laska/laska.dart';

void main() async {
  // Create new Laska object with 2 [Isolate]
  final laska = Laska(isolateCount: 2);

  // Set global BasicAuth middleware 
  laska.Use(BasicAuth('laska', 'ermine', realm: 'Access to private zone'));

  laska.GET('/users/:userId', getUserById);
  laska.POST('/users/', createUser);

  // Start server
  await run(laska);
}

void getUserById(Context context) async {
  await context.HTML('User: <b>${context.params['userId']}</b>');
}

void createUser(Context context) async {
  await context.JSON({'status': 'created'}, statusCode: HttpStatus.created);
}
```