<a href="https://indykite.com">
    <img src="logo.png" alt="IndyKite logo" title="IndyKite.ID" align="right" width="200"/>
</a>

# IndyKite Client UI SDK

IndyKite is a cloud identity platform built to secure and manage human & non-person (IoT) identities and their data. This repository contains JavaScript Library packages for the [IndyKite Platform](https://indykite.com/) client SDK.

This library contains multiple client libraries for each service of the platform.

[![NPM version](https://img.shields.io/npm/v/@indykiteone/jarvis-sdk-web.svg?style=flat-square)](https://www.npmjs.com/package/@indykiteone/jarvis-sdk-web)
![npm type definitions](https://img.shields.io/npm/types/@indykiteone/jarvis-sdk-web?style=flat-square)
[![codecov](https://codecov.io/gh/indykite/jarvis-sdk-web/branch/master/graph/badge.svg?token=33M4Y8GYFV)](https://codecov.io/gh/indykite/jarvis-sdk-web)

You need to get an API key before you can access the platform.

## Terminology

| Definition               | Description                                                                                      |
| ------------------------ | ------------------------------------------------------------------------------------------------ |
| Digital Twin             | The digital identity of a physical entity on/in a software/identity system .    |
| Application Space ID     | The ID of the application the digital twin belongs to.                                         |
| Application Agent ID     | The ID of the agent that makes the application available for different calls.                    |
| Tenant ID                | The ID of the tenant the digital twin belongs to. The tenant belongs to an application space. |
| Private Key and Settings | The secret required to reach the system. Indykite provides the necessary secrets.           |
| Property                 | The digital twin's property (e.g., email, name).                                                   |
| JWT                      | An acronym for JSON Web Tokens.                                                                                  |
| Introspect               | A process used to validate the token and retrieve properties assigned to the token.            |
| Patch property           | Used to add, change, or delete a digital twin's property.                                               |

## Documentation

Visit the IndyKite.one Developer Community site for official [IndyKite documentation](https://indykite.one/blog?category=5e3e9297-3451-4b52-91ee-8027dcd1789c), and learn how to use the platform for your project.

## Installation

##### From [npm](https://www.npmjs.com/package/@indykiteone/jarvis-sdk-web):

```
npm install @indykiteone/jarvis-sdk-web
```

##### From CDN:

UNPKG

```javascript
<script src="https://unpkg.com/@indykiteone/jarvis-sdk-web/dist/iksdkweb.dist.js"></script>
```

jsDelivr

```javascript
<script src="https://cdn.jsdelivr.net/npm/@indykiteone/jarvis-sdk-web/dist/iksdkweb.dist.js"></script>
```

### Browser support

#### CDN .dist.js version

Built with support for all recent versions of major browsers (Chrome, FF, Safari, Edge, Opera).

#### Import version

`import { IKUIInit } from "@indykiteone/jarvis-sdk-web";` is not prebuilt. You need to build your application using tools like Webpack and Babel.
If you're building apps with frameworks like React, Angular, or Vue.js, you most likely have already set up
a build process (often automatically by the framework), so you don't need to do anything.

If you need support for Internet Explorer (ES5), please contact us through the Issues page [issues page](https://github.com/indykite/jarvis-sdk-web/issues), and we'll give you a built SDK or build instructions.

## API

##### dist.js package

If you are using the dist package, all functions are exported under the `IK` keyword. For example:

```html
<script src="https://unpkg.com/@indykiteone/jarvis-sdk-web/dist/iksdkweb.dist.js"></script>
<script>
 IK.IKUIInit({});
</script
```

## Getting started

### Trial

For a trial, please contact [IndyKite](https://www.indykite.com/contact-us) to set up and configure the platform.

### Init

To use the functions from the library, you first need to initialize the lib. This should be done in entry point to the app:

```javascript
import { IKUIInit } from "@indykiteone/jarvis-sdk-web";

IKUIInit({
  baseUri: process.env.REACT_APP_INDY_BASE_URI,
  applicationId: process.env.REACT_APP_INDY_CLIENT_ID,
  tenantId: process.env.REACT_APP_INDY_TENANT_ID,
});
```

### Core - UI API

To use UI elements provided by the @indykiteone/jarvis-sdk-web library, make sure you have completed initializing the library as described above.
All render functions do network setup calls, so they should be treated as side effects in the JS framework of your application.

#### Form rendering

If you want to render the form without any customization, you only need to call the `IKUICore.renderForm` function with `renderElementSelector` and `onSuccess` parameters:

```javascript
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      redirectUri: "/callback", // Optional - Needed only if you use OIDC (Google, FB etc) buttons
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

#### Forgotten password flow

It's common that your login screen contains a `Forgotten password` action. What if you want to render the forgotten password screen as the initial form? You just need to update your authentication flow a little bit. You need to add a new condition to your `Switch` node with the following condition:

```
has(session.input) && has(session.input.flow) && session.input.flow == 'forgotten'
```

and connect the new branch with the `Forgotten password` node as it's on the image.

![](/assets/forgotten_password_flow.png)

Then, in the rendering function, add the flow argument and start the flow with the Forgotten password form:

```javascript
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      arguments: {
        flow: "forgotten",
      },
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

#### Set a new password flow (a flow with OTP)

Use the `IKUICore.renderForm` function. You only need to pass an `otpToken` property there, as shown in the example that follows:

```javascript
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      otpToken: referenceId,
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

#### Replace the defult component

Use the `onRenderComponent` property to tweak an existing element or create a totally new one. In the example that follows, we included a small _"Enter the same password here"_ message under the second password input in the registration form:

```javascript
import ReactDOM from "react-dom";
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      onRenderComponent: (component, componentType, componentSubtype) => {
        if (componentType === "form" && componentSubtype === "password-confirm") {
          const wrapper = document.createElement("div");
          ReactDOM.render(
            <i>
              <small>Enter the same password here</small>
            </i>,
            wrapper,
          );
          wrapper.prepend(component);
          return wrapper;
        }
      },
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

You can also replace a component with your custom one. In the example that follows, we replaced the form submit button:

```javascript
import ReactDOM from "react-dom";
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      onRenderComponent: (component, componentType, componentSubtype, ...rest) => {
        if (componentType === "form" && componentSubtype === "submit") {
          const [clickHandler] = rest;
          const newButton = document.createElement("button");
          newButton.innerText = "Custom submit";
          newButton.addEventListener("click", clickHandler);
          return newButton;
        }
      },
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

Or, you can update an existing component. Here, we added a custom class to all of our inputs:

```javascript
import ReactDOM from "react-dom";
import { IKUICore } from "@indykiteone/jarvis-sdk-web";

const IKForm = () => {
  // React example, call only during the first render. Side effect!
  React.useEffect(() => {
    IKUICore.renderForm({
      renderElementSelector: "#form-container",
      onSuccess: (data) => {
        console.log(data);
      },
      onFail: (error) => {
        console.error(error);
      },
      onRenderComponent: (component, componentType) => {
        if (componentType === "form") {
          const inputs = component.getElementsByTagName("input");
          if (inputs.length > 0) {
            Array.from(inputs).forEach((input) => input.classList.add("my-input-class"));
          }
          return component;
        }
      },
    });
  }, []);

  return (
    <div>
      <div id="form-container" />
    </div>
  );
};
```

## IKUIOidc - OIDC Support

### Handling the OIDC callback

To log in with Google or Facebook, you need to pass the `redirectUri` property to your `IKUICore.renderForm` function call. After you finish the login/registration with the external OIDC provider, you're redirected to this URI. On this route, call the `IKUIOidc.oidcCallback()` function, which will process the returned values from the OIDC provider and return an object to you. Determine whether this object contains a `redirect_to` property, and if so, redirect your page to this URI. If the object doesn't have such a property, then the object should contain the access token:

```javascript
import React from "react";
import { IKUIOidc } from "@indykiteone/jarvis-sdk-web";

const Callback = () => {
  React.useEffect(() => {
    IKUIOidc.oidcCallback()
      .then((data) => {
        if (data.redirect_to) {
          window.location.href = data.redirect_to;
          return;
        }
        console.log(data.token);
        console.log(data.refresh_token);
        console.log(data.token_type); // For example "bearer"
        console.log(data.expiration_time); // Timestamp
        console.log(data.expires_in); // In x seconds
      })
      .catch(console.log);
  }, []);
  return <span>OIDC callback</span>;
};
```

After you redirect your page to the URI from the `redirect_to` property, and you call the `IKUICore.renderForm` function, you only need to call that function once. If you are using React, ensure your `useEffect` is called only once. Otherwise, the data from the OIDC provider will be lost.

### OIDC provider

When you act as an OIDC provider, you have to call `IKUIOidc.handleOidcOriginalParamsAndRedirect` function. It's good to have a special route so you know you are redirected here from a different page. This function will store its actual state and redirect you to a route  you usually use for logging users:I 

```javascript
import React from "react";
import { IKUIOidc } from "@indykiteone/jarvis-sdk-web";

const Oidc = () => {
  React.useEffect(() => {
    // Put your default login route to the function argument
    IKUIOidc.handleOidcOriginalParamsAndRedirect("/login");
  }, []);

  return <span>OIDC login...</span>;
};
```

## TypeScript

The library has built-in types is compatible with Typescript projects.

## SDK development

If you want to develop the lib with the app, look into using `npm link`.

If you update the response, remember to update CommonJS docs and any Typescript definitions.

Here is an example of a commit message [commit guidelines]: ./doc/guides/commit-message.md#commit-message-guidelines

Install husky pre-commit hooks:
`npm install & npm run prepare`

### Localization development

All the messages and keys are stored in the localise.com app under project Indykite:
https://app.lokalise.com/project/5428220160b483af770945.85338584/

All the keys for the UI SDK only should have tag `uisdk`.

To export new localization keys and error messages, go to this download page:
https://app.lokalise.com/download/5428220160b483af770945.85338584/

Download settings are as follows:

```
Format: JSON
- Include all platform keys YES

Plural format: ICU
Placeholder format: ICU
```

Click build and download. Put the final JSONs into the `lib/services/core/locale/src`folder.
See the`.github/export-settings.png` file for the export settings from localize.

## Roadmap

Check out our roadmap on our [issues page](https://github.com/indykite/jarvis-sdk-web/issues)

## Contributing to this project

[Contribution guidelines for this project](contributing.md)

## Support, feedback, connect with other developers

Connect with us or other community developers at [IndyKite.one](https://indykite.one).

Feel free to file a bug, submit an issue, or give us feedback on our [issues page](https://github.com/indykite/jarvis-sdk-web/issues).

## Vulnerability reporting

[Responsible Disclosure](https://www.indykite.com/security)

## Changelog

Coming soon!

## Contributers / Acknowledgements

Coming soon!

## What is IndyKite

IndyKite is a cloud identity platform built to secure and manage human & non-person (IoT) identities and their data. Based on open source standards, the cloud platform gives developers the ability to secure data and embed identity controls into their Web 3.0 applications. Empowering the world’s 23 million developers without the need to involve security and identity specialists.

## License

[This project is licensed under the terms of the Apache 2.0 license.](LICENSE)
