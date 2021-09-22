# NamespacesConsumer \(v9\)

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

<table>
  <thead>
    <tr>
      <th style="text-align:left"><em><b>options</b></em>
      </th>
      <th style="text-align:left"><em><b>type (default)</b></em>
      </th>
      <th style="text-align:left"><em><b>description</b></em>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>wait</b>
      </td>
      <td style="text-align:left">boolean (false)</td>
      <td style="text-align:left">
        <p>assert all provided namespaces are loaded before rendering the component
          (can be set <a href="i18next-instance.md">globally</a> too).
          <br />
          <br /><em>Note that rendering will not be blocked again when dynamically updating the <code>ns</code> prop after initial mount.</em>
        </p>
        <p></p>
        <p><b>In most cases you like to set this to true.</b> If not handling not
          ready by evaluating ready.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">nsMode</td>
      <td style="text-align:left">string (&apos;default&apos;)</td>
      <td style="text-align:left"><em>default:</em> namespaces will be loaded and the first will be set as
        default
        <br />
        <br /><em>fallback:</em> namespaces will be used as fallbacks used in order provided</td>
    </tr>
    <tr>
      <td style="text-align:left">bindI18n</td>
      <td style="text-align:left">string (&apos;languageChanged loaded&apos;)</td>
      <td style="text-align:left">which events trigger a rerender, can be set to false or string of events</td>
    </tr>
    <tr>
      <td style="text-align:left">bindStore</td>
      <td style="text-align:left">string (&apos;added removed&apos;)</td>
      <td style="text-align:left">which events on store trigger a rerender, can be set to false or string
        of events</td>
    </tr>
    <tr>
      <td style="text-align:left">omitBoundRerenders</td>
      <td style="text-align:left">boolean (true)</td>
      <td style="text-align:left">Does not trigger rerenders while state not ready - avoiding unneeded renders
        on init</td>
    </tr>
    <tr>
      <td style="text-align:left">i18n</td>
      <td style="text-align:left">object (undefined)</td>
      <td style="text-align:left">pass i18next via options (useful for <a href="https://github.com/i18next/react-i18next/tree/master/example/nextjs">next.js usage</a>)</td>
    </tr>
    <tr>
      <td style="text-align:left">initialI18nStore</td>
      <td style="text-align:left">object (undefined)</td>
      <td style="text-align:left">pass in initial translations (useful for <a href="https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29">next.js usage</a>)</td>
    </tr>
    <tr>
      <td style="text-align:left">initialLanguage</td>
      <td style="text-align:left">string (undefined)</td>
      <td style="text-align:left">pass in initial language (useful for <a href="https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29">next.js usage</a>)</td>
    </tr>
  </tbody>
</table>

