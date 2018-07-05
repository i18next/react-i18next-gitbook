# I18n \(render prop\)

The I18n component passes the [**t** function](https://www.i18next.com/overview/api#t) to child function and triggers loading the translation files defined. Further it asserts the component gets rerendered on language change or on changes to the translations themselves.

{% hint style="info" %}
To learn more about using the **t** function have a look at i18next documentation:

* [essentials](https://www.i18next.com/essentials.html)
* [interpolation](https://www.i18next.com/interpolation.html)
* [formatting](https://www.i18next.com/formatting.html)
* [plurals](https://www.i18next.com/plurals.html)
* ...
{% endhint %}

## Sample usage

```javascript
import React from 'react';
import { I18n } from 'react-i18next';

function TranslatableView() {
  return (
    <I18n ns={['defaultNamespace', 'anotherNamespace']}>
      {
        (t, { i18n, t, ready }) => (
          <div>
            <h1>{t('keyFromDefault')}</h1>
            <p>{t('anotherNamespace:key.from.another.namespace', { /* options t options */ })}</p>
          </div>
        )
      }
    </I18n>
  )
}
```

## I18n props

| _**options**_ | _**type \(default\)**_ | _**description**_ |
| --- | --- | --- |
| wait | boolean \(false\) | assert all provided namespaces are loaded before rendering the component \(can be set [globally](i18next-instance.md) too\) |
| nsMode | string \('default'\) | _default:_ namespaces will be loaded an the first will be set as default or _fallback:_ namespaces will be used as fallbacks used in order provided |
| bindI18n | string \('languageChanged loaded'\) | which events trigger a rerender, can be set to false or string of events |
| bindStore | string \('added removed'\) | which events on store trigger a rerender, can be set to false or string of events |
| i18n | object \(undefined\) | pass i18next via options \(useful for [next.js usage](https://github.com/i18next/react-i18next/tree/master/example/nextjs) |
| initialI18nStore | object \(undefined\) | pass in initial translations \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |
| initialLanguage | string \(undefined\) | pass in initial language \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |

