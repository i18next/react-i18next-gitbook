# SSR \(additional components\)

## Using [Next.js](https://nextjs.org/)?

You should have a look at [next-i18next](https://github.com/isaachinman/next-i18next) which extends react-i18next to bring it to next.js the easiest way.

> With next-i18next &gt; v8.x and `Next.js v10`, next-i18next has done a major rewrite of the package, leveraging the built-in [internationalized routing](https://nextjs.org/docs/advanced-features/i18n-routing) provided by Next.js.
>
> [Here](https://github.com/locize/next-i18next-locize) you can also find a next-i18next app example in combination with locize, that offers 2 different approaches.

> `next-i18next@v5.0.0` supports `Next.js v9.5` in [**Serverless** mode](https://nextjs.org/blog/next-8#serverless-nextjs) \(as of [July 2020](https://github.com/isaachinman/next-i18next/issues/274#issuecomment-664616304)\). If your goal is to use earlier versions of Next.js with Serverless, then you should have a look at ["Next Right Now"](https://github.com/UnlyEd/next-right-now), which is a Next.js 9 boilerplate with built-in `i18next`, `react-i18next` and Locize.

## Setting the i18next instance based on req

Use the [I18nextProvider](i18nextprovider.md) to inject the i18next instance bound to the http i18n instance on the request object using [i18next-http-middleware](https://github.com/i18next/i18next-http-middleware).

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

export function InitSSR({ initialI18nStore, initialLanguage }) {
  useSSR(initialI18nStore, initialLanguage);

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

