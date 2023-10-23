import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Add SAML SSO to Next.js App

This guide assumes that you have a Next.js app and want to enable SAML Single Sign-On authentication for your enterprise customers. By the end of this guide, you'll have an app that allows you to authenticate the users using SAML Single Sign-On.

Visit the [GitHub repository](https://github.com/boxyhq/saas-starter-kit) to see the source code for the Next.js SAML SSO integration.

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

Setup the SAML Jackson to work with Next.js app.

`samlPath` is where the identity provider POST the SAML response after authenticating the user.

We'll use [NextAuth.js](https://next-auth.js.org/) for the authentication. NextAuth is a complete open-source authentication solution for Next.js applications.

Let's add a custom provider called `saml-jackson` to the NextAuth.

### Make Authentication Request

Let's add a route to begin the authenticate flow; this route initiates the SAML SSO flow by redirecting the users to their configured Identity Provider.

### Receives SAML Response

After successful authentication, Identity Provider POST the SAML response to the Assertion Consumer Service (ACS) URL.

Let's add a route to handle the SAML response. Ensure the route matches the value of the `samlPath` you configured while initializing the SAML Jackson library and should be able to receives POST request.

### Request Access Token

Let's add another route for receiving the callback after the authentication.

The NextAuth requests an `access_token` by passing the authorization `code` along with authentication details, including the `grant_type`, `redirect_uri`, and `code_verifier`.

### Fetch User Profile

Once the `access_token` has been fetched, NextAuth can use it to retrieve the user profile from the Identity Provider. The `userInfo` method returns a response containing the user profile if the authorization is valid.

The entire response will look something like this:

### Authenticate User

Once the user has been retrieved from the Identity Provider, you may determine if the user exists in your application and authenticate the user. If the user does not exist in your application, you will typically create a new record in your database to represent the user.

### Starts OAuth sign-in flow

You can use NextAuth's `signIn` method to initiate the authentication with `saml-jackson` provider.

You can pass the `tenant` and `product` as additional parameters to the `/api/auth/saml/authorize` endpoint through the third argument of `signIn()`.