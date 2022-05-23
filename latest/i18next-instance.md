# i18next instance

The instance is an initialized i18next instance. In the following code snippet, we add a backend to load translations from server and a language detector for detecting user language.

> You can learn more about [i18next](http://i18next.com) and [plugins](https://www.i18next.com/plugins-and-utils.html#plugins) on the i18next website.

```javascript
import i18n from 'i18next';
import Backend from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';


i18n
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next) // bind react-i18next to the instance
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    // react i18next special options (optional)
    // override if needed - omit if ok with defaults
    /*
    react: {
      bindI18n: 'languageChanged',
      bindI18nStore: '',
      transEmptyNodeValue: '',
      transSupportBasicHtmlNodes: true,
      transKeepBasicHtmlNodesFor: ['br', 'strong', 'i'],
      useSuspense: true,
    }
    */
  });


export default i18n;
```

All additional options for react in init options:

| options                    | default                     | description                                                                                                                                                                                                      |
| -------------------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bindI18n                   | 'languageChanged'           | <p>which events trigger a rerender, can be set to false or string of events<br>separated by ""</p>                                                                                                               |
| bindI18nStore              | ''                          | define which events on [resourceStore](https://www.i18next.com/overview/api#store-events) should trigger a rerender                                                                                              |
| transEmptyNodeValue        | ''                          | how to treat failed lookups in Trans component                                                                                                                                                                   |
| transSupportBasicHtmlNodes | true                        | <p>convert eg. <code>&#x3C;br/></code> found in translations to a react component of type br<br><a href="trans-component.md#using-for-simple-html-elements-in-translations-v-10-4-0">See Trans component</a></p> |
| transKeepBasicHtmlNodesFor | \['br', 'strong', 'i', 'p'] | <p>Which nodes not to convert in defaultValue generation in the Trans component.<br><a href="trans-component.md#using-for-simple-html-elements-in-translations-v-10-4-0">See Trans component</a></p>             |
| useSuspense                | true                        | If using Suspense or not                                                                                                                                                                                         |
| keyPrefix                  | undefined                   | the optional `keyPrefix` will be automatically applied to the returned `t` function in [useTranslation](usetranslation-hook.md#optional-keyprefix-option) for example.                                           |

For more initialization options have look at the [docs](https://www.i18next.com/overview/configuration-options).
