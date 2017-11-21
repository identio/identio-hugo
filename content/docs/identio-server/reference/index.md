---
date: "2016-06-16T15:01:31+02:00"
title: "Configuration reference"
type: "doc-identio-server"

menu:
  identioServer:
    name: "Configuration reference"
    url: "/docs/identio-server/reference/"
    weight: -20
---

# Reference documentation

This page details the configuration options of Ident.io.
The configuration is by default located in the `identio.yml` file.

Each configuration option can also be provided as a system environment variable.
For example, the system variable `global.port` will set the TCP port to open.

{{< callout title="Warning" type="warning" >}}
As the configuration is a YAML file, be aware that the indentation of each item is important.
{{< /callout >}}

The configuration is split in 5 parts:

* **Global**: technical configuration of the server (ports, url, etc.)
* **Authentication policy**: configuration of the authentication policy to apply to authentication requests
* **Authorization**: configuration of the authorization policy.
* **SAML Identity Provider**: configuration of the SAML Identity Provider
* **OAuth server**: configuration of the OAuth authorization server
* **Session**: configuration of the session storage
* **Authentication methods**: configuration of the authentication methods that an user can use to authenticate
* **Data sources**: configuration of the different data-sources used by modules of the application

## Global configuration

The global configuration is defined in the `global` section.

### Public URL and port

* `port`: TCP port to open (default: `10080` for HTTP, `10443` for HTTPS).
* `basePublicUrl`: Public fully qualified domain name of the server. This is the url that end-users
will use to access the server.

### SSL configuration

As a pre-requisite to enable SSL, you have to generate a valid certificate in PKCS12 format.

* `secure`: Set to `true` to enable SSL on the server (default: `false`).
* `sslKeystorePath`: Path to the PKCS12 file containing the SSL certificate for the server
(default: `config/ssl-certificate.p12`).
* `sslKeystorePassword`: Password of the PKCS12 file (default: `password`).

### Digital signature configuration

Those parameters configure the keystore used in digital signatures of SAML or JWT tokens.

* `signatureKeystorePath`: Path to the PKCS12 file containing the signing certificate for the server
(default: `config/default-sign-certificate.p12`).
* `signatureKeystorePassword`: Password of the PKCS12 file (default: `password`).

### Miscellaneous

* `staticResourcesPath`: Path to the static resources hosting the UI of Ident.io server. This option is useful
if you want to customize the UI of the server (default: `ui` directory)

### Sample configuration

Here is a sample configuration for this section:

```yaml
global:
  sslKeystorePath: /opt/identio/config/idp.identio.net.p12
  sslKeystorePassword: password
  signatureKeystorePath: /opt/identio/config/sign-certificate.p12
  signatureKeystorePassword: password  
  port: 443
  basePublicUrl: https://idp.identio.net
  secure: true
  staticResourcesPath: /opt/identio/ui
```

## Authentication policy configuration

The authentication policy is defined in the `authPolicy` section.

### Authentication levels configuration

The authentication levels are defined in the `authLevels` property.

This is a list of the authentication levels you want to handle, ordered from the weakest to the strongest.

Each authentication levels has two properties:

* `name`: The internal name of the authentication level.
* `urn`: A URN referencing this authentication level.

### Default authentication level configuration

The default authentication level is defined in the `defaultAuthLevel` property.
This authentication level is used by the authentication server when no information in the request
is usable to determine which authentication level to apply.

The default authentication level has two properties:

* `authLevel`: The name of the authentication level declared in the previous section.
* `comparison`: Used by the authentication server to determine which authentication methods
to propose to the user. The authentication server compares the default authentication level and the different declared
authentication method and chose the correct one based on the value on this parameter.
This parameter has 4 possible valid values:
	* `exact`: Authentication methods that have an authentication level equal to the default authentication level.
	* `minimum`: Authentication methods that have an authentication level equal or better than the default authentication level.
	* `better`: Authentication methods that have an authentication level better than the default authentication level.
	* `maximum`:  Authentication methods that have an authentication level equal or lower than the default authentication level.

### Configure a specific authentication level for an application

You can configure a specific authentication level for an application that will override both the authentication level requested by the application (if any) or the default authentication level.

This behavior is configured through the ´applicationSpecificAuthLevel´ parameter. This parameter is a list.

An application specific authentication level has 3 properties:

* `appName`: The unique identifier of the application. For a SAML 2.0 Service Provider, this is the entity ID.
* `authLevel`
* `comparison`

The meaning of `authLevel` and `comparison` is the same as in the previous section.

### Sample configuration

Here is a sample configuration for this section:

```yaml
authPolicy:
  authLevels:
    - name: low
      urn: urn:identio:auth-level:low
    - name: medium
      urn: urn:identio:auth-level:medium
    - name: strong
      urn: urn:identio:auth-level:strong
  defaultAuthLevel:
    authLevel: medium
    comparison: minimum
  applicationSpecificAuthLevel:
    - appName: http://sp1.identio.net:8080/sp/SAML2
      authLevel: medium
      comparison: minimum
```

## Authorization configuration

