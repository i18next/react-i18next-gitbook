# Using with hooks

When we first heard about react [introducing hooks](https://reactjs.org/docs/hooks-intro.html) \(that was yesterday\) our feelings were mixed. But one night and a few hours of coding later we are convinced this is the best thing happening to web development after the introduction of react itself.

So we are proud to.....wait a second....

![](../.gitbook/assets/pill.jpeg)

> After this, there is no turning **back**. You take the blue pill—the story ends, you wake up in your bed and believe whatever you want to believe. You take the red pill—you stay in Wonderland, and I show you how deep the rabbit hole goes. Remember: all I'm offering is the truth.  
>   
> The matrix \(1999\)

## Took the blue pill?

Just forget everything...go to the current getting started guide 😄. Nothing will change for you.

## Took the red pill: Learn about the new react-i18next 🌈

So there it is **v8.2.0** of react-i18next. Forget about importing from `react-i18next` the shiny things are imported from `react-i18next/hooks` .

From here everything gets easier.

### Install needed dependencies

We expect you having an existing react application supporting [hooks](https://reactjs.org/docs/hooks-intro.html) \(v16.7.0-alpha of react and react-dom\).

Install both react-i18next and i18next package:

```bash
npm install react-i18next i18next --save

// when like to detect user language and load translation
npm install i18next-xhr-backend i18next-browser-languagedetector --save
```

### Configure i18next

I18next is the core of the i18n functionality while react-i18next extends and glue it to react.

Create a new file `i18n.js` beside your `index.js` containing following content:

```javascript
import i18n from 'i18next';
import { initReactI18n } from 'react-i18next/hooks';

import Backend from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
// not like to use this?
// have a look at the Quick start guide 
// for passing in lng and translations on init

i18n
  // load translation using xhr -> see /public/locales
  // learn more: https://github.com/i18next/i18next-xhr-backend
  .use(Backend)
  // detect user language
  // learn more: https://github.com/i18next/i18next-browser-languageDetector
  .use(LanguageDetector)
  // pass the i18n instance to react-i18next.
  .use(initReactI18n)
  // init i18next
  // for all options read: https://www.i18next.com/overview/configuration-options
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react as it escapes by default
    },

    // special options for react-i18next
    // learn more: https://react.i18next.com/components/i18next-instance
    react: {
      wait: true
    }
  });


export default i18n;
```

The interesting part here is by `i18n.use(initReactI18n)` we pass the i18n instance to react-i18next which will make it available for all the components.

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

### Translate your content

#### Using the useT hook

You can use the hook inside your functional components like:

```jsx
import React from 'react';
import { useT } from 'react-i18next/hooks';

export default function MyComponent () {
  const [t, ready, i18n] = useT();
  
  // not yet loaded the translations
  // render nothing or a placeholder?!?
  if (!ready) return null;
  
  return <h1>{t('Welcome to React')}</h1>
}
```

The useT hook function takes one options argument. You can either pass in a namespace or a array of namespaces to load.

```javascript
const [t, ready, i18n] = useT('common');

const [t, ready, i18n] = useT(['page1', 'common']);
```

{% hint style="info" %}
Please note the t function will be either bound to the default namespace defined on i18next init or to the first one passed in in arguments.
{% endhint %}

#### Using the withT HOC

There might be some legacy cases where you still forced to use classes. No worry we still provide a hoc to cover this cases:

```jsx
import React, { Component } from 'react';
import { withT } from 'react-i18next/hooks';

class LegacyComponentClass extends Component {
  render() {
    const { t, ready } = this.props;

    if (!ready) return null;

    return (
      <h1>{t('Welcome to React')}</h1>
    )
  }
}
export default withT()(LegacyComponentClass)
```

The withT hook function takes one options argument. You can either pass in a namespace or a array of namespaces to load.

```javascript
withT('common')(LegacyComponentClass);

withT(['page1', 'common'])(LegacyComponentClass);
```

#### Using the Trans component

The Trans component is the best way to translate a JSX tree in one translation. This enables you to eg. easily translate text containing a link component or formatting like `<strong>`.

```jsx
import React from 'react';
import { Trans } from 'react-i18next/hooks';

export default function MyComponent () {
  return <Trans>Welcome to <strong>React</strong></Trans>
}

// the translation in this case should be
"Welcome to <1>React</1>": "Welcome to <1>React and react-i18next</1>"
```

Don't worry if you do not yet understand how the Trans component works in detail. Learn more about it [here](../components/trans-component.md).

## See the sample

Prefer having code to checkout? Directly dive into our example:

* [using hooks with react-i18next](https://github.com/i18next/react-i18next/tree/master/example/react-hooks)

