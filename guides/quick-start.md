# Quick start

## Install needed dependencies

We expect you having an existing react application - if not give [create-react-app](https://github.com/facebook/create-react-app) a try.

Install both react-i18next and i18next package:

```bash
npm install react-i18next i18next --save
```

Why do you need i18next package? i18next is the core that provides all translation functionality while react-i18next gives some extra power for using with react.

## Configure i18next

Create a new file `i18n.js` beside your `index.js` containing following content:

```javascript
import i18n from "i18next";
import { initReactI18next } from "react-i18next";

// the translations
// (tip move them in a JSON file and import them)
const resources = {
  en: {
    translation: {
      "Welcome to React": "Welcome to React and react-i18next"
    }
  },
  fr: {
    translation: {
      "Welcome to React": "Bienvenue à React et react-i18next"
    }
  }
};

i18n
  .use(initReactI18next) // passes i18n down to react-i18next
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

The interesting part here is by `i18n.use(initReactI18next)` we pass the i18n instance to react-i18next which will make it available for all the components via the context api.

Then import that in `index.js`:

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

## Translate your content

### Using the hook

Using the hook in functional components is one of the options you got.

The `t` function is the main function in i18next to translate content. Read the [documentation](https://www.i18next.com/translation-function/essentials) for all the options.

```jsx
import React from 'react';

// the hook
import { useTranslation } from 'react-i18next';

function MyComponent () {
  const { t, i18n } = useTranslation();
  return <h1>{t('Welcome to React')}</h1>
}
```

Learn more about the hook [useTranslation](../latest/usetranslation-hook.md).

### Using the HOC

Using higher order components is one of the most used method to extend existing components by passing additional props to them.

The `t` function is the main function in i18next to translate content. Read the [documentation](https://www.i18next.com/translation-function/essentials) for all the options.

```jsx
import React from 'react';

// the hoc
import { withTranslation } from 'react-i18next';

function MyComponent ({ t }) {
  return <h1>{t('Welcome to React')}</h1>
}

export default withTranslation()(MyComponent);
```

Learn more about the higher order component [withTranslation](../latest/withtranslation-hoc.md).

### Using the render prop

The render prop enables you to use the `t` function inside your component.

```jsx
import React from 'react';

// the render prop
import { Translation } from 'react-i18next';

export default function MyComponent () {
  return (
    <Translation>
      {
        t => <h1>{t('Welcome to React')}</h1>
      }
    </Translation>
  )
}
```

Learn more about the render prop [Translation](../latest/translation-render-prop.md).

### Using the Trans component

The Trans component is the best way to translate a JSX tree in one translation. This enables you to eg. easily translate text containing a link component or formatting like `<strong>`.

```jsx
import React from 'react';
import { Trans } from 'react-i18next';

export default function MyComponent () {
  return <Trans><H1>Welcome to React</H1></Trans>
}

// the translation in this case should be
"<0>Welcome to React</0>": "<0>Welcome to React and react-i18next</0>"
```

Don't worry if you do not yet understand how the Trans component works in detail. Learn more about it [here](../latest/trans-component.md).

## Next steps

Depending on your learning style, you can now read the more in-depth [step by step](../latest/using-with-hooks.md) guide and learn how to load translations using xhr or how to change the language.

Prefer having code to checkout? Directly dive into our examples:

* [Example react](https://github.com/i18next/react-i18next/tree/master/example/react)

