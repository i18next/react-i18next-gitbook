# i18next instance

The instance is a initialized i18next instance. In the following code snipplet we add a backend to load translations from server and a language detector for detecting user language.

You can learn more about [i18next](http://i18next.com) and [plugins](https://www.i18next.com/plugins-and-utils.html#plugins) on the i18next website.

```js
import i18n from 'i18next';
import XHR from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { reactI18nextModule } from 'react-i18next';


i18n
  .use(XHR)
  .use(LanguageDetector)
  .use(reactI18nextModule) // if not using I18nextProvider
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },
    
    // react i18next special options (optional)
    react: {
      wait: false,
      bindI18n: 'languageChanged loaded',
      bindStore: 'added removed',
      nsMode: 'default'
    }
  });


export default i18n;
```

All additional options for react in init options:

| options | default | description |
| --- | --- | --- |
| wait | false | assert all provided namespaces are loaded before rendering the component \(can be set [globally](/components/i18next-instance.md) too\) |
| nsMode | 'default' | _default:_ namespaces will be loaded an the first will be set as default or _fallback:_ namespaces will be used as fallbacks used in order provided |
| bindI18n | 'languageChanged loaded' | which events trigger a rerender, can be set to false or string of events |
| bindStore | 'added removed' | which events on store trigger a rerender, can be set to false or string of events |




For more initialization options have look at the [docs](https://www.i18next.com/configuration-options.html).

The instance could be passed to the [I18nextProvider](/components/i18nextprovider.md) or directly to the [translate hoc](/components/translate-hoc.md).