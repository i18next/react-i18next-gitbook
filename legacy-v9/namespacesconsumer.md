# NamespacesConsumer (v9)

{% hint style="info" %}
Was introduced in v8.0.0. Not available in older versions.
{% endhint %}

The NamespacesConsumer is a so called render prop. The component passes the [**t** function](https://www.i18next.com/overview/api#t) to child function and triggers loading the translation files defined. Further it asserts the component gets rerendered on language change or on changes to the translations themselves.

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
import { NamespacesConsumer } from 'react-i18next';

function TranslatableView() {
  return (
    <NamespacesConsumer ns={[
      'defaultNamespace',
      'anotherNamespace'
    ]}>
      {
        (t, { i18n, ready }) => (
          <div>
            <h1>{t('keyFromDefault')}</h1>
            <p>{t('anotherNamespace:key.from.another.namespace', { /* options t options */ })}</p>
          </div>
        )
      }
    </NamespacesConsumer>
  )
}
```

## NamespacesConsumer props

| _**options**_      | _**type (default)**_              | _**description**_                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------ | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **wait**           | boolean (false)                   | <p>assert all provided namespaces are loaded before rendering the component (can be set <a href="i18next-instance.md">globally</a> too).<br><br><em>Note that rendering will not be blocked again when dynamically updating the <code>ns</code> prop after initial mount.</em></p><p><strong>In most cases you like to set this to true.</strong> If not handling not ready by evaluating ready.</p> |
| nsMode             | string ('default')                | <p><em>default:</em> namespaces will be loaded and the first will be set as default<br><br><em>fallback:</em> namespaces will be used as fallbacks used in order provided</p>                                                                                                                                                                                                                        |
| bindI18n           | string ('languageChanged loaded') | which events trigger a rerender, can be set to false or string of events                                                                                                                                                                                                                                                                                                                             |
| bindStore          | string ('added removed')          | which events on store trigger a rerender, can be set to false or string of events                                                                                                                                                                                                                                                                                                                    |
| omitBoundRerenders | boolean (true)                    | Does not trigger rerenders while state not ready - avoiding unneeded renders on init                                                                                                                                                                                                                                                                                                                 |
| i18n               | object (undefined)                | pass i18next via options (useful for [next.js usage](https://github.com/i18next/react-i18next/tree/master/example/nextjs))                                                                                                                                                                                                                                                                           |
| initialI18nStore   | object (undefined)                | pass in initial translations (useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29))                                                                                                                                                                                                                                                    |
| initialLanguage    | string (undefined)                | pass in initial language (useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29))                                                                                                                                                                                                                                                        |
