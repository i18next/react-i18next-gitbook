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

<table>
  <thead>
    <tr>
      <th style="text-align:left"><em><b>options</b></em>
      </th>
      <th style="text-align:left"><em><b>default</b></em>
      </th>
      <th style="text-align:left"><em><b>description</b></em>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">bindI18n</td>
      <td style="text-align:left">&apos;languageChanged&apos;</td>
      <td style="text-align:left">which events trigger a rerender, can be set to false or string of events
        separated by &quot; &quot;</td>
    </tr>
    <tr>
      <td style="text-align:left">bindI18nStore</td>
      <td style="text-align:left">&apos;&apos;</td>
      <td style="text-align:left">define which events on <a href="https://www.i18next.com/overview/api#store-events">resourceStore</a> should
        trigger a rerender</td>
    </tr>
    <tr>
      <td style="text-align:left">transEmptyNodeValue</td>
      <td style="text-align:left">&apos;&apos;</td>
      <td style="text-align:left">how to treat failed lookups in Trans component</td>
    </tr>
    <tr>
      <td style="text-align:left">transSupportBasicHtmlNodes</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">
        <p>convert eg. &lt;br/&gt; found in translations to a react component of
          type br</p>
        <p></p>
        <p><a href="trans-component.md#using-for-simple-html-elements-in-translations-v-10-4-0">See Trans component</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">transKeepBasicHtmlNodesFor</td>
      <td style="text-align:left">[&apos;br&apos;, &apos;strong&apos;, &apos;i&apos;, &apos;p&apos;]</td>
      <td
      style="text-align:left">
        <p>Which nodes not to convert in defaultValue generation in the Trans component.</p>
        <p></p>
        <p><a href="trans-component.md#using-for-simple-html-elements-in-translations-v-10-4-0">See Trans component</a>
        </p>
        </td>
    </tr>
    <tr>
      <td style="text-align:left">useSuspense</td>
      <td style="text-align:left">true</td>
      <td style="text-align:left">If using Suspense or not</td>
    </tr>
  </tbody>
</table>For more initialization options have look at the [docs](https://www.i18next.com/configuration-options.html).

