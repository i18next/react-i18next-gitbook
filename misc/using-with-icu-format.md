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
      escapeValue: false, // not needed for react!!
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
{% tab title="JavaScript" %}
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

{% tab title="TypeScript" %}
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
Warning: direct use of `Trans` (not using the `icu.macro` babel macro) may not be compatible with [`react-compiler`](https://react.dev/learn/react-compiler). Use the `IcuTrans` component instead.
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

### using babel macros (Trans, Plural, Select) (deprecated)

{% hint style="warning" %}
**Deprecated:** the `react-i18next/icu.macro` Babel macro is deprecated in favour of using the [IcuTrans Component](../latest/icu-trans-component.md) directly. It still works on Babel 7 and is not removed, but it gets no new features, and:

* it fails on Babel 8 with `react-i18next/icu.macro: t.jSXIdentifier is not a function`
* `<Plural count={count} ...>` renders the raw ICU message (`{count, plural, ...}`) when the variable is named `count`; another name such as `count={itemsCount}` works
* a React `key` prop on `<Plural>` or `<Select>` is turned into an ICU case (`key {...}`) instead of staying a React key, so lists warn about missing keys and the extracted message changes

Everything below compiles to `IcuTrans`, so each sample can be written as an `IcuTrans` element with the same message.
{% endhint %}

{% hint style="info" %}
Thanks to using [kentcdodds/babel-plugin-macros](https://github.com/kentcdodds/babel-plugin-macros) we could use some babel magic to transpile nicer looking jsx to above Trans markup.

Check [https://github.com/kentcdodds/babel-plugin-macros/blob/master/other/docs/user.md](https://github.com/kentcdodds/babel-plugin-macros/blob/master/other/docs/user.md) for setting babel-plugin-macros up.

Using create-react-app? Make sure you are using react-scripts v2 as it includes the macro plugin.

```
$ # Create a new application
$ npx create-react-app
$ # Upgrade an existing application
$ yarn upgrade react-scripts@2
```
{% endhint %}

```javascript
import { Trans } from 'react-i18next/icu.macro';

const user = 'John Doe';

<Trans i18nKey="icu_and_trans">
  We invited <strong>{user}</strong>.
</Trans>
```

The macro will add the needed import for the `IcuTrans` Component and generate the correct `IcuTrans` component for you.

The correct string for translations will be shown in the browser console output as a missing string (if set debug: true on i18next init) or submitted via saveMissing (have saveMissing set true and a i18next backend supporting saving missing keys).

The defaults parsing supports the `@babel/react` preset, so any expressions that require more complex parsing may not work.

**More samples:**

```jsx
// basic interpolation
<Trans>Welcome, { name }!</Trans>

// interpolation and components
<Trans>Welcome, <strong>{ name }</strong>!</Trans>
<Trans defaults="Welcome, <strong>{ name }</strong>" />

// number formatting
<Trans>Trainers: { trainersCount, number }</Trans>
<Trans>Trainers: <strong>{ trainersCount, number }</strong>!</Trans>
<Trans defaults="Trainers: <strong>{ trainersCount, number }</strong>!" />

// date formatting
<Trans>Caught on { catchDate, date, short }</Trans>
<Trans>Caught on <strong>{ catchDate, date, short }</strong>!</Trans>
<Trans defaults="Caught on <strong>{ catchDate, date, short }</strong>!" />

<Trans>You have <Link to="/inbox">{ unread, number } messages</Link></Trans>
<Trans defaults="You have <Link to='/inbox'>{ unread, number } messages</Link>" />
```

#### Tagged Template for ICU

To support complex interpolations, `react-i18next` provides additional imports from the `icu.macro`. These provide a way to represent translations closer to the ICU messageformat syntax, but in a manner that is compatible with React and strictly typed in typescript.

For example, to format a number:

```javascript
import { Trans } from "react-i18next/icu.macro";

const num = 1;

<Trans i18nKey="number">
 Incremented {num, number} times
</Trans>
```

the above syntax, although valid javascript, will error when using a linting tool like eslint. Instead, we can do this:

```jsx
import { Trans, number } from "react-i18next/icu.macro";

const num = 1;

<Trans i18nKey="number">
 Incremented {number`${num}`} times
</Trans>
```

This results in the translation string `Incremented {num, number} times`

Supported interpolators are `number`, `date`, `time`, `select`, `plural`, and `selectOrdinal`.

More complex skeletons can also be represented:

```jsx
import { Trans, number } from "react-i18next/icu.macro";

const awesomePercentage = 100;

<Trans i18nKey="number">
 It's awesome {number`${awesomePercentage}, ::percent`} of the time
</Trans>
```

This results in the translation string `It's awesome {awesomePercentage, number, ::percent} of the time`.

**Complex interpolations with plural/select/selectOrdinal**

The `plural` and `select` and `selectOrdinal` interpolations support more advanced syntax. For instance, it is possible to interpolate both React elements and other interpolations:

```jsx
import { Trans, plural, number } from "react-i18next/icu.macro";

const awesomePercentage = 100;

<Trans i18nKey="number">
 {plural`${awesomePercentage},
   =0 { It's ${<i>never</i>} awesome }
   =100 { It is ${<b>ALWAYS</b>} awesome! }
   other { It's awesome ${number`${awesomePercentage}, ::percent`} of the time }`}
</Trans>
```

This will result in the translation string `{awesomePercentage, plural, =0 { It's <0>never</0> awesome } =100 { It is <1>ALWAYS</1> awesome! } other { It's awesome {awesomePercentage, number, ::percent} of the time }}`

It possible to nest any interpolated type, including nested `plural`, `select`, or `selectOrdinal`.

**Typescript support for interpolated template strings**

The `number`, `plural`, and `selectOrdinal` functions will error if a non-number typed variable is interpolated.

```jsx
import { Trans, number } from "react-i18next/icu.macro";

// type error below - awesomePercentage must be a number
const awesomePercentage = "100";

<Trans i18nKey="number">
 It's awesome {number`${awesomePercentage}, ::percent`} of the time
</Trans>
```

The `date` and `time` functions will error if a non-Date object is interpolated.

```jsx
import { Trans, date } from "react-i18next/icu.macro";

// type error below - awesomePercentage must be a number
const notADate = "100";

<Trans i18nKey="number">
 What time is it? it's {date`${notADate}`} o'clock
</Trans>
```

Finally, the `select` function will error if a non-string is interpolated.

```jsx
import { Trans, select } from "react-i18next/icu.macro";

// type error below - awesomePercentage must be a number
const notAString = 100;

<Trans i18nKey="number">
 {select`${notAString} oops { you have to pass in a string } other { oh well }`}
</Trans>
```

### Alternative syntax for select and plural

It is also possible to display `select` and `plural` and `selectOrdinal` using Elements `Select`, `Plural` and `SelectOrdinal`. All of them have full type safety in typescript.

#### Select

There is no way to directly add the needed ICU format inside a JSX child - so we had to add another component that gets transpiled to needed Trans component:

```jsx
import { Select } from 'react-i18next/icu.macro';

// simple select
<Select
  i18nKey="optionalKey" // optional key
  switch={gender}
  male="He avoids bugs."
  female="She avoids bugs."
  other="They avoid bugs."
/>
```

```jsx
import { Select } from 'react-i18next/icu.macro';

// select with inner components
<Select
  i18nKey="optionalKey" // optional key
  switch={gender}
  male={<Trans><strong>He</strong> avoids bugs.</Trans>}
  female={<Trans><strong>She</strong> avoids bugs.</Trans>}
  other={<Trans><strong>They</strong> avoid bugs.</Trans>}
/>
```

#### Plural

```jsx
import { Plural } from 'react-i18next/icu.macro';

// simple plural
<Plural
  i18nKey="optionalKey" // optional key
  count={itemsCount}
  $0="There is no item."
  one="There is # item."
  other="There are # items."
/>
```

```jsx
import { Plural } from 'react-i18next/icu.macro';

// plural with inner components
<Plural
  i18nKey="optionalKey" // optional key
  count={itemsCount3}
  $0={<Trans>There is <strong>no</strong> item.</Trans>}
  one={<Trans>There is <strong>#</strong> item.</Trans>}
  other={<Trans>There are <strong>#</strong> items.</Trans>}
/>
```

#### SelectOrdinal

```jsx
import { SelectOrdinal } from 'react-i18next/icu.macro';

// simple SelectOrdinal
<SelectOrdinal
  i18nKey="optionalKey"
  count={position}
  one="You are #st in line"
  two="You are #nd in line"
  few="You are #rd in line"
  other="You are #th in line"
/>
```

```jsx
import { SelectOrdinal } from 'react-i18next/icu.macro';

// SelectOrdinal with inner components
<SelectOrdinal
  i18nKey="optionalKey"
  count={position}
  one={<Trans>You are <strong>#st in line</strong></Trans>}
  two={<Trans>You are <strong>#nd in line</strong></Trans>}
  few={<Trans>You are <strong>#rd in line</strong></Trans>}
  other={<Trans>You are <strong>#th in line</strong></Trans>}
  $7={<Trans>You are the lucky <strong>#th in line</strong></Trans>}
/>
```

{% hint style="info" %}
The needed plural forms can be looked up in the official unicode cldr table: [https://www.unicode.org/cldr/charts/latest/supplemental/language_plural_rules.html](https://www.unicode.org/cldr/charts/latest/supplemental/language_plural_rules.html)

In addition to the plural forms you can specify results for given number values like show above:

`$0="show if zero"`

in ICU it would be `=0 {show if zero}` but `=` is not allowed to be leading char in attributes so we replaced it with `$`
{% endhint %}
