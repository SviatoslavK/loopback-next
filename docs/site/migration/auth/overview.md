---
lang: en
title: 'Migrating authentication and authorization'
keywords: LoopBack 4.0, LoopBack 4, LoopBack 3, Migration
sidebar: lb4_sidebar
permalink: /doc/en/lb4/migration-auth-overview.html
---

## LoopBack 3 authentication and authorization facilities

LoopBack version 3 provides several options for adding authentication and
authorization to secure the applications. See
[docs](https://loopback.io/doc/en/lb3/Authentication-authorization-and-permissions.html)
for more details.

### Authentication

1. Built-in `User` and `AccessToken` based authentication

LoopBack 3 includes a built-in `User` model with `login` and other methods to
perform username/password based authentication and return an access token, which
can then be used to access protected resources.

2. Integration with [Passport](http://www.passportjs.org/)

[loopback-component-passport](https://github.com/strongloop/loopback-component-passport)
provides integration between LoopBack 3 and
[Passport](http://www.passportjs.org) to support third-party login and account
linking for LoopBack applications. The migration path is described in
[Migrating Passport-based authentication](./passport.md).

3. oAuth 2.0

[loopback-component-oauth2](https://github.com/strongloop/loopback-component-oauth2)
provides full integration between OAuth 2.0 and LoopBack. It enables LoopBack
applications to function as an oAuth 2.0 provider to authenticate and authorize
client applications and/or resource owners (i.e. users) to access protected API
endpoints. The migration path is described in
[Migrating OAuth2 provider](./oauth2.md).

### Authorization

- A set of built-in models like `User`, `AccessToken` and `ACL` makes it easy to
  store your user credentials locally and define custom access control checks.
  The migration path is described in
  [Migrating built-in authentication and authorization](./built-in.md).

- Built-in ACL based authorization

## LoopBack 4 authentication and authorization facilities

LoopBack 4 focuses on capturing the minimum common metadata for authentication
and authorization and enabling extensibility so that different security
strategies/schemes can be plugged in to enforce authentication and
authorization.

### Authentication

- AuthenticationStrategy
- PassportAdapter

### Authorization

- Authorizer
- Use your own interceptor for authorization