The authorization policy is configured in the `authorization` section.

In this section, you can define the authorization scopes that are managed by the authorization server.

Each scope is declared in the `scopes` section and contains the following informations:

* `name`: Name of the scope
* `authLevel`: Name of the authentication level of the end-user that is necessary to get this scope
* `expirationTime`: Maximum time to live in seconds of an access token containing this scope
* `description`: A list of descriptions of the scope for end-users. Each item of the list is the description in a specific locale.

### Sample configuration

Here is a sample configuration for this section:

```yaml
authorization:
  scopes:
    - name: scope.test.1
      authLevel: medium
      expirationTime: 2400
      description:
        fr: Accéder à scope test 1
        en: Access scope test 1
    - name: scope.test.2
      authLevel: strong
      expirationTime: 3600
```

## SAML Identity Provider configuration

The SAML Identity provider is configured in the `samlIdp` section.

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
samlIdp:
  allowUnsecureRequests: true
  allowedTimeOffset: 1
  certificateCheckEnabled: true
  contactPersonEmail: support@identio.net
  contactPersonSurname: Ident.io Support
  organizationDisplayName: Ident.io IDP
  organizationName: Ident.io IDP
  organizationUrl: http://identio.net
  spMetadataDirectory: /opt/identio/config/sp/
  tokenValidityLength: 3
```

## OAuth Server configuration

The OAuth server is configured in the `oAuthServer` section.

The following parameters can be set:

* `actorsFile`: Path to the file defining clients and resource servers (default: `config/oauth-clients.yml`).
* `dataSource`: Name of the datasource where tokens and codes are stored. If omitted,
everything is stored in memory. Only JDBC data sources are supported for now.
* `jwtToken`: Set to `true` to generate access token in JWT format (default: `false`)

### Sample configuration

```yaml
oAuthServer:
  clientFile: /opt/identio/config/oauth-clients.yml
  dataSource: jdbc-database
  jwtToken: true
```

### Actors file configuration

The actors file contains two sections. One for the clients and one for the resource servers.

The clients are declared in the `clients` section.

The following properties can be set for each client:

* `name`: Name of the client
* `clientId`: Client Id of the client
* `clientSecret`: Client secret of the client
* `allowedScopes`: List of all the scope names that the client is allowed to request.
* `responseUri`: List of valid callback urls for sending responses
* `allowedGrants`: List of grants that the client is allowed to use. The server allows this grants:
    * `token`: Implicit grant
    * `authorization_code`: Authorization code grant
    * `refresh_token`: Refresh token grant
    * `client_credentials`: Client credentials grant
    * `password`: Resource owner credentials grant. The `resourceOwnerAuthMethod` configuration property must be set when
    enabling this grant.
* `consentNeeded`: Indicates if an user consent is needed for each scope requested by this client
* `resourceOwnerAuthMethod`: Name of the authentication method to use when the resource owner credentials flow is invoked by this client.


The resource servers are declared in the `resourceServers` section.

The following properties can be set for each resource server:

* `name`: Name of the client
* `clientId`: Client Id of the client
* `clientSecret`: Client secret of the client. It can be either in plaintext or hashed via the Bcrypt algorithm
(for more information, see [Local Authentication - Adding users]({{< relref "#adding-users" >}}) ) .

A sample actors file may look like this:

```yaml
clients:
  - name: Test Client
    clientId: test
    clientSecret: "{plain}test"
    allowedScopes:
      - scope.test.1
      - scope.test.2
    responseUri:
      - http://example.com/cb
    allowedGrants:
      - token
    consentNeeded: true
  - name: Test Client 2
    clientId: test2
    clientSecret: "{plain}test2"
    allowedScopes:
      - scope.test.1
      - scope.test.2
    responseUri:
      - http://example.com/cb
    allowedGrants:
      - token
      - authorization_code
      - refresh_token
      - client_credentials
      - password
    consentNeeded: true
    resourceOwnerAuthMethod: Local

resourceServers:
  - name: Test API
    clientId: rs1
    clientSecret: "{plain}rs1"
```

## Session configuration

The user session is configured in the `session` section.

The session is stored in memory.

This section is composed of 1 parameter:

* `duration`: Period in minutes of user inactivity after which the session will be destroyed.

### Sample configuration

Here is a sample configuration for this section:

```yaml
session:
  duration: 120
