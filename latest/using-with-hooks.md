# Step by step guide

## Install needed dependencies

We expect you to have an existing react application supporting [hooks](https://reactjs.org/docs/hooks-intro.html) (at least v16.7.0-alpha of react and react-dom).

Install both react-i18next and i18next packages:

```bash
npm install react-i18next i18next --save

# if you'd like to detect user language and load translation
npm install i18next-http-backend i18next-browser-languagedetector --save
```

### Configure i18next

I18next is the core of the i18n functionality while react-i18next extends and glues it to react.

Create a new file `i18n.js` beside your `index.js` containing following content:

```javascript
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

import Backend from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
// don't want to use this?
// have a look at the Quick start guide 
// for passing in lng and translations on init

i18n
  // load translation using http -> see /public/locales (i.e. https://github.com/i18next/react-i18next/tree/master/example/react/public/locales)
  // learn more: https://github.com/i18next/i18next-http-backend
  // want your translations to be loaded from a professional CDN? => https://github.com/locize/react-tutorial#step-2---use-the-locize-cdn
  .use(Backend)
  // detect user language
  // learn more: https://github.com/i18next/i18next-browser-languageDetector
  .use(LanguageDetector)
  // pass the i18n instance to react-i18next.
  .use(initReactI18next)
  // init i18next
  // for all options read: https://www.i18next.com/overview/configuration-options
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react as it escapes by default
    }
  });


export default i18n;
```

The interesting part here is by `i18n.use(initReactI18next)` we pass the i18n instance to react-i18next which will make it available for all the components.

Then import that in `index.js`:

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import App from './App';

// import i18n (needs to be bundled ;)) 
import './i18n';

ReactDOM.render(
  <App />,
  document.getElementById("root")
);
```

{% hint style="info" %}
If you need to access the `t` function or the `i18next` instance from outside of a React component you can simply import your `./i18n.js` and use the exported i18next instance:

```
import i18next from './i18n'

i18next.t('my.key')
```
{% endhint %}

### Translate your content

#### Using the useTranslation hook

You can use the hook inside your functional components like:

```jsx
import React, { Suspense } from 'react';
import { useTranslation } from 'react-i18next';

function MyComponent() {
  const { t, i18n } = useTranslation();

  return <h1>{t('Welcome to React')}</h1>
}

// i18n translations might still be loaded by the http backend
// use react's Suspense
export default function App() {
  return (
    <Suspense fallback="loading">
      <MyComponent />
    </Suspense>
  );
}
```

The useTranslation hook function takes one options argument. You can either pass in a namespace or an array of namespaces to load.

```javascript
const { t, i18n } = useTranslation('common');

const { t, i18n } = useTranslation(['page1', 'common']);
```

#### Translation Files

Create a new file `public/locales/<language_code>/translation.json` with the following sample content.

```
{
  "title": "Welcome to react using react-i18next",
  "description": {
    "part1": "To get started, edit <1>src/App.js</1> and save to reload.",
    "part2": "Switch language between english and german using buttons above."
  }
}
```

Files are plain JSON you can checkout the full sample [here](https://github.com/i18next/react-i18next/tree/master/example/react/public/locales).

{% hint style="info" %}
Please note the t function will be either bound to the default namespace defined on i18next init or to the first one passed in arguments.
{% endhint %}

{% content-ref url="../guides/multiple-translation-files.md" %}
[multiple-translation-files.md](../guides/multiple-translation-files.md)
{% endcontent-ref %}

#### Using the withTranslation HOC

There might be some legacy cases where you are still forced to use classes. Don't worry, we still provide a hoc to cover these cases:

```jsx
import React, { Component, Suspense } from 'react';
import { withTranslation } from 'react-i18next';

class LegacyComponentClass extends Component {
  render() {
    const { t } = this.props;

    return (
      <h1>{t('Welcome to React')}</h1>
    )
  }
}
const MyComponent = withTranslation()(LegacyComponentClass)

// i18n translations might still be loaded by the http backend
// use react's Suspense
export default function App() {
  return (
    <Suspense fallback="loading">
      <MyComponent />
    </Suspense>
  );
}
```

The withTranslation hook function takes one options argument. You can either pass in a namespace or a array of namespaces to load.

```javascript
withTranslation('common')(LegacyComponentClass);

withTranslation(['page1', 'common'])(LegacyComponentClass);
```

#### Using the Trans component

The Trans component is the best way to translate a JSX tree in one translation. This enables you to eg. easily translate text containing a link component or formatting like `<strong>`.

```jsx
import React from 'react';
import { Trans } from 'react-i18next';

export default function MyComponent () {
  return <Trans>Welcome to <strong>React</strong></Trans>
}

// the translation in this case should be
"Welcome to <1>React</1>": "Welcome to <1>React and react-i18next</1>"
```

Don't worry if you do not yet understand how the Trans component works in detail. Learn more about it [here](trans-component.md).

## See the sample

Prefer having code to checkout? Directly dive into our example:

* [using hooks with react-i18next](https://github.com/i18next/react-i18next/tree/master/example/react)

### Do you like to read a more complete step by step tutorial?

{% hint style="success" %}
[Here](https://dev.to/adrai/how-to-properly-internationalize-a-react-application-using-i18next-3hdb) you'll find a simple tutorial on how to best use react-i18next.\
Some basics of i18next and some cool possibilities on how to optimize your localization workflow.[\
![](<../.gitbook/assets/title width.jpg>)](https://dev.to/adrai/how-to-properly-internationalize-a-react-application-using-i18next-3hdb)
{% endhint %}
