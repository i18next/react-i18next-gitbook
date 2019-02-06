# SSR \(additional components\)

## Using next.js?

You should have a look at [next-i18next](https://github.com/isaachinman/next-i18next) which extends react-i18next to bring it to next.js the easiest way.

## Setting the i18next instance based on req

Use the [I18nextProvider](i18nextprovider.md) to inject the i18next instance bound to the express.js i18n instance on the request object using [i18next-express-middleware](https://github.com/i18next/i18next-express-middleware).

```jsx
<I18nextProvider i18n={req.i18n}>
  <App />
</I18nextProvider>
```

## Passing initial translations / initial language down to client

To avoid asynchronous loading of translation on the client side \(and the possible Suspense out of that\) you will need to pass down initialLanguage \(will call changeLanguage on i18next\) and initialI18nStore \(will prefill translations in i18next store\).

### using the useSSR hook

```jsx
import React from 'react';
import { useSSR } from 'react-i18next';

export function InitSSR({ initialLanguage, initialI18nStore }) {
  useSSR(initialLanguage, initialI18nStore);
  
  return <App />
}
```

### using the withSSR HOC

```jsx
import React from 'react';
import { withSSR } from 'react-i18next';
import App from './App';

const ExtendedApp = withSSR()(App);

<ExtendedApp initialLanguage={} initialI18nStore={} />
```

The ExtendedApp in this case will also have the composed `ExtendedApp.getInitialProps()`

