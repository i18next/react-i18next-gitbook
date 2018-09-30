# Using with ICU format

i18next itself is flexible enough to support multiple existing i18next formats beside it's own.

{% hint style="info" %}
Find the full working sample here: 

[https://github.com/i18next/react-i18next/tree/master/example/react\_icu\_withHOC](https://github.com/i18next/react-i18next/tree/master/example/react_icu_withHOC)
{% endhint %}

![](../.gitbook/assets/screen-shot-2018-07-30-at-10.25.19.png)

## Extend the i18n instance with ICU module

To enable ICU format you will need to include the [i18next-icu](https://github.com/i18next/i18next-icu) module into your [i18next instance](../components/i18next-instance.md).

```javascript
import i18n from 'i18next';
import ICU from 'i18next-icu';
import XHR from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { reactI18nextModule } from 'react-i18next';

// import locale-data for needed lngs
import de from 'i18next-icu/locale-data/de';

i18n
  .use(new ICU({
    localeData: de // you also can pass in array of localeData
  }))
  .use(XHR)
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
      wait: false,
      bindI18n: 'languageChanged loaded',
      bindStore: 'added removed',
      nsMode: 'default'
    }
  });


export default i18n;

```

{% hint style="info" %}
To dynamically import the needed locale-data have a look here: [https://github.com/locize/locize-react-intl-example/blob/master/src/locize/index.js\#L53](https://github.com/locize/locize-react-intl-example/blob/master/src/locize/index.js#L53)
{% endhint %}

## Use the ICU format

### using t function

```javascript
import React, { Component } from 'react';
import { translate } from 'react-i18next';

function myComponent({ t }) => {
  return <div>{t('icu', { numPersons: 500 })}</div>
}

export default translate()(myComponent);

// json
"icu": "{numPersons, plural, =0 {no persons} =1 {one person} other {# persons}}",

// result:
<div>500 persons</div>
```

### using the Trans Component

As is including plain ICU syntax inside a JSX node will result in invalid JSX as the ICU format uses curly brackets that are reserved by JSX.

So the default option is to use the [Trans Component](../components/trans-component.md) just with props like:

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

### using babel macros \(Trans, Plural, Select\)

{% hint style="info" %}
Thanks to using [kentcdodds/babel-plugin-macros](https://github.com/kentcdodds/babel-plugin-macros) we could use some babel magic to transpile nicer looking jsx to above Trans markup.  
  
Check [https://github.com/kentcdodds/babel-plugin-macros/blob/master/other/docs/user.md](https://github.com/kentcdodds/babel-plugin-macros/blob/master/other/docs/user.md) for setting babel-plugin-macros up.

Using create-react-app? Make sure you are using alpha version of react-scripts 2.0 as it includes the macro plugin.

```
$ # Create a new application
$ npx create-react-app@next --scripts-version=2.0.0-next.47d2d941
$ # Upgrade an existing application
$ yarn upgrade react-scripts@2.0.0-next.47d2d941
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

The correct string for translations will be shown in the browser console output as a missing string \(if set debug: true on i18next init\) or submitted via saveMissing \(have saveMissing set true and a i18next backend supporting saving missing keys\).

**More samples:**

```markup
// basic interpolation
<Trans>Welcome, { name }!</Trans>

// interpolation and components
<Trans>Welcome, <strong>{ name }</strong>!</Trans>

// number formatting
<Trans>Trainers: { trainersCount, number }</Trans>
<Trans>Trainers: <strong>{ trainersCount, number }</strong>!</Trans>

// date formatting
<Trans>Caught on { catchDate, date, short }</Trans>
<Trans>Caught on <strong>{ catchDate, date, short }</strong>!</Trans>
```

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

{% hint style="info" %}
The needed plural forms can be looked up in the official unicode cldr table: [http://www.unicode.org/cldr/charts/33/supplemental/language\_plural\_rules.html](http://www.unicode.org/cldr/charts/33/supplemental/language_plural_rules.html)  
  
In addition to the plural forms you can specify results for given number values like show above:  
  
`0="show if zero"`

in ICU it would be `=0 {show if zero}` but `=` is not allowed to be leading char in attributes so we replaced it with `$`
{% endhint %}

