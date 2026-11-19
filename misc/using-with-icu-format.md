---
description: "Use the ICU message format in react-i18next with i18next-icu, including plurals and selects in ICU syntax."
---

# Using with ICU format

i18next itself is flexible enough to support multiple existing i18next formats beside its own. So also the ICU format, thanks to [i18next-icu](https://github.com/i18next/i18next-icu).

{% hint style="info" %}
Find the full working sample [here](https://github.com/i18next/react-i18next/tree/master/example/react-icu).
{% endhint %}

![](<../.gitbook/assets/Screen Shot 2018-07-30 at 10.25.19.png>)

## Extend the i18n instance with ICU module

To enable ICU format you will need to include the [i18next-icu](https://github.com/i18next/i18next-icu) module into your [i18next instance](../latest/i18next-instance.md). `intl-messageformat` is a peer dependency of i18next-icu, so install both:

```bash
npm install i18next-icu intl-messageformat
```

```javascript
import i18n from 'i18next';
import ICU from 'i18next-icu';
import Backend from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';

i18n
  .use(ICU)
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // React escapes what it renders (keep true for t() output in dangerouslySetInnerHTML)
    },

    // react i18next special options (optional)
    // react: {
    //   useSuspense: true
    // }
  });


export default i18n;
```

With i18next-icu, i18next's own plural and context suffix keys (`key_one`, `key_male`), `{{name}}` interpolation and `$t()` nesting stop working. [Using ICU or MF2](https://www.i18next.com/how-to/using-icu-or-mf2) in the i18next docs lists what keeps working and what does not.

{% hint style="info" %}
**TypeScript:** set `parseInterpolation: 'icu'` in your `CustomTypeOptions` (since i18next v26.5.0). The options of `t` and the `values` of `Trans` and `IcuTrans` are then checked against the ICU message: `{name}` takes a string or number, `plural`, `selectordinal` and `{n, number}` a number, `{d, date}` a `Date`, and a missing or misspelled argument is an error. The resources need literal types, as the resources file from the [i18next-cli](https://github.com/i18next/i18next-cli) `types` command has them.

```typescript
// i18next.d.ts
import 'i18next';

declare module 'i18next' {
  interface CustomTypeOptions {
    parseInterpolation: 'icu';
  }
}
```

With i18next v26.2.0 to v26.4.x, use `parseInterpolation: false` instead. The type extractor looks for `{{variable}}`, so ICU plurals like `{count, plural, one {{count} row} other {{count} rows}}` otherwise make `t` ask for variables that do not exist. Keys are still checked, the values are not.
{% endhint %}

## Use the ICU format

### using t function

{% tabs %}
{% tab title="String keys" %}
```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

function MyComponent() {
  const { t, i18n } = useTranslation();
  // or const [t, i18n] = useTranslation();

  return <div>{t('icu', { numPersons: 500 })}</div>
}

// ...

// json
"icu": "{numPersons, plural, =0 {no persons} =1 {one person} other {# persons}}",

// result:
<div>500 persons</div>
```
{% endtab %}

{% tab title="Selector API" %}
```tsx
import React from 'react';
import { useTranslation } from 'react-i18next';

function MyComponent() {
  const { t, i18n } = useTranslation();
  // or const [t, i18n] = useTranslation();
  
  return <div>{t($ => $.icu, { numPersons: 500 })}</div>
}

// ...

// json
"icu": "{numPersons, plural, =0 {no persons} =1 {one person} other {# persons}}",

// result:
<div>500 persons</div>
```
{% endtab %}
{% endtabs %}

### using the Trans Component

{% hint style="warning" %}
Warning: direct use of `Trans` with ICU messages may not be compatible with [`react-compiler`](https://react.dev/learn/react-compiler). Use the `IcuTrans` component instead.
{% endhint %}

Using ICU syntax is not possible within a JSX node because `{curly brackets}` are reserved for interpolation.

To work around this, use the [IcuTrans Component](../latest/icu-trans-component.md). It takes the ICU message as a string and a list of components that replace the numbered tags:

```javascript
import { IcuTrans } from 'react-i18next';

const user = 'John Doe';

<IcuTrans
  i18nKey="icu_and_trans"
  defaultTranslation="We invited <0>{user}</0>."
  content={[{ type: "strong" }]}
  values={{ user }}
/>

// json
"icu_and_trans": "We invited <0>{user}</0>."

// result
We invited <strong>John Doe</strong>.
```

See the [IcuTrans Component](../latest/icu-trans-component.md) page for plurals inside such a message, nested elements, usage in React Server Components and the props reference.

### using babel macros (Trans, Plural, Select): removed in v18

The `react-i18next/icu.macro` Babel macro, deprecated since v17.0.21, is removed in v18. It compiled JSX into `IcuTrans` elements, so write the [IcuTrans Component](../latest/icu-trans-component.md) directly, with plurals and selects inside the ICU message. The [migration guide](../latest/migrating-v17-to-v18.md) has a before and after example. On react-i18next 17 the macro keeps working on Babel 7.
