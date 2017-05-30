# i18next instance

The instance is a initialized i18next instance. In the following code snipplet we add a backend to load translations from server and a language detector for detecting user language.

You can learn more about [i18next](http://i18next.com) and [plugins](https://www.i18next.com/plugins-and-utils.html#plugins) on the i18next website.

```js
import i18n from 'i18next';
import XHR from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';


i18n
  .use(XHR)
  .use(LanguageDetector)
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    }
  });


export default i18n;
```

For more initialization options have look at the [docs](https://www.i18next.com/configuration-options.html).

The instance should be passed to the [I18nextProvider](/components/i18nextprovider.md).