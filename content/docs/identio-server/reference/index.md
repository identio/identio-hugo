+++
date = "2016-06-16T15:01:31+02:00"
title = "Reference documentation"
type = "doc-identio-server"
weight = "20"

[menu.identioServer]
	name   = "Reference documentation"
	url    = "/docs/identio-server/reference/"
+++

# Reference documentation

This page details the configuration options of Ident.io.
The configuration is by default located in the file `identio-config.yml` in the `config`
directory.

{{< callout title="Warning" type="warning" >}}
As the configuration is a YAML file, be aware that the identation of each item is important.
{{< /callout >}}

The configuration is split in 5 parts:

* **Global**: technical configuration of the server (ports, url, etc.)
* **Authentication policy**: configuration of the authentication policy to apply to authentication requests
* **SAML Identity Provider**: configuration of the SAML Identity Provider
* **Session**: configuration of the session storage
* **Authentication methods**: configuration of the authentication methods that an user can use to authenticate

## Global configuration

The global configuration is defined in the `globalConfiguration` section.

### Host and port

* `port`: TCP port to open (default: `10080`).
* `publicFqdn`: Public fully qualified domain name of the server. This is the url that end-users
will use to access the authentication server.
* `workDirectory`: A directory where the SSL Keystore of the embedded application
server will be created (default: the directory hosting the configuration).

### SSL configuration

As a pre-requisite to enable SSL, you have to generate a valid certificate in PKCS12 format.

* `secure`: Set to `true` to enable SSL on the server (default: `false`).
* `keystorePath`: Path to the PKCS12 file containing the SSL certificate for the server.
* `keystorePassword`: Password of the PKCS12 file.

### Miscellaneous

* `staticResourcesPath`: Path to the static resources hosting the UI of Ident.io server. This option is useful
if you want to customize the UI of the server (default: `ui` directory)

### Sample configuration

Here is a sample configuration for this section:

```yaml
globalConfiguration:
  keystorePath: /opt/identio/config/idp.identio.net.p12
  keystorePassword: password
  port: 443
  publicFqdn: https://idp.identio.net
  secure: true
  workDirectory: /opt/identio/config
  staticResourcesPath: /opt/identio/ui
```

## Authentication policy configuration

The authentication policy is defined in the `authPolicyConfiguration` section.

### Authentication levels configuration

The authentication levels are defined in the `authLevels` property.

This is a list of the authentication levels you want to handle, ordered from the weakest to the strongest.
Each item of the list start with an anchor (name prefixed with `&`), in order to be used as a reference
elsewhere in the configuration.

Each authentication levels has two properties:

* `name`: The internal name of the authentication level.
* `urn`: A URN referencing this authentication level.

### Default authentication level configuration

The default authentication level is defined in the `defaultAuthLevel` property.
This authentication level is used by the authentication server when no information in the request
is usable to determine which authentication level to apply.

The default authentication level has two properties:

* `authLevel`: A reference to a authentication level declared in the previous section.
It must start with a `*` character (denoting a reference in YAML)
* `comparison`: Used by the authentication server to determine which authentication methods
to propose to the user. The authentication server compares the default authentication level and the different declared
authentication method and chose the correct one based on the value on this parameter.
This parameter has 4 possible valid values:
	* `exact`: Authentication methods that have an authentication level equal to the default authentication level.
	* `minimum`: Authentication methods that have an authentication level equal or better than the default authentication level.
	* `better`: Authentication methods that have an authentication level better than the default authentication level.
	* `maximum`:  Authentication methods that have an authentication level equal or lower than the default authentication level.

### Configure a specific authentication level for an application

You can configure a specific authentification level for an application that will override both the authentication level requested by the application (if any) or the default authentification level.

This behaviour is configured through the ´applicationSpecificAuthLevel´ parameter. This parameter is a list.

An application specific authentication level has 3 properties:

* `appName`: The unique identifier of the application. For a SAML 2.0 Service Provider, this is the entity ID.
* `authLevel`
* `comparison`

The meaning of `authLevel` and `comparison` is the same as in the previous section.

### Sample configuration

Here is a sample configuration for this section:

