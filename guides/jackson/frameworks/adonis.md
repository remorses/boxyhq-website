import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Add SAML SSO to AdonisJS App

This guide assumes that you have a AdonisJS app and want to enable SAML Single Sign-On authentication for your enterprise customers. By the end of this guide, you'll have an app that allows you to authenticate the users using SAML Single Sign-On.

Visit the [GitHub repository](https://github.com/boxyhq/jackson-examples/tree/main/apps/adonisjs) to see the source code for the AdonisJS SAML SSO integration.

Integrating SAML SSO into an app involves the following steps.

- Configure SAML Single Sign-On
- Authenticate with SAML Single Sign-On

## Configure SAML Single Sign-On

This step allows your tenants to configure SAML connections for their users. Read the following guides to understand more about this.

- [UI Best Practices for Configuring SAML Single Sign-On](/guides/jackson/configuring-saml-sso)
- [SSO Connection API](/docs/jackson/sso-flow/)

## Authenticate with SAML Single Sign-On

Once you add a SAML connection, the app can use this SAML connection to initiate the SSO authentication flow using SAML Jackson. The following sections focus more on the SSO authentication side.

### Install SAML Jackson

To get started with SAML Jackson, use the Node Package Manager to add the package to your project's dependencies.

### Setup SAML Jackson

Setup the SAML Jackson to work with AdonisJS app.

`samlPath` is where the identity provider POST the SAML response after authenticating the user and `redirectUrl` is where the SAML Jackson redirects the user after authentication.

Create a new custom Provider `JacksonProvider` that relies on the `@boxyhq/saml-jackson`. The `boot` method initializes the SAML Jackson and returns a singleton.

Create a declaration file if you are working with TypeScript.

### Make Authentication Request

Let's add a route to begin the authenticate flow; this route initiates the SAML SSO flow by redirecting the users to their configured Identity Provider.

The `store` method of `LoginController` takes care of redirecting the user to the Identity Provider.

### Receives SAML Response

After successful authentication, Identity Provider POST the SAML response to the Assertion Consumer Service (ACS) URL.

Let's add a route to handle the SAML response. Ensure the route matches the value of the `samlPath` you configured while initializing the SAML Jackson library and should be able to receives POST request.

The `acs` method of `SSOController` takes care of handling the SAML response from the Identity Provider and redirecting the users to the `redirectUrl`.

### Request Access Token

Let's add another route for receiving the callback after the authentication. Ensure the route matches the value of the `redirectUrl` you configured previously.

The application requests an `access_token` by passing the authorization `code` along with authentication details, including the `client_id`, `client_secret`, and `redirect_uri`.

The `callback` method of `SSOController` take care of this.

### Fetch User Profile

Once the `access_token` has been fetched, you can use it to retrieve the user profile from the Identity Provider. The `userInfo` method returns a response containing the user profile if the authorization is valid.

The entire response will look something like this:

### Authenticate User

Once the user has been retrieved from the Identity Provider, you may determine if the user exists in your application and authenticate the user. If the user does not exist in your application, you will typically create a new record in your database to represent the user.