# Step by step guide

Lets start with the sample created in the [quick start guide](quick-start.md) and extend it to be of more use.

{% hint style="info" %}
react-i18next will run in any environment without you having to do changes to your babel or webpack setup.
{% endhint %}

## Initial situation based on quick start

We have installed the needed i18n packages react-i18next and i18next:

```bash
npm install react-i18next i18next --save
```

Added following files:

**translation.json** \(/public/locales/en/translation.json\)

```javascript
{
  "Welcome to React": "Welcome to React and react-i18next"
}
```

**i18n.js** \(/src/i18n.js\)

```javascript
import i18n from "i18next";
import { reactI18nextModule } from "react-i18next";

import translationEN from '../public/locales/en/translation.json';

// the translations
const resources = {
  en: {
    translation: translationEN
  }
};

i18n
  .use(reactI18nextModule) // passes i18n down to react-i18next
  .init({
    resources,
    lng: "en",
    
    keySeparator: false, // we do not use keys in form messages.welcome

    interpolation: {
      escapeValue: false // react already safes from xss
    }
  });
  
export default i18n;
```

Make sure to import `i18n.js` in **index.js**:

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import './i18n';
import App from './App';

// append app to dom
ReactDOM.render(
  <App />,
  document.getElementById("root")
);
```

Have **App.js** using the `t` function for translation:

```jsx
import React from 'react';

// the hoc
import { withNamespaces } from 'react-i18next';

function App ({ t }) {
  return <h1>{t('Welcome to React')}</h1>
}

export default withNamespaces()(App);
```

## Adding more languages

### 1\) Add an additional language file

**translation.json** \(/public/locales/**de**/translation.json\)

```javascript
{
  "Welcome to React": "Willkommen bei react und react-i18next"
}
```

### 2\) Add the additional translations on init

in i18n.js:

```javascript
// ...
import translationEN from '../public/locales/en/translation.json';
import translationDE from '../public/locales/de/translation.json';

// the translations
const resources = {
  en: {
    translation: translationEN
  },
  de: {
    translation: translationDE
  }
};

// ...
```

### 3\) Auto detect the user language

As the language is set on `i18n.init` you either could create some custom code setting the needed language or just use one of the provided language detectors coming with i18next.

For browser usage there is the [i18next-browser-languageDetector](https://github.com/i18next/i18next-browser-languageDetector) which detects language based on:

* cookie
* localStorage
* navigator
* querystring \(append `?lng=LANGUAGE` to URL\)
* htmlTag
* path
* subdomain

```bash
npm install i18next-browser-languagedetector --save
```

Using it before i18n.init is all needed to get this to work:

```javascript
import i18n from "i18next";
import detector from "i18next-browser-languagedetector";
import { reactI18nextModule } from "react-i18next";

import translationEN from '../public/locales/en/translation.json';
import translationDE from '../public/locales/de/translation.json';

// the translations
const resources = {
  en: {
    translation: translationEN
  },
  de: {
    translation: translationDE
  }
};

i18n
  .use(detector)
  .use(reactI18nextModule) // passes i18n down to react-i18next
  .init({
    resources,
    lng: "en",
    fallbackLng: "en", // use en if detected lng is not available
    
    keySeparator: false, // we do not use keys in form messages.welcome

    interpolation: {
      escapeValue: false // react already safes from xss
    }
  )};
  
export default i18n;
```

Now we already are able to set language based on the browsers set language or by appending `?lng=LANGUAGE` to the URL.

### 4\) Let the user toggle the language

Call [i18n.changeLanguage](https://www.i18next.com/overview/api#changelanguage) is all needed to do.

```jsx
import React from 'react';
import i18n from './i18n';
import { withNamespaces } from 'react-i18next';

function App ({ t }) {
  const changeLanguage = (lng) => {
    i18n.changeLanguage(lng);
  }

  return (
    <div>
      <button onClick={() => changeLanguage('de')}>de</button>
      <button onClick={() => changeLanguage('en')}>en</button>
      <h1>{t('Welcome to React')}</h1>
    </div>
  )
}

export default withNamespaces()(App);
```

{% hint style="info" %}
It's essential to have at least your outer page level / container component wrapped with the [withNamespaces](../components/withnamespaces.md) or [NamespacesConsumer](../components/namespacesconsumer.md) as those are bound to the [languageChanged event](https://www.i18next.com/overview/api#onlanguagechanged) and trigger a needed rerender.
{% endhint %}

## Lazy loading translations

We not yet started splitting translations into multiple files \(which is highly recommended for larger projects\) but we already see that adding more languages would result in bundling unneeded translations into the application.

> Why not just use dynamic imports to load the language needed?

This for sure works but comes with one drawback. If you have a change in your translations you will need to rebuild your application and deploy that.

This might not be a problem when starting but at some point you will learn localization is a complete different beast than just adding i18n to your code. You will keep translations as separated from your code as you can - so developers and translators can work as independent as possible.

### 1\) Adding lazy loading for translations

This will be simpler than you think. All needed to be done is adding another package called [i18next-xhr-backend](https://github.com/i18next/i18next-xhr-backend) and using that.

```bash
npm install i18next-xhr-backend --save
```

**i18n.js**

```javascript
import i18n from "i18next";
import detector from "i18next-browser-languagedetector";
import backend from "i18next-xhr-backend";
import { reactI18nextModule } from "react-i18next";

// translations are already at
// '../public/locales/en/translation.json'
// which is the default for the xhr backend to load from

i18n
  .use(detector)
  .use(backend)
  .use(reactI18nextModule) // passes i18n down to react-i18next
  .init({
    lng: "en",
    fallbackLng: "en", // use en if detected lng is not available
    
    keySeparator: false, // we do not use keys in form messages.welcome

    interpolation: {
      escapeValue: false // react already safes from xss
    }
  )};
  
export default i18n;
```

{% hint style="info" %}
i18next  implementation is smart enough to only load needed languages and comes with intelligent deduplications so even multiple load requests for files in different code locations result in one request while notifying all needed requester.
{% endhint %}

### 2\) Loading multiple translation files

Lets assume your project started to grow and you like to split translations into multiple files.

Without configuration i18next will always load one file \(namespace\) named `translation`. Learn more about namespaces [here](https://www.i18next.com/principles/namespaces).

You can load them on i18n.init or in code like:

```javascript
i18n.init({
  ns: ['common', 'moduleA', 'moduleB'],
  defaultNS: 'moduleA'
}, (err, t) => {
  i18n.t('myKey'); // key in moduleA namespace (defined default)
  i18n.t('common:myKey'); // key in common namespace
});

// load additional namespaces after initialization
i18n.loadNamespaces('anotherNamespace', (err, t) => { /* ... */ });
```

But that will load all upfront or in some custom code where you would need to handle manually that the translations where loaded. So there is a better way.

#### Use the withNamespaces hoc

As you might guess the withNamespaces hocs role is to lazy load the needed namespaces.

**page2.json Add more translation files:** \(/public/locales/en/page2.json\)

```javascript
{
  "Welcome on page2": "Welcome on page2"
}
```

**Page2.js** \(/src/Page2.js\)

```jsx
import React from 'react';

// the hoc
import { withNamespaces } from 'react-i18next';

function Page2 ({ t }) {
  return <h1>{t('Welcome on page2')}</h1>
}

export default withNamespaces('page2')(Page2);
```

### 3\) Handle rendering while translations are not yet loaded

In the above `Page2` you will notice the initial render will trigger the load for `page2.json` but also render the page without the translations ready. There will be a rerender when the translations where loaded resulting in the page flickering.

#### Using the prop tReady

tReady will be set to true when translations are loaded so you can eg. suspend rendering and show a Spinner or another placeholder:

```jsx
import React from 'react';

// the hoc
import { withNamespaces } from 'react-i18next';

function Page2 ({ t, tReady }) {
  // just to add some more here we load an additional namespace
  // common for "common" used texts and use that namespace
  // be prefixing it in front of the key "common:"
  if (!tReady) return <p>{t('common:loading')}</p>

  return <h1>{t('Welcome on page2')}</h1>
}

export default withNamespaces(['page2', 'common'])(Page2);
```

#### Set the global wait option

You can configure the withNamespaces / NamespacesConsumer to not render the content until needed namespaces are loaded.

**i18n.js:**

```javascript
import i18n from "i18next";
import detector from "i18next-browser-languagedetector";
import backend from "i18next-xhr-backend";
import { reactI18nextModule } from "react-i18next";

i18n
  .use(detector)
  .use(backend)
  .use(reactI18nextModule) // passes i18n down to react-i18next
  .init({
    lng: "en",
    fallbackLng: "en", // use en if detected lng is not available
    
    keySeparator: false, // we do not use keys in form messages.welcome

    interpolation: {
      escapeValue: false // react already safes from xss
    },
    
    // react-i18next options
    react: {
      wait: true
    }
  )};
  
export default i18n;
```

Now `Page2` will be blank until the needed translation files were loaded.

{% hint style="info" %}
You can set the wait option either globally for all instances or individually like:  
  
`withNamepaces('page2', { wait: true })(Page2);`
{% endhint %}



