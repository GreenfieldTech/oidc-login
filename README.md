# `<oidc-login>`

An OpenID Connect client-side login web component built with and for Polymer 2

## Install

```
bower install --save oidc-login
```

## Usage

1. Create access token with your chosen Open ID Connect provider (or providers)
2. Copy the sample `login-callback.html` provided to the root of your web server, or another well known location.
  If you're using a non-default path, you'd need to set the `redirect-uri` property of `<oidc-login>`
3. Use `<oidc-login>` to wrap the content that you want to be visible only to authenticated users.

### Sample Implementation:

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

### Supported Properties

 * `access-token` : This read-only property returns the OpenID Connect provider's access token after a successful authentication.
 * `provider` : This read-only property returns the OpenID Connect providers name as specified in the `<oidc-login-provider>` element.
 * `scope` : The OAuth 2.0 scope to retrieve from the server. Defaults to "`openid profile email`".
 * `redirect-uri` : Use a non-default URI for the callback page.

### Supported `<oidc-login-provider>` Properties

 * `name` : The OpenID Connect provider name. This property is used to select the provider to authenticate with using the `login()` API,
   and is also the name reported back in the `provider` property. Setting this property is only needed if there are more than one provider.
 * `discovery` : The OpenID Connect discovery URL for the provider. 
 * `client-id` : The OpenID Connect client token received from the provider.
 * `redirect-url` : Allow to override the non-default callback page URI on a per-provider basis.
 