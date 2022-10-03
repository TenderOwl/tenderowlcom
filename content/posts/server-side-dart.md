---
title: "Server-side Dart"
date: 2022-10-04T01:26:21+03:00
draft: false
tags: dart, framework, server-side, programming
description: "Server-side Dart framework for micro-services and small applications"
---


## Disclaimer

Laska is under active development and is not supposed to be used in production. API may change dramatically in future versions.

## Intro

It's not a secret that Dart's popularity comes from the Flutter framework and its acceptance by the community. I love Flutter as well, but what I miss is server-side. How cool it could be to be a full-stack developer by using only Dart language?

<!--more-->

There was a time, I want to start a new project and have to choose what technologies to use for the frontend and backend parts as well. Research gets me to [Aqueduct](https://aqueduct.io/) and [Shelf](https://pub.dev/packages/shelf), both of them weren't looking actively developing and supported and that leads me to the idea to make my own small micro-framework like [Echo](https://echo.labstack.com/) for Golang or [Bottle](https://bottlepy.org/) for Python. And it was easy to decide: I've had time and motivation :)

And before we get started, I'd like to answer some questions that will definitely come up: why do we need one more framework, and what's the problem your framework resolves? The first one is easy: ask JavaScript developers! But OK, the real answer is competition: the more frameworks we have, the more we learn from each other and, eventually, we build better apps. The second answer: it was designed and built for micro-services and small applications. Sure, there is a way to extend it with middleware and extensions that come later, but as I said I treat it as a Dart alternative to Bottle or Echo. And when I say built I mean will be built 😄

## Architecture

Laska - that's how the project is named - is a micro-framework, it's not aimed to be a full-featured app like Django. Therefore the architecture is quite simple: every request is processed by the same process while performance is based on leveraging the async nature of Dart.

Let's briefly review how it works.

![Async server diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/21sorv8stnckxhl0yb21.png)

As we can see on the diagram, asynchronous server processing works like a charm :) 

I did experiment with Isolates and multiprocessing, but finally, I decided it would require the developers to work with additional limitations. It is not the way I want to use any kind of software, so why does anyone should do it? Programming should be easy and fun therefore there is no kind of parallelism in Laska.

## Let's build something

There is a trivial way to present programming languages and frameworks called 'Hello world'. Let's be trivial!

```dart
import 'package:laska/laska.dart';

void main() async {
  final laska = Laska();

  laska.GET('/hello/:name',
      (context) async => await context.Text("Hello ${context.param('name')}!"));

  await run(laska);
}
```

It's easy to understand what's going on here:
- initialize a Laska object
- add a handler for GET requests at the route '/hello/:name', where `name` is a placeholder for context parameters
- code a handler that returns a string with a parameter given in the path
- and run the server

That's it, an asynchronous server is up and running on http://localhost:3789.


## More realistic example

Surely, 'Hello world' is not a kind of real-world application someone will deploy to production. The more realistic would be to build a server that logs user requests to the stdout and has routes that require authorization.

```dart
import 'package:laska/laska.dart';

// Custom middleware that checks user access.
class Acl implements Middleware {
  final allowedRoles;

  Acl(this.allowedRoles);

  @override
  Future<Function> execute(Function next, Context context) async {
    return (Context c) {
      // In this case it's simple check:
      // Does the request contains `role` header with `admin` value.
      final role = context.request.headers.value('role');

      // If the header's `role` is not in `allowedRoles`, reject the request.
      if (!allowedRoles.contains(role)) {
        context.Text('Role $role is not allowed.');
        return null;
      }
      print('Role $role is allowed.');

      // Don't forget to call the handler.
      return next(c);
    };
  }
}

// Custom middleware that prints request path and given prefix.
class Logger implements Middleware {
  String prefix;

  Logger(this.prefix);

  @override
  Future<Function> execute(Function next, Context context) async {
    return (Context c) {
      print('$prefix: Path: ${context.path}');

      // Don't forget to call the handler.
      return next(c);
    };
  }
}

void main() async {
  final laska = Laska();

  final acl_middleware = Acl(['admin']);

  // Add global middleware
  laska.Use(Logger('global'));

  // Create handler with per-route middlewares: logger and acl
  laska.GET('/secret', secretHandler,
      middlewares: {Logger('route'), acl_middleware});

  // Add route handler, only global middleware will apply
  laska.GET('/users', getUsers);

  // Add route with acl middleware, but only for the POST method.
  laska.POST('/users', getUsers, middlewares: {acl_middleware});

  await run(laska);
}

void secretHandler(Context context) async {
  await context.Text('You have access to secret path!');
}

void getUsers(Context context) async {
  await context.JSON([
    {'id': 1, 'name': 'Make something useful', 'status': 0},
    {'id': 2, 'name': 'Make new website', 'status': 1},
  ]);
}
```

I hope this code is self-explanatory but you can ask me  any questions  in comments :)

## Roadmap

🅿️ - P is for planned.

- ✅ Dynamic routing with placeholders
- ✅ Middlewares
- ✅ JSON handling
- ✅ Form handling
- ✅ Null-Safety
- 🅿️ Logging
- 🅿️ Serve static files
- 🅿️ Template rendering?
- 🅿️ Extensions
- 🅿️ Health checks
- ❓ What's more?

## The end

I'd love to see more server-side Dart projects and hope you do too. If you think so it would be very encouraging if you star the project on [GitHub](https://github.com/TenderOwl/laska) and like it on Dev.to.