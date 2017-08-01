# `<oidc-login>`

An OpenID Connect client-side login web component built with and for Polymer 2

## Install

```
bower install --save oidc-login
```

## Usage

1. Create a credential set with your chosen Open ID Connect provider (or providers),
  and record the "Cliend ID".
2. Copy the provided `login-callback.html` to the root of your web server (to the
  same directory as your primary `index.html` file), or another well known location.
  If you're using a non-default path, you'd need to set the `redirect-uri` property of `<oidc-login>`
3. Use `<oidc-login>` to wrap the content that you want to be visible only to authenticated users.

### Sample Implementation:

```
<link rel="import" href="../bower_components/oidc-login/oidc-login.html">

<dom-module id="my-app">
<template>
	<style>
	:host {
		display: block;
	}
	</style>

	<oidc-login access-token="{{accessToken}}" id="oidc">
		<oidc-login-provider name="google" discovery="https://accounts.google.com"
			client-id="client-id-from-google"></oidc-login-provider>

		<paper-card>
		<h2>Hello World!</h2>
		<p>Access Token: [[accessToken]]</p>
		<paper-button on-click="doLogout">Logout</paper-button>
		</paper-card>

	</oidc-login>
</template>

<script>
class MyApp extends Polymer.Element {
	static get is() { return 'my-app'; }
	
	static get properties() {
		return {
			accessToken: { type: String }
		};
	}
	
	doLogout() {
		this.$.oidc.logout();
	}
}
window.customElements.define(KappasApp.is, KappasApp);

</script>
</dom-module>
```

With such a setup, if the user is not logged in (i.e. the `access-token` is empty),
then `<oidc-login>` will hide the content of the element and instead display a set
of login buttons (one for each configured provider) allowing the user to start the
authentication process.

Once the user completes the authentication process, the OpenID Connect provider will
redirect the browser to the `login-callback.html` page - which will record the
user's access token and redirect the user's browser back to the application.

Once the user is back in the application: the access token will be loaded and made
available through the `access-token` attribute of `<oidc-login>`; the
element's content will be displayed, and an `oidc-login-success` event will be sent.

### Supported Properties

 * `access-token` : This read-only property returns the OpenID Connect provider's access token after a successful authentication.
 * `provider` : This read-only property returns the OpenID Connect providers name as specified in the `<oidc-login-provider>` element.
 * `scope` : The OAuth 2.0 scope to retrieve from the server. Default: `openid profile email`.
 * `redirect-uri` : The stand-alone page in the application that will read the OpenID
   access token from all providers.
   Default: `/login-callback.html`.

### Supported `<oidc-login-provider>` Properties

 * `name` : The OpenID Connect provider name. This property is used to select the provider to authenticate with using the `login()` API,
   and is also the name reported back in the `provider` property. Setting this property is only needed if there are more than one provider.
 * `discovery` : The OpenID Connect discovery URL for the provider. 
 * `client-id` : The OpenID Connect client token received from the provider.
 * `redirect-uri` : The stand-alone page in the application that will read the OpenID 
   Connect access token from this provider.
   Default: the global `redirect-uri` setting.

## Advanced Topics

### Custom Login Screen

The login screen (where the "login with..." buttons live) can be customized by adding
a slot implementation named "login". With the additional slot, the application needs
to manage the login flow and call the `oidc-login` API to start the login process.

#### Example:

```
<dom-module id="my-app">
<template>
	<style>
	:host {
		display: block;
	}
	</style>

	<oidc-login access-token="{{accessToken}}" id="oidc">
		<oidc-login-provider name="google" discovery="https://accounts.google.com"
			client-id="client-id-from-google"></oidc-login-provider>

		<paper-card slot="login">
			<paper-button on-click="doLogin">Login With G+</paper-button>
		</paper-card>

		<paper-card>
		<h2>Hello World!</h2>
		<p>Access Token: [[accessToken]]</p>
		<paper-button on-click="doLogout">Logout</paper-button>
		</paper-card>

	</oidc-login>
</template>

<script>
class MyApp extends Polymer.Element {
	static get is() { return 'my-app'; }
	
	static get properties() {
		return {
			accessToken: { type: String }
		};
	}
	
	doLogin() {
		this.$.oidc.login('google');
	}
	
	doLogout() {
		this.$.oidc.logout();
	}
}
window.customElements.define(KappasApp.is, KappasApp);

</script>
</dom-module>
```