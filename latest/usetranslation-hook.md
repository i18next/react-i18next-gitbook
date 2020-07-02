# useTranslation \(hook\)

## What it does

It gets the `t` function and `i18n` instance inside your functional component.

```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const { t, i18n } = useTranslation();
  // or const [t, i18n] = useTranslation();

  return <p>{t('my translated text')}</p>
}
```

While most of the time you only need the `t` function to translate your content, you can also get the i18n instance \(in order to change the language\).

```javascript
i18n.changeLanguage('en-US');
```

{% hint style="info" %}
The `useTranslation` hook will trigger a [Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html) if not ready \(eg. pending load of translation files\). You can set `useSuspense` to false if prefer not using Suspense.
{% endhint %}

## When to use?

Use the `useTranslation` hook inside your **functional components** to access the translation function or i18n instance.

## useTranslation params

### Loading namespaces

```javascript
// load a specific namespace
// the t function will be set to that namespace as default
const { t, i18n } = useTranslation('ns1');
t('key'); // will be looked up from namespace ns1

// load multiple namespaces
// the t function will be set to first namespace as default
const { t, i18n } = useTranslation(['ns1', 'ns2', 'ns3']);
t('key'); // will be looked up from namespace ns1
t('ns2:key'); // will be looked up from namespace ns2
```

### Overriding the i18next instance

```javascript
// passing in an i18n instance
// use only if you do not like the default instance
// set by i18next.use(initReactI18next) or the I18nextProvider
import i18n from './i18n';
const { t, i18n } = useTranslation('ns1', { i18n });
```

### Not using Suspense

```javascript
// additional ready will state if translations are loaded or not
const { t, i18n, ready } = useTranslation('ns1', { useSuspense: false });
```

{% hint style="info" %}
Not using Suspense you will need to handle the not ready state yourself by eg. render a loading component as long `ready === false` . Not doing so will result in rendering your translations before they loaded which will cause save missing be called although translations exists \(just yet not loaded\).
{% endhint %}

