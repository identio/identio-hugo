+++
date = "2016-06-16T15:01:31+02:00"
title = "Getting started"
type = "doc-identio-server"
weight = "10"

[menu.identioServer]
	name   = "Getting started"
	url    = "/docs/identio-server/getting-started/"
+++

# Getting started

This page will guide you through the installation process of a simple Identity Provider.

{{< callout title="Warning" type="warning" >}}
Be aware that this setup is useful in a development or demo environment, but should not be used as-is
in production.
{{< /callout >}}

## Pre-requisite

A Java 1.8+ Runtime Environment must be installed on your machine.

Ensure that the time of your machine is synchronized through NTP.

## Installation

### Ident.io Server installation

You just need to download the [latest version](https://github.com/identio/identio-server/releases)
and unzip the content on the filesystem.

### Prepare the environment

Edit the configuration file `identio-config.yml` that can be found in the `config` directory and set
the public hostname of your machine:
```yaml
globalConfiguration:
  publicFqdn: http://<your_hostname>:10080
```

### Start the server

On UNIX systems:
```sh
bin/identio-server --identio.config=config/identio-config.yml
```

On Windows systems:
```sh
bin/identio-server.bat --identio.config=config\identio-config.yml
```

This will start a HTTP server listening on port 10080.

### Fetch the IDP metadata

The metadata of the Identity Provider can be fetched by accessing the URL `http://<your_hostname>:10080/SAML2`

### Copy the Service Provider Metadata

To configure a Service Provider, just copy the metadata of your Service Provider in the `config/trusted-sp` folder.
Ident.io server periodically scans for new metadatas in this directory and will automatically load the file.

### Test the setup

Access your Service Provider and you should be redirected to Ident.io server.
The sample configuration uses a local authentication file containing one test user.
The login and password are `johndoe` / `password`.
