# useTranslation (hook)

## What it does

It gets the `t` function and `i18n` instance inside your functional component.

{% tabs %}
{% tab title="JavaScript" %}
```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const { t, i18n } = useTranslation(); // not passing any namespace will use the defaultNS (by default set to 'translation')
  // or const [t, i18n] = useTranslation();

  return <p>{t('my translated text')}</p>
}
```
{% endtab %}

{% tab title="TypeScript" %}
```tsx
import React from 'react';
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const { t, i18n } = useTranslation(); // not passing any namespace will use the defaultNS (by default set to 'translation')
  // or const [t, i18n] = useTranslation();

  return <p>{t($ => $['my translated text'])}</p>
}
```
{% endtab %}
{% endtabs %}

While most of the time you only need the `t` function to translate your content, you can also get the i18n instance (in order to change the language).

```javascript
i18n.changeLanguage('en-US');
```

{% hint style="info" %}
The `useTranslation` hook will trigger a [Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html) if not ready (eg. pending load of translation files). You can set `useSuspense` to false if prefer not using Suspense.
{% endhint %}

## When to use?

Use the `useTranslation` hook inside your **functional components** to access the translation function or i18n instance.

{% hint style="success" %}
In [this tutorial](https://locize.com/blog/react-i18next/) you'll find some ways on how to use this useTranslation hook.

You'll also see how to use it when you need to work with [multiple namespaces](https://locize.com/blog/react-i18next/#multiple-namespaces).[\
<img src="../.gitbook/assets/title width (1).jpg" alt="" data-size="original">](https://locize.com/blog/react-i18next/)
{% endhint %}

## useTranslation params

### Loading namespaces

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-jsx"><code class="lang-jsx"><strong>// load a specific namespace
</strong><strong>// the t function will be set to that namespace as default
</strong>const { t, i18n } = useTranslation('ns1');
t('key'); // will be looked up from namespace ns1

// load multiple namespaces
// the t function will be set to first namespace as default
const { t, i18n } = useTranslation(['ns1', 'ns2', 'ns3']);
t('key'); // will be looked up from namespace ns1
t('key', { ns: 'ns2' }); // will be looked up from namespace ns2
</code></pre>
{% endtab %}

{% tab title="TypeScript" %}
```tsx
// load a specific namespace
// the t function will be set to that namespace as default
const { t, i18n } = useTranslation('ns1');
t('key'); // will be looked up from namespace ns1

// load multiple namespaces
// the t function will be set to first namespace as default
const { t, i18n } = useTranslation(['ns1', 'ns2', 'ns3']);
t($ => $.key); // will be looked up from namespace ns1
t($ => $.key, { ns: 'ns2' }); // will be looked up from namespace ns2
```
{% endtab %}
{% endtabs %}

### Overriding the i18next instance

```javascript
// passing in an i18n instance
// use only if you do not like the default instance
// set by i18next.use(initReactI18next) or the I18nextProvider
import i18n from './i18n';
const { t, i18n } = useTranslation('ns1', { i18n });
```

### Optional keyPrefix option

> available in react-i18next version >= 11.12.0
>
> depends on i18next version >= 20.6.0

{% tabs %}
{% tab title="JavaScript" %}
```jsx
// having JSON in namespace "translation" like this:
/*{
    "very": {
      "deeply": {
        "nested": {
          "key": "here"
        }
      }
    }
}*/
// you can define a keyPrefix to be used for the resulting t function
const { t } = useTranslation('translation', { keyPrefix: 'very.deeply.nested' });
const text = t('key'); // "here"
```
{% endtab %}

{% tab title="TypeScript" %}
```tsx
// having JSON in namespace "translation" like this:
/*{
    "very": {
      "deeply": {
        "nested": {
          "key": "here"
        }
      }
    }
}*/
// you can define a keyPrefix to be used for the resulting t function
const { t } = useTranslation('translation', { keyPrefix: 'very.deeply.nested' });
const text = t($ => $.key); // "here"
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
{% hint style="warning" %}
Do **not** use the `keyPrefix` option if you want to use keys with prefixed namespace notation:

i.e.

```javascript
const { t } = useTranslation('translation', { keyPrefix: 'very.deeply.nested' });
const text = t('ns:key'); // this will not work
```
{% endhint %}
{% endtab %}

{% tab title="TypeScript" %}
{% hint style="warning" %}
Do **not** use the `keyPrefix` option if you want to use keys with prefixed namespace notation:

i.e.

```javascript
const { t } = useTranslation('translation', { keyPrefix: 'very.deeply.nested' });
const text = t($ => $.key, { ns: 'ns' }); // this will not work
```
{% endhint %}
{% endtab %}
{% endtabs %}

### Optional lng option

> available in react-i18next version >= 12.3.1

{% tabs %}
{% tab title="JavaScript" %}
```jsx
// you can pass a language to be used for the resulting t function
const { t } = useTranslation('translation', { lng: 'de' });
const text = t('key'); // "hier"
```
{% endtab %}

{% tab title="TypeScript" %}
```tsx
// you can pass a language to be used for the resulting t function
const { t } = useTranslation('translation', { lng: 'de' });
const text = t($ => $.key); // "hier"
```
{% endtab %}
{% endtabs %}

### Not using Suspense

```javascript
// additional ready will state if translations are loaded or not
const { t, i18n, ready } = useTranslation('ns1', { useSuspense: false });
```

{% hint style="info" %}
Not using Suspense you will need to handle the not ready state yourself by eg. render a loading component as long `!ready` . Not doing so will result in rendering your translations before they loaded which will cause save missing be called although translations exists (just yet not loaded).
{% endhint %}
