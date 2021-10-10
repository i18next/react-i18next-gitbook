# Using with ICU format

i18next itself is flexible enough to support multiple existing i18next formats beside its own. So also the ICU format, thanks to [i18next-icu](https://github.com/i18next/i18next-icu).

{% hint style="info" %}
Find the full working sample [here](https://github.com/i18next/react-i18next/tree/master/example/react-icu).
{% endhint %}

![](<../.gitbook/assets/Screen Shot 2018-07-30 at 10.25.19.png>)

## Extend the i18n instance with ICU module

To enable ICU format you will need to include the [i18next-icu](https://github.com/i18next/i18next-icu) module into your [i18next instance](../legacy-v9/i18next-instance.md).

```javascript
import i18n from 'i18next';
import ICU from 'i18next-icu';
import Backend from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { reactI18nextModule } from 'react-i18next';

i18n
  .use(ICU)
  .use(Backend)
  .use(LanguageDetector)
  .use(reactI18nextModule) // if not using I18nextProvider
  .init({
    fallbackLng: 'en',
    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    // react i18next special options (optional)
    react: {
      bindI18n: 'languageChanged loaded',
      bindStore: 'added removed',
      nsMode: 'default'
    }
  });


export default i18n;
```

## Use the ICU format

### using t function

```javascript
import React, { Component } from 'react';
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

### using the Trans Component

As is including plain ICU syntax inside a JSX node will result in invalid JSX as the ICU format uses curly brackets that are reserved by JSX.

So the default option is to use the [Trans Component](../legacy-v9/trans-component.md) just with props like:

```javascript
import { Trans } from 'react-i18next';

const user = 'John Doe';

<Trans
  i18nKey="icu_and_trans"
  defaults="We invited <0>{user}</0>."
  components={[<strong>dummyChild</strong>]}
  values={{ user }}
/>

// json
"icu_and_trans": "We invited <0>{user}</0>."

// result
We invited <strong>John Doe</strong>.
```

While this works the resulting JSX is very verbose - guess we could do better.

### using babel macros (Trans, Plural, Select)

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

The macro will add the needed import for Trans Component and generate the correct Trans component for you.

The correct string for translations will be shown in the browser console output as a missing string (if set debug: true on i18next init) or submitted via saveMissing (have saveMissing set true and a i18next backend supporting saving missing keys).

If linting or other code analysis tools are complaining or failing because of the invalid JSX syntax, you can use the `defaults` prop instead of putting your message as a child, and it will be parsed and updated to the correct format.

```javascript
import { Trans } from 'react-i18next/icu.macro';

const user = 'John Doe';

<Trans 
  i18nKey="icu_and_trans_defaults"
  defaults="We invited <strong>{user}</strong>."
/>
```

This will be converted by the macro into:

```javascript
import { Trans } from 'react-i18next';

const user = 'John Doe';

<Trans 
  i18nKey="icu_and_trans_defaults"
  values={{user}}
  defaults="We invited <0>{user}</0>."
  components={[<strong>{user}</strong>]}
/>
```

The defaults parsing supports the `@babel/react` preset, so any expressions that require more complex parsing may not work.

**More samples:**

```markup
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

```javascript
import { Trans, number } from "react-i18next/icu.macro";

const num = 1;

<Trans i18nKey="number">
 Incremented {number`${num}`} times
</Trans>
```

This results in the translation string `Incremented {num, number} times`

Supported interpolators are `number`, `date`, `time`, `select`, `plural`, and `selectOrdinal`.

More complex skeletons can also be represented:

```javascript
import { Trans, number } from "react-i18next/icu.macro";

const awesomePercentage = 100;

<Trans i18nKey="number">
 It's awesome {number`${awesomePercentage}, ::percent`} of the time
</Trans>
```

This results in the translation string `It's awesome {awesomePercentage, number, ::percent} of the time`.

**Complex interpolations with plural/select/selectOrdinal**

The `plural` and `select` and `selectOrdinal` interpolations support more advanced syntax. For instance, it is possible to interpolate both React elements and other interpolations:

```javascript
import { Trans, plural, number } from "react-i18next/icu.macro";

const awesomePercentage = 100;

<Trans i18nKey="number">
 {plural`${awesomePercentage},
   =0 { It's ${<i>never</i>} awesome }
   =100 { It is ${<b>ALWAYS</b>} awesome! }
   other { It's awesome {number`${awesomePercentage}, ::percent`} of the time }`}
</Trans>
```

This will result in the translation string `{awesomePercentage, plural, =0 { It's <0>never&lt;/0&gt; awesome } =100 { It is <1>ALWAYS&lt;/1&gt; awesome! } =100 { It's awesome {awesomePercentage, number, ::percent} of the time }}`

It possible to nest any interpolated type, including nested `plural`, `select`, or `selectOrdinal`.

**Typescript support for interpolated template strings**

The `number`, `plural`, and `selectOrdinal` functions will error if a non-number typed variable is interpolated.

```javascript
import { Trans, number } from "react-i18next/icu.macro";

// type error below - awesomePercentage must be a number
const awesomePercentage = "100";

<Trans i18nKey="number">
 It's awesome {number`${awesomePercentage}, ::percent`} of the time
</Trans>
```

The `date` and `time` functions will error if a non-Date object is interpolated.

```javascript
import { Trans, date } from "react-i18next/icu.macro";

// type error below - awesomePercentage must be a number
const notADate = "100";

<Trans i18nKey="number">
 What time is it? it's {date`${notADate}`} o'clock
</Trans>
```

Finally, the `select` function will error if a non-string is interpolated.

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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
The needed plural forms can be looked up in the official unicode cldr table: [http://www.unicode.org/cldr/charts/33/supplemental/language_plural_rules.html](http://www.unicode.org/cldr/charts/33/supplemental/language_plural_rules.html)

In addition to the plural forms you can specify results for given number values like show above:

`0="show if zero"`

in ICU it would be `=0 {show if zero}` but `=` is not allowed to be leading char in attributes so we replaced it with `$`
{% endhint %}
