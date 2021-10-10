# SSR (v9)

## Using next.js?

You should have a look at [next-i18next](https://github.com/isaachinman/next-i18next) which extends react-i18next to bring it to next.js the easiest way.

## Samples

To learn more you should have a look at our samples:

* [razzle (sample provided by react-i18next)](https://github.com/i18next/react-i18next/tree/master/example/razzle-ssr)
* [simpleblack's boilerplate](https://github.com/simpleblack/react-redux-universal-hot-example)

{% hint style="info" %}
The usage of the `reactI18nextModule` for holding the i18n instance is not a valid option (the instance would be set globally). Always use the I18nextProvider like done in the samples above.
{% endhint %}

For further information see this [issue](https://github.com/i18next/react-i18next/issues/375).

## Pass language and translations down to client

Both the [i18nextProvider](i18nextprovider.md) and [translate hoc](serverside-rendering.md) allow to pass in `initialI18nStore` and `initialLanguage`. By doing so the translations won't be loaded and initial clientside render will avoid any flickering or rerender by checksum mismatch.

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

## use the i18next-express-middleware

When using [i18next-express-middleware](https://github.com/i18next/i18next-express-middleware), you can use `req.i18n` as the `i18next` instance for `I18nextProvider` it will assert no request conflicts happen (each request gets it's cloned instance of i18next):

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
