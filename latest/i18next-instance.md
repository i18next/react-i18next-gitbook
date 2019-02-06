# i18next instance

The instance is an initialized i18next instance. In the following code snippet, we add a backend to load translations from server and a language detector for detecting user language.

> You can learn more about [i18next](http://i18next.com) and [plugins](https://www.i18next.com/plugins-and-utils.html#plugins) on the i18next website.

```javascript
import i18n from 'i18next';
import XHR from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';


i18n
  .use(XHR)
  .use(LanguageDetector)
  .use(initReactI18next) // bind react-i18next to the instance
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    // react i18next special options (optional)
    /*
    react: {
      bindI18n: 'languageChanged loaded'
    }
    */
  });


export default i18n;
```

All additional options for react in init options:

| _**options**_ | _**default**_ | _**description**_ |
| :--- | :--- | :--- |
| bindI18n | 'languageChanged' | which events trigger a rerender, can be set to false or string of events separated by " " |
| transEmptyNodeValue | '' | how to treat failed lookups in Trans component |

For more initialization options have look at the [docs](https://www.i18next.com/configuration-options.html).

