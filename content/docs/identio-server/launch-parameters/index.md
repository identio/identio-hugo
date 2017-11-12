---
date: "2016-06-16T15:01:31+02:00"
title: "Launch parameters"
type: "doc-identio-server"

menu:
  identioServer:
    name: "Launch parameters"
    url: "/docs/identio-server/launch-parameters/"
    weight: -30
---

# Launch parameters

This page will guide you through the different launch parameters of the server.

## Specifying launch parameters

The launch parameters described in the following sections can only be provided
as launch parameters of the server or as system environment variables.
They can't be stored in a remote backend.

Example usage of the `identio.config` parameter on UNIX systems:
```sh
bin/identio-server --identio.config=config
```
or, alternatively:
```
export identio.config=config
bin/identio-server
```

Same example on Windows systems:
```sh
bin/identio-server.bat --identio.config=config
```
or, alternatively:
```
set identio.config=config
bin/identio-server
```

## Defining a work directory

Ident.io server generates temporary files on startup. By default, those files are written in
the `config/work` directory.

If you want to change this default, you can specify it with the following parameter:

* `identio.work.directory`: Path to the work directory.

## Configuration

### Choose a configuration backend

Ident.io supports the following configuration backends out of the box:

* Local filesystem
* Git repository
* Hashicorp Vault
* Spring Cloud Config Server
* System environment variables (useful in a container deployment)

You can store your configuration in one of those backends or use a combination.
For example, in a typical cloud deployment, the main part of the configuration may
be stored in a Git and all configuration passwords may be in a Vault Server or in
system environment variables.

### Configuration from the local filesystem

The local filesystem configuration backend is based on a YAML file, which must
be named `identio.yml`.

The configuration is specified through the following launch parameters:

* `identio.config`: Path to the directory hosting the `identio.yml` file.

### Configuration from a Git repository

The Git configuration backend is based on a YAML file, which must
be named `identio.yml` and stored at the root of a git repository.

The configuration is specified through the following launch parameter:

* `identio.config.git.uri`: Url of the Git repository. It can be a HTTPS, Git or SSH URL.
* `identio.config.git.username`: Username to use to connect to the Git repository, if authentication
is required.
* `identio.config.git.password`: Corresponding password.

{{< callout title="Note" type="info" >}}
The repository will be cloned in the work directory on startup.
{{< /callout >}}

### Configuration from a Vault repository

The Vault configuration backend is based on a repository stored in Vault.
Parameters must be prefixed in Vault by `identio/`.

{{< callout title="Note" type="info" >}}
Ident.io Server only supports the APPROLE authentication scheme.
{{< /callout >}}

The configuration is specified through the following launch parameters:

* `identio.config.vault.uri`: Url of the Vault repository. (e.g.: https://vaultserver:8200)
* `identio.config.vault.role-id`: Role identifier of the service in vault.
* `identio.config.vault.secret-id`: Secret identifier of the application.
* `identio.config.vault.trust.path`: Path to the certificate authority used to validate
connection to vault through SSL.

### Configuration from a Spring Cloud Config server

The Spring Cloud Config server configuration backend is based on a repository stored
in Spring Cloud Config server.

The configuration is specified through the following launch parameters:

* `identio.config.server.uri`: Url of the Vault repository.
* `identio.config.server.username`: Username to use to connect to the Config server, if authentication
is required.
* `identio.config.server.password`: Corresponding password.
