# Translation \(render prop\)

## What it does <a id="what-it-does"></a>

The `Translation` is a render prop and gets the `t` function and `i18n` instance to your component.

```jsx
import React from 'react';
import { Translation } from 'react-i18next';

export function MyComponent() {
  return (
    <Translation>
      {
        (t, { i18n }) => <p>{t('my translated text')}</p>
      }
    </Translation>
  )
}
```

While you most time only need the t function to translate your content you also get the i18n instance to eg. change the language.

```javascript
i18n.changeLanguage('en-US');
```

{% hint style="info" %}
The `Translation` render prop will trigger a [Suspense](https://reactjs.org/docs/code-splitting.html#suspense) if not ready \(eg. pending load of translation files\). You can set `useSuspense` to false if prefer not using Suspense.
{% endhint %}

## When to use?

Use the `Translation` render prop inside **any component \(class or function\)** to access the translation function or i18n instance. 

## Translation params

### Loading namespaces

```jsx
// load a specific namespace
// the t function will be set to that namespace as default
<Translation ns="ns1">
{
  (t) => <p>{t('my translated text')}</p> // will be looked up from namespace ns1
}
</Translation>

// load multiple namespaces
// the t function will be set to first namespace as default
<Translation ns={['ns1', 'ns2', 'ns3']}>
{
  (t) => <p>{t('my translated text')}</p> // will be looked up from namespace ns1
}
</Translation>
```

### Overriding the i18next instance

```jsx
// passing in an i18n instance
// use only if you do not like the default instance
// set by i18next.use(initReactI18next) or the I18nextProvider
import i18n from './i18n';

<Translation i18n={i18n}>
{
  (t, { i18n }) => <p>{t('my translated text')}</p> // will be looked up from namespace ns1
}
</Translation>
```