```

## Authentication methods configuration

The different authentication methods are configured in the `authMethods` section.

Ident.io server currently supports 5 authentication methods types out-of-the-box:

* **Local**: Login and passwords are stored in a YAML file. Password can be stored hashed (Bcrypt hash) or in plain text.
* **LDAP**: Login and password authentication on a LDAP directory.
* **Radius**: Login and password stored on a Radius Server. This method also supports RSA SecurID® specific "New PIN mode" and "Next token mode" challenges.
* **SAML**: Authentication delegated to another SAML 2.0 compliant Identity Provider. Ident.io server acts as a SAML 2.0 IDP Proxy. This method also offers a flexible way to map authentication levels between the remote Identity Provider and Ident.io.
* **X.509 Certificates**: Authentication based on X.509 certificates. This method offers a powerful mean to analyse attributes

### Local authentication

The Local authentication methods are configured in the `local` section.

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: The name of an authentication level declared in a previous section.
* `userFilePath`: Path to the YAML file containing the users definitions (default: `config/users.yml`)

#### Adding users

You can add arbitrary users in the user file. All users should be added in the `users` list, with the following attributes:

* `userId`: Identifier of the user
* `password`: Password of the user. It can be either in plaintext or hashed via the Bcrypt algorithm.

Plain-text passwords are prefixed by the `{plain}` keyword, Bcrypt passwords by `{bcrypt}`.

{{< callout title="Warning" type="warning" >}}
Don't forget to put the password between quotes, as the '{' character would be interpreted by the configuration parser.
{{< /callout >}}

You can use the `password-generator` utility in the `bin` directory to generate a BCrypt hashed password.

On UNIX systems:
```sh
$ bin/password-generator
```

On Windows systems:
```sh
$ bin\password-generator.bat
```

#### Sample configuration

Here is a sample configuration for this section:

```yaml
localAuthMethods:
  - name: Local
    authLevel: medium
    userFilePath: /opt/identio/config/users.yml
```

### LDAP authentication

The LDAP authentication methods are configured in the `ldap` section.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: The name of an authentication level declared in a previous section.
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
ldap:
- name: Corporate LDAP
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

The Radius authentication methods are configured in the `radius` section.
This authentication method supports the specific challenges sent by the RSA SecurID®
Radius server.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: The name of an authentication level declared in a previous section.
* `authPort`: Port of the Radius authentication service.
* `accountPort`: Port of the Radius accounting service.
* `radiusHost`: A list containing the radius server hostnames. The hosts will
be tried successively in case of a timeout.
* `sharedSecret`: The Radius shared secret for Ident.io.
* `timeout`: Time in milliseconds to wait for a response of the Radius server.

#### Sample configuration

Here is a sample configuration for this section:

```yaml
radius:
  - name: SecurID
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

The SAML authentication methods are configured in the `saml` section.
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
  strong: urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract
  medium: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
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
  urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract: strong
  urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport: medium
```

This configuration means that a response sporting `urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract`
authentication context will be map to a `strong` authentication level.
Similarly, a response sporting `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`
authentication context will be map to a `medium` authentication level.

#### Sample configuration

Here is a sample configuration for this section:

```yaml
samlAuthMethods:
  - name: Remote Identity Provider
    certificateCheckEnabled: false
    logoFileName: /opt/identio/config/trusted-idp/remote-idp.jpg
    metadata: /opt/identio/config/trusted-idp/remote-idp.xml
    samlAuthMap:
      in:
        urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract: strong
        urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport: medium
      out:
        strong: urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract
        very-weak: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
        medium: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
        weak: urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
```

### X.509 Certificate authentication

The X.509 authentication methods are configured in the `x509` section.
This section is a list, you can include multiple authentication methods
(for example, one for software certificates and one for certificates embedded in a smartcard
or if you want to handle several client certificate authorities).
This authentication method is transparent for the end-user, meaning that it will
be checked before displaying the login screen.

#### Common configuration options

The following options can be set:

* `name`: Display name of the authentication method.
* `authLevel`: The name of an authentication level declared in a previous section.
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
x509:
- name: Soft Certificate
  authLevel: medium
  security: native
  clientCertTrust: /opt/identio/config/client-ca.crt
  conditionExpression: !getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')
  uidExpression: getSubjectAlternativeNames()[2][1]
```

The same configuration, but with an Apache reverse proxy handling the SSL endpoint, configured with a shared secret:

```yaml
x509:
- name: Soft Certificate
  authLevel: medium
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
x509:
- name: Soft Certificate
  authLevel: medium
  security: ssl
  apacheFix: true
  proxyCertTrust: /opt/identio/config/proxy-ca.crt
  proxyCertDn: CN=myproxy,OU=myentity,O=mycompany
  certHeaderName: User-Cert
  clientCertTrust: /opt/identio/config/client-ca.crt
  conditionExpression: !getExtendedKeyUsage().contains('1.3.6.1.4.1.311.20.2.2') and getExtendedKeyUsage().contains('1.3.6.1.5.5.7.3.2')
  uidExpression: getSubjectAlternativeNames()[2][1]
```

## Data sources configuration

Data sources of the application are defined in the `data` section.

The `datasources` sub-section contains a list of all declared data sources in the application.

Each data source has the following properties:

* `name`: Name of the data source
* `type`: Type of the data source. The only valid value for now is `jdbc`.
* `driver`: Name of the driver to use for this data source.
* `url`: Url to connect to the data source.
* `username`: Username of the technical account to connect to the data source.
* `password`: Corresponding password.

### Sample configuration

Here is a sample configuration for this section:

```yaml
data:
  dataSources:
    - name: Local
      type: jdbc
      driver: org.h2.Driver
      url: jdbc:h2:mem:db;DB_CLOSE_DELAY=-1
      username: sa
      password: sa
```
