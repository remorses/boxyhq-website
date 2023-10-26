import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Add SAML SSO to React App

This guide assumes that you have a React app and want to enable SAML Single Sign-On authentication for your enterprise customers. By the end of this guide, you'll have an app that allows you to authenticate the users using SAML Single Sign-On.

If you wish to dive straight into the source, Checkout: <https://github.com/boxyhq/jackson-examples/tree/main/apps/react>

:::info
We use an express backend with the React Single Page App. The code for this can be found at <https://github.com/boxyhq/jackson-examples/tree/main/apps/express-api>
:::

Integrating SAML SSO into an app involves the following steps.

- Configure SAML Single Sign-On
- Authenticate with SAML Single Sign-On

## Configure Enterprise SSO on React

This step allows your tenants to configure SAML connections for their users. Read the following guides to understand more about this.

- [UI Best Practices for Configuring SAML Single Sign-On](/guides/jackson/configuring-saml-sso)
- [SSO Connection API](/docs/jackson/sso-flow/)

## Authenticate with SAML Single Sign-On

Once you add a SAML connection, the app can use this SAML connection to initiate the SSO authentication flow using SAML Jackson. The following sections focus more on the SSO authentication side.

### Deploy SAML Jackson

The first step is to deploy the SAML Jackson service. Follow the [deployment docs](/docs/jackson/deploy/service) to install and configure the SAML Jackson.

### Setup SAML Jackson Integration

We'll use the client library `@bity/oauth2-auth-code-pkce` to implement the authentication process. It is a zero-dependency OAuth 2.0 client implementing the authorization code grant with PKCE for client-side protection.

Let's configure the `OAuth2AuthCodePKCE` client to use the SAML Jackson service for authentication. Here we use a custom hook so that the `oauthClient` can be used elsewhere in the app.

### Setup global Authentication primitives

#### AuthContext

We need a way to make the logged-in `user` as well as the `signIn`, `signOut` methods accessible globally. These, along with the `setTenant` (method used to select the tenant for the SSO flow) and `authStatus` (boolean which helps us to conditionally render content based on whether the authenticated status is fully known or being loaded) are made available throughout the application by using `AuthContext`.

We also create a custom hook that returns a handle to the `AuthContext`.

#### AuthProvider

We will wire up the flow inside the AuthProvider.

1. Once the app shell is rendered, we run an effect that uses the `authClient` from `useOAuthClient` to conduct the flow. Two scenarios need to be handled here. The first one is the case where we have secured an access_token from the SSO provider (Jackson) in which case we can retrieve the logged-in user profile by passing in the cookie. The second one is the case where the browser gets redirected back to the app, after signing in at IdP. The authorization code in the redirect is exchanged for an access token which is then passed to the app backend to complete the login.

2. When someone tries to access protected/private routes they will be redirected to the login page. Before we do this we save the current location they were trying to access in the history state. This logic is encapsulated in the `RequireAuth` wrapper component. Use it to protect routes that require authentication.

   We then use the `from` state in the `redirectUrl` to construct the `oAuthClient` inside `AuthProvider`.

3. `signIn` and `signOut` methods can be implemented as follows:

### Make Authentication Request

Let's add a page to begin the authenticate flow. This page initiates (by calling `signIn` from the `AuthContext`) the SAML SSO flow by redirecting the users to their configured Identity Provider (via Jackson).

The user will be redirected to the IdP when clicking the "Continue with SAML SSO" button.

### Fetch User Profile

Once the `accessToken` has been what da fuq, the React app can use it to retrieve the user profile from the Identity Provider.

Typically you would use your backend service (Eg: Express.js) to call the SAML Jackson API to fetch the user profile using the `accessToken`.

Here are the express.js routes that return the user profile either on login or by parsing the JWT from the client-side cookie.

The profile will look something like this:

In the React app, we call the `getProfileByJWT` if an access_token is already in possession or we call the `authenticate` when returning back from SSO provider with the authorization code.

## Ready to go

That's it, your react app is ready for Single Sign-On. 🎉

## Next steps

- Got a question? [Ask here](https://discord.gg/uyb7pYt4Pa)