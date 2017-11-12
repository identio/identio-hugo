---
date: "2016-06-16T14:03:11+02:00"
title: "Ident.io Server documentation"
type: "doc-identio-server"

menu:
  identioServer:
    name: "Introduction"
    url: "/docs/identio-server/"
    weight: -100
---

# Ident.io documentation

## What is Ident.io Server ?

Ident.io Server is a lightweight authentication, authorization and SSO server.
It provides an implementation of a SAML 2.0 Identity Provider and an OAuth 2.0
authorization server.
It aims to be portable, simple to configure and performant.

## Main Features

### Flexible authentication policy

The aim of Ident.io Server is to decouple applications from authentication methods.
Authentication methods evolve quickly, whereas security needs don't evolve that much.

A complete authentication policy can be implemented depending on your needs, based
on authentication levels.

As an example, if a user accesses an application that requires a strong authentication,
Ident.io Server will only propose authentication methods that satisfies this
authentication level (One-Time Password, Smartcard, etc.).
If the user accesses an application that requires a basic authentication, Ident.io
won't ask the user to authenticate, as it is already strongly authenticated.

### Identity proxying

Ident.io Server is able to delegate authentication to an external SAML 2.0 provider
as part of the user logon flow.
Ident.io Server will enforce the authentication policy on this external providers.

### Flexible architecture

Ident.io Server can be deployed in a variety of architectures depending on your needs,
following a "batteries included but removable" design.

It can be deployed as a standalone in-memory application on a developer workstation
or as a full high-availability solution with load-balancing.

### Cloud Native architecture

Ident.io Server is designed from the ground up to be deployed in a cloud environment.
An official Docker image is provided.

## Support Matrix

### Authentication backends

The following authentication backends are supported:

* LDAPv3 directory
* Radius (including RSA SecurID)
* Local filesystem
* X.509 Certificates

### Social Login

Authentication can be delegated to the following services:

* Any SAML 2.0 compliant authentication server

### Configuration backend

The following configuration backends are supported:

* Local filesystem
* Git repository
* [Hashicorp Vault](https://www.vaultproject.io/) Server for storing secrets
* [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/)
* System environment variables (useful in a container deployment)

### SAML 2.0

The server supports the SAML 2.0 Web Browser SSO Profile of SAML 2.0.

### OAuth 2.0

The following features are supported:

* OAuth 2.0 Core ([RFC 6749](https://tools.ietf.org/html/rfc6749))
* OAuth 2.0 Token Introspection ([RFC 7662](https://tools.ietf.org/html/rfc7662))
* Proof Key for Code Exchange ([RFC7636](https://tools.ietf.org/html/rfc7636))

## Roadmap

The roadmap of the next releases of Ident.io server can be found on [Github](https://github.com/identio/identio-server/milestones).

## Community

If you have an issue, question or an enhancement suggestion, please submit it on the project page on  [Github](https://github.com/identio/identio-server/issues).
