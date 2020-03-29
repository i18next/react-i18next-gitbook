# Extracting translations

At some point you will come to the question how to get new translation key/values into your namespace \(translation\) file.

## 1\) Adding new strings manually

While for sure this is the least efficient method for adding new translations, we know a lot of projects are doing this. There is actually nothing wrong with it beside being some extra work developers could avoid.

## 2\) Using an extraction tool

Static extraction tools can read through your code files to automatically find and export translation keys. [i18next-scanner](http://i18next.github.io/i18next-scanner), [i18next-parser](https://github.com/i18next/i18next-parser) and [babel-plugin-i18next-extract](https://github.com/gilbsgilbs/babel-plugin-i18next-extract) are sensible choices to achieve this goal.

For more information about extraction tools, see [plugin and utils](https://www.i18next.com/overview/plugins-and-utils#extraction-tools) documentation page.

## 3\) Runtime extraction

I18next has a setting to send all keys that it was unable to resolve during runtime using the attached backend.

In case of the xhr-backend just set `saveMissing: true` on init:

```javascript
import i18n from "i18next";
import detector from "i18next-browser-languagedetector";
import backend from "i18next-xhr-backend";
import { initReactI18next } from "react-i18next";

i18n
  .use(detector)
  .use(backend)
  .use(initReactI18next) // passes i18n down to react-i18next
  .init({
    lng: "en",
    fallbackLng: "en", // use en if detected lng is not available

    saveMissing: true, // send not translated keys to endpoint

    keySeparator: false, // we do not use keys in form messages.welcome

    interpolation: {
      escapeValue: false // react already safes from xss
    }
  });

export default i18n;
```

Check the [options](https://github.com/i18next/i18next-xhr-backend#backend-options) for where missing translation gets sent.

Using node.js and express? You can get that endpoint for free: [https://github.com/i18next/i18next-express-middleware\#add-routes](https://github.com/i18next/i18next-express-middleware#add-routes)

This is the most convenient way of working with react-i18next: just develop and run your applications without worrying too much about adding translations to your catalog as those get added automatically.

{% hint style="info" %}
Wanna have this process on steroids? Just hook up a [locize.com](https://locize.com) localization project using the provided backend and get both the saveMissing and loading of translations automated in a true continuous localization process.
{% endhint %}

