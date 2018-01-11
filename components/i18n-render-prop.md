# I18n (render prop)

The I18n component passes `t` function to child function and triggers loading the translation files needed. Further it asserts the component gets rerendered on language change or changes to the translations themself.


To learn more about using the `t` function have a look at i18next documentation:

* [essentials](https://www.i18next.com/essentials.html)
* [interpolation](https://www.i18next.com/interpolation.html)
* [formatting](https://www.i18next.com/formatting.html)
* [plurals](https://www.i18next.com/plurals.html)
* ...

## Sample usage

```js
import React from 'react';
import { I18n } from 'react-i18next';

function TranslatableView(props) {
  const { t } = props;

  return (
    <I18n ns={['defaultNamespace', 'anotherNamespace']}>
      {
        (t, { i18n }) => (
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

| options | default | description |
| --- | --- | --- |
| wait | false | assert all provided namespaces are loaded before rendering the component \(can be set [globally](/components/i18next-instance.md) too\) |
| nsMode | 'default' | _default:_ namespaces will be loaded an the first will be set as default or _fallback:_ namespaces will be used as fallbacks used in order provided |
| bindI18n | 'languageChanged loaded' | which events trigger a rerender, can be set to false or string of events |
| bindStore | 'added removed' | which events on store trigger a rerender, can be set to false or string of events |
| i18n | undefined | pass i18next via options \(useful for [next.js usage](https://github.com/i18next/react-i18next/tree/master/example/nextjs) |
| initialI18nStore | undefined | pass in initial translations \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |
| initialLanguage | undefined | pass in initial language \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |





