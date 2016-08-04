+++
date = "2016-06-16T14:03:11+02:00"
title = "Ident.io Server documentation"
type = "doc-identio-server"
weight = "0"

[menu.identioServer]
	name   = "Introduction"
	url    = "/docs/identio-server/"
	weight = 0
+++

# Ident.io documentation

## What is Ident.io Server ?

Ident.io Server is a lightweight authentication and SSO server written in Java.
It provides (for now) an implementation of a SAML 2.0 Identity Provider.
It aims to be portable, simple to configure and performant.

## Features

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
