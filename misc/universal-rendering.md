# Serverside Rendering

__loadNamespaces__: Function that will pre-load all namespaces used by your components.  Works well with `react-router` `match` function

__props__:

- components: Components that need to have namespaces loaded.
- i18n: the i18n instance to load translations into

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
When using [i18next-express-middleware](https://github.com/i18next/i18next-express-middleware), you can use `req.i18n` as the `i18next` instance for `I18nextProvider`:

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

## samples

- [Universal boilerplate](https://github.com/simpleblack/react-redux-universal-hot-example).
- [nextjs](https://github.com/i18next/react-i18next/tree/master/example/nextjs)