```yaml
authPolicyConfiguration:
  authLevels:
    - &low
      name: low
      urn: urn:identio:auth-level:low
    - &medium
      name: medium
      urn: urn:identio:auth-level:medium
    - &strong
      name: strong
      urn: urn:identio:auth-level:strong
  defaultAuthLevel:
    authLevel: *medium
    comparison: minimum
  applicationSpecificAuthLevel:
    - appName: http://sp1.identio.net:8080/sp/SAML2
      authLevel: *medium
      comparison: minimum
```

## SAML Identity Provider configuration

The SAML Identity provider is configured in the `samlIdpConfiguration` section.

### SAML protocol validation

* `allowUnsecureRequests`: Set to `true` to allow unsigned SAML requests (default: `false`).
* `allowedTimeOffset`: Maximum clock skew (in minutes) allowed for a Service Provider registered to this server (default: `0`).
* `tokenValidityLength`: Validity period of a SAML assertion emitted by the Identity Provider (default: `3`).
* `certificateCheckEnabled`: Set to true if you want the Identity Provider to check the validity of the signing certificates (default: `false`).

{{% callout title="About certificate validation" type="info" %}}
Certificate validation is not recommended by OASIS for interoperability reasons.  More on this in the
[SAML V2.0 Metadata Interoperability Profile document](http://docs.oasis-open.org/security/saml/Post2.0/sstc-metadata-iop.pdf).
{{% /callout %}}

### Metadata informations

* `contactPersonEmail`: Email address to get support for this Identity Provider
* `contactPersonSurname`: Name of the contact of this support
* `organizationDisplayName`: Display name of the organization this Identity Provider belongs to.
* `organizationName`: Name of the organization defined previously.
* `organizationUrl`: URL of the website of this organization.

### Miscellaneous

* `keystore`: Path to the PKCS12 keystore containing the signing certificate used to sign the SAML protocol elements. (Default: `idp-default-sign-certificate.p12` in the configuration directory)
* `keystorePassword`: Password of this keystore (default: `password`)
* `spMetadataDirectory`: Path to the directory containing the trusted Service Provider metadatas. Ident.io server scans this directory every 30s for metadatas changes. (Default: `trusted-sp` in the configuration directory)

### Sample configuration

Here is a sample configuration for this section:

```yaml
authPolicyConfiguration:
  authLevels:
    - &low
      name: low
      urn: urn:identio:auth-level:low
    - &medium
      name: medium
      urn: urn:identio:auth-level:medium
    - &strong
      name: strong
      urn: urn:identio:auth-level:strong
  defaultAuthLevel:
    authLevel: *medium
    comparison: minimum
  applicationSpecificAuthLevel:
    - appName: http://sp1.identio.net:8080/sp/SAML2
      authLevel: *medium
      comparison: minimum
```

## Session configuration

The user session is configured in the `samlIdpConfiguration` section.

The session is stored in memory.

This section is composed of 1 parameter (for now):

* `duration`: Period in minutes of user inactivity after which the session will be destroyed.

### Sample configuration

Here is a sample configuration for this section:

```yaml
sessionConfig:
  duration: 120
```

## Authentication methods configuration

The different authentication methods are configured in the `authMethodConfiguration` section.

Ident.io server currently supports 5 authentication methods types out-of-the-box:

* **Local**: Login and passwords are stored in a YAML file. Password can be stored hashed (Bcrypt hash) or in plain text.
* **LDAP**: Login and password authentication on a LDAP directory.
* **Radius**: Login and password stored on a Radius Server. This method also supports RSA SecurID® specific "New PIN mode" and "Next token mode" challenges.
* **SAML**: Authentication delegated to another SAML 2.0 compliant Identity Provider. Ident.io server acts as a SAML 2.0 IDP Proxy. This method also offers a flexible way to map authentication levels between the remote Identity Provider and Ident.io.
* **X.509 Certificates**: Authentication based on X.509 certificates. This method offers a powerful mean to analyse attributes

{{% callout title="Authentication methods naming" type="info" %}}
Each authentication method starts with an anchor (name prefixed with `&`), in order to be used as a reference
elsewhere in the configuration.
{{% /callout %}}

### Local authentication

The Local authentication methods are configured in the `localAuthMethods` section.

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: A reference to a authentication level declared in a previous section.
It must start with a `*` character (denoting a reference in YAML)
* `userFilePath`: Path to the YAML file containing the users definitions (default: `users.yml` in the config directory)

#### Adding users

You can add arbitrary users in the user file. All users should be added in the `users` list, with the following attributes:

* `userId`: Identifier of the user
* `password`: Password of the user. It can be either in plaintext or hashed via the Bcrypt algorithm.

You can use the `password-generator` utility in the `bin` directory to generate a BCrypt hashed password.

On UNIX systems:
```sh
$ bin\password-generator
```

On Windows systems:
```sh
$ bin\password-generator.bat
```

#### Sample configuration

Here is a sample configuration for this section:

```yaml
localAuthMethods:
  - &local
    name: Local
    authLevel: *medium
    userFilePath: /opt/identio/config/users.yml
```

### LDAP authentication

The LDAP authentication methods are configured in the `ldapAuthMethods` section.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: A reference to a authentication level declared in a previous section.
It must start with a `*` character (denoting a reference in YAML)
* `baseDn`: Base DN of the LDAP directory.
* `ldapUrl`: A list containing the LDAP servers URLs. The hosts will
be tried successively in case of a timeout or error.
* `proxyUser`: DN of the proxy user used to search the directory. Don't set this option
for anonymous search.
* `proxyUser`: Password of the proxy user.
* `trustCert`: The path to the Certificate Autority that will be used to validate the certificate
of the LDAP server when accessed through LDAPS protocol.
* `userSearchFilter`: LDAP filter used to search a user in the LDAP directory. A specific place-holder `#UID`
must appear in the filter definition. It will be replaced by the identifier given by the user.

#### Pool configuration

The LDAP authentication method supports connection pooling and validation. This configuration
is done through the `poolConfig` property.

The following options are available:

* `maxIdleConnections`: Maximum number of idle connections to maintain to the directory.
* `minEvictableIdleTime`: Minimum amount of time that a connection may stay idle before being eligible to eviction.
The value `-1` should be used if you don't want to close idle connections.
* `minIdleConnections`: Minimum number of idle connections to maintain to the directory.
* `numTestsPerEvictionRun`: Number of connections tested at each validation run.
* `testOnBorrow`: `true` if the connection should be tested before each authentication request.
* `testRequestFilter`: Search filter to use for testing the connection.
* `testWhileIdle`: `true` if tests should be done when the connection is idle.
* `timeBetweenEvictionRuns`: Time in second between two connection validation runs.

#### Sample configuration

Here is a sample configuration for this section:

```yaml
ldapAuthMethods:
- &corpLDAP
    name: Corporate LDAP
    authLevel: *medium
    baseDn: c=identio
    ldapUrl:
      - ldaps://myldap1:636
      - ldaps://myldap2:636
    proxyUser: cn=proxyUser,ou=applications,c=identio
    proxyPassword: $5hG!s2Am9&#
    trustCert: /opt/identio/config/ldap-trust-ca.crt
    userSearchFilter: (&(objectclass=person)(cn=#UID))
    poolConfig:
      maxIdleConnections: 8
      minEvictableIdleTime: -1
      minIdleConnections: 4
      numTestsPerEvictionRun: 4
      testOnBorrow: true
      testRequestFilter: (objectclass=*)
      testWhileIdle: true
      timeBetweenEvictionRuns: 60
```

### Radius authentication

The Radius authentication methods are configured in the `radiusAuthMethods` section.
This authentication method supports the specific challenges sent by the RSA SecurID®
Radius server.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: A reference to a authentication level declared in a previous section.
It must start with a `*` character (denoting a reference in YAML)
* `authPort`: Port of the Radius authentication service.
* `accountPort`: Port of the Radius accounting service.
* `radiusHost`: A list containing the radius server hostnames. The hosts will
be tried successively in case of a timeout.
* `sharedSecret`: The Radius shared secret for Ident.io.
* `timeout`: Time in milliseconds to wait for a response of the Radius server.

#### Sample configuration

Here is a sample configuration for this section:

```yaml
radiusAuthMethods:
  - &securID
    name: SecurID
    authLevel: *strong
    authPort: 1812
    accountPort: 1813
    radiusHost:
      - host1.myfqn
      - host2.myfqdn
    sharedSecret: $5hG!s2Am9&#
    timeout: 5000
```

### SAML authentication: Identity Provider Proxying

The SAML authentication methods are configured in the `samlAuthMethods` section.
This section is a list, you can include multiple authentication methods, if you want
to delegate authentication to multiple Identity Providers.

#### General configuration

The following options can be set:

* `name`: Display name of the authentication method.
* `certificateCheckEnabled`: Set to true if you want the Identity Provider to check the validity of the signing certificates (default: `false`).
* `metadata`: The path to the metadata of the target Identity Provider.
* `logoFileName`: The path to the logo that will be used to display the target Identity Provider
on the logon page.

#### Authentication levels mapping

The SAML authentication method offers a flexible way to convert Ident.io authentication levels
to authentication contexts that the target Identity Provider is able to understand. In reverse,
Ident.io can also map authentication contexts of the target Identity Provider to internal
authentication levels.

This mapping is declared in the `samlAuthMap` property. Two maps can be declared:

* `out`: Outbound mapping of Ident.io's authentication levels to authentication contexts in the SAML requests sent to this remote Identity Provider.
* `in`: Inbound mapping of the authentication contexts in the SAML responses coming from this remote Identity Provider.

##### Outbound mapping

The `out` section must list all the authentication contexts supported for this Identity Provider.

An example configuration could be:

```yaml
out:
  *strong: urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract
  *medium: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
```

This configuration means that this remote Identity Provider will appear on the logon
script only if the authentication level requested is `medium` or `strong`. If the authentication
level must be *exactly* `low`, it will not appear. If the authentication level must be at *minimum* `low`, it will appear.

If the authentication level is `strong`, the SAML request will contain a requested authentication
context of `urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract`.

{{% callout title="Handling of multiple authentication level" type="info" %}}
If the initial request asks for a `medium` level with a `minimum` comparison, in the example above,
the requests sent to the remote Identity Provider will requests both authentication contexts
`urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract` and
 `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`.
{{% /callout %}}

##### Inbound mapping

The `in` section must list all the authentication contexts that this remote Identity Provider can send.
An unknown authentication context in a response will result in the response being rejected by Ident.io.

An example configuration could be:

```yaml
in:
  urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract: *strong
  urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport: *medium
```

This configuration means that a response sporting `urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract`
authentication context will be map to a `strong` authentication level (note the `*` character denoting a reference
to an internal authentication level).
Similarly, a response sporting `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`
authentication context will be map to a `medium` authentication level.

#### Sample configuration

Here is a sample configuration for this section:

```yaml
samlAuthMethods:
  - &remoteIDP
    name: Remote Identity Provider
    certificateCheckEnabled: false
    logoFileName: /opt/identio/config/trusted-idp/remote-idp.jpg
    metadata: /opt/identio/config/trusted-idp/remote-idp.xml
    samlAuthMap:
      in:
        urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract: *strong
        urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport: *medium
      out:
        *strong: urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract
        *very-weak: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
        *medium: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
        *weak: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
```

### X.509 Certificate authentication

The X.509 authentication methods are configured in the `x509AuthMethods` section.
This section is a list, you can include multiple authentication methods
(for example, one for software certificates and one for certificates embedded in a smartcard
or if you want to handle several client certificate authorities).
This authentication method is transparent for the end-user, meaning that it will
be checked before displaying the login screen.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: A reference to a authentication level declared in a previous section.
It must start with a `*` character (denoting a reference in YAML)
* `clientCertTrust`: The path to the client Certificate Autority that will be used
to validate the end-user certificate.

#### Choose a deployment model

X.509 certificate authentication is often dependent on the deployment architecture
(SSL offloading on a load-balancer, reverse proxying, etc.).
As such, Ident.io offers 3 different configurations to secure the authentication,
defined by the `security` property which takes the following valid values:

* **native**: Ident.io is the SSL end-point and directly handles the authentication.
* **shared-secret**: A reverse-proxy or load-balancer is the SSL endpoint and handles the
authentication. The public user certificate is sent by the reverse proxy in a
header and the communication between the load-balancer and Ident.io is secured by
a shared-secret, also communicated in a header.
* **ssl**: A reverse-proxy or load-balancer is the SSL endpoint and handles the
authentication. The public user certificate is sent by the reverse proxy in a
header and the communication between the load-balancer and Ident.io is secured by
mutual SSL authentication.

##### Native security

In this mode, Ident.io is the SSL end-point and directly handles the authentication.
The server is automatically configured to authenticate clients with the certificate
authority defined by the `clientCertTrust` property.

{{% callout title="Enable SSL" type="info" %}}
This security mode only works when SSL is enabled on Ident.io server (cf. [SSL configuration]({{< relref "#ssl-configuration" >}}))
{{% /callout %}}

##### Shared secret security

In this mode, the reverse proxy in front of Ident.io sends two headers: one containing
the public certificate and one containing a shared secret, unknown to end-users.

Ident.io will do the following:

* The client certificate is extracted from the header specified by the `certHeaderName`
property. If the reverse-proxy is Apache-based, the `apacheFix` property must be set to
true to circumvent a bug in the way Apache encodes client certificates.
* The value of the header defined by the `securityHeaderName` property is check against
the value of the `sharedSecret` property.

##### SSL security

In this mode, the reverse proxy in front of Ident.io sends one header containing
the public certificate and is authenticated through mutual SSL authentication.

Ident.io will do the following:

* Ident.io will only accepts requests that are authenticated through mutual SSL
authentication, based on the certificate authority specified by the `proxyCertTrust`
property. It will check that the Certificate DN and Issuer DN matches the values in the
`proxyCertDn` property and the DN of the `proxyCertTrust` respectively.
* The client certificate is extracted from the header specified by the `certHeaderName`
property. If the reverse-proxy is Apache-based, the `apacheFix` property must be set to
true to circumvent a bug in the way Apache encodes client certificates.

{{% callout title="Enable SSL" type="info" %}}
This security mode only works when SSL is enabled on Ident.io server (cf. [SSL configuration]({{< relref "#ssl-configuration" >}}))
{{% /callout %}}

#### Filtering and extracting user identifier

This authentication method allows to filter the user certificates based on their
attributes.

This filtering is described in the `conditionExpression` property, which is
a [Spring Expression Language](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/expressions.html) string, applied on the certificate.

The possible methods that can be applied to the certificate are described in the
[JDK Javadoc](https://docs.oracle.com/javase/8/docs/api/java/security/cert/X509Certificate.html)

For example, the expression `!getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')` will only accepts certificates that contains the OID *1.3.6.1.5.5.7.3.2* (Client authentication) in the Extended Key Usage but exclude those with the OID *1.3.6.1.4.1.311.20.2.2* (Smartcard logon).

The user identifier is extracted with the same method. The expression should be specified by the `uidExpression` property.

#### Sample configurations

Here is a sample configuration of a native X.509 certificate authentication method:

```yaml
x509AuthMethods:
- &softCert
  name: Soft Certificate
  authLevel: *medium
  security: native
  clientCertTrust: /opt/identio/config/client-ca.crt
  conditionExpression: !getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')
  uidExpression: getSubjectAlternativeNames()[2][1]
```

The same configuration, but with an Apache reverse proxy handling the SSL endpoint, configured with a shared secret:

```yaml
x509AuthMethods:
- &softCert
  name: Soft Certificate
  authLevel: *medium
  security: shared-secret
  apacheFix: true
  certHeaderName: User-Cert
  securityHeaderName: IIO-Shared-Secret
  sharedSecret: F3ds!m5f[mld09hx2?
  clientCertTrust: /opt/identio/config/client-ca.crt
  conditionExpression: !getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')
  uidExpression: getSubjectAlternativeNames()[2][1]
```

Again, the same configuration, but with an Apache reverse proxy handling the SSL endpoint,
authenticated through SSL:

```yaml
x509AuthMethods:
- &softCert
  name: Soft Certificate
  authLevel: *medium
  security: ssl
  apacheFix: true
  proxyCertTrust: /opt/identio/config/proxy-ca.crt
  proxyCertDn: CN=myproxy,OU=myentity,O=mycompany
  certHeaderName: User-Cert
  clientCertTrust: /opt/identio/config/client-ca.crt
  conditionExpression: !getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')
  uidExpression: getSubjectAlternativeNames()[2][1]
```
