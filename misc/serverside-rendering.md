# Serverside Rendering

## Samples

To learn more you should have a look at our samples:

* [nextjs \(sample provided by react-i18next\)](https://github.com/i18next/react-i18next/tree/master/example/nextjs)
* [razzle \(sample provided by react-i18next\)](https://github.com/i18next/react-i18next/tree/master/example/razzle-ssr)
* [simpleblack's boilerplate](https://github.com/simpleblack/react-redux-universal-hot-example)

{% hint style="info" %}
The usage of the `reactI18nextModule` for holding the i18n instance is not a valid option \(the instance would be set globally\). Always use the I18nextProvider like done in the samples above.
{% endhint %}

For futher information see this [issue](https://github.com/i18next/react-i18next/issues/375).

## Pass language and translations down to client

Both the [i18nextProvider](../components/i18nextprovider.md) and [translate hoc](../components/translate-hoc.md) allows to pass in `initialI18nStore` and `initialLanguage`. By doing so the translations won't be loaded and initial clientside render will avoid any flickering or rerender by checksum mismatch.

For details check the docs of those components or **have a look at the examples above**.

## loadNamespaces helper

**loadNamespaces**: Function that will pre-load all namespaces used by your components. Works well with `react-router` `match` function

**props**:

* components: Components that need to have namespaces loaded.
* i18n: the i18n instance to load translations into

```javascript
import { I18nextProvider, loadNamespaces } from 'react-i18next';
import { match } from 'react-router';

match({...matchArguments}, (error, redirectLocation, renderProps) => {
   loadNamespaces({ ...renderProps, i18n: i18nInstance })
   .then(()=>{
      // All i18n namespaces required to render this route are loaded
   })
});
```

## Report used namespaces

[I18nextProvider](../components/i18nextprovider.md) also accept a `reportNS` prop which is called for every namespace that is loaded with the [translate HOC](../components/translate-hoc.md).

You can use it to pass only the necessary namespaces down to the client like in the following example:

```javascript
import { I18nextProvider } from 'react-i18next';
import { renderToString } from 'react-dom/server';

import i18nInstance from './i18nInstance';
// i18nInstance is a i18next instance with all languages and namespaces preloaded

const namespaces = new Set();

const App = () => (
  <I18nextProvider i18n={i18nInstance} reportNS={ns => namespaces.add(ns)}>
    {bodyComponent}
  </I18nextProvider>
);

const markup = renderToString(<App />);

// Now we can use `namespaces` to construct a initialI18nStore object
// and pass it down to client through HTML. Example:

const initialLanguage = 'en';
const initialI18nStore = { [initialLanguage]: {} };

Array.from(namespaces).forEach(ns => {
  initialI18nStore[initialLanguage][ns] =
    i18nInstance.services.resourceStore.data[initialLanguage][ns];
});

// Then you can inject in the rendered HTML

const markupWithI18n = `
  ${markup}
  <script>
    window.initialLanguage = ${JSON.stringify(initialLanguage)};
    window.initialI18nStore = ${JSON.stringify(initialI18nStore)};
  </script>
`;

// ...
```

## use the i18next-express-middleware

When using [i18next-express-middleware](https://github.com/i18next/i18next-express-middleware), you can use `req.i18n` as the `i18next` instance for `I18nextProvider` it will assert no request conflicts happen \(each request gets it's cloned instance of i18next\):

```javascript
import { I18nextProvider } from 'react-i18next';
import i18n from './i18next'; // your own initialized i18next instance
import App from './app';

app.use((req, res) => {
   const component = (
      <I18nextProvider i18n={req.i18n}>
         <App />
      </I18nextProvider>
   );

   // render as desired now ...
});
```

