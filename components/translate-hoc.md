# Translate HOC

The translate [hoc](https://reactjs.org/docs/higher-order-components.html) is responsible for passing the [**t** function](https://www.i18next.com/overview/api#t) to your component which enables all the translation functionality provided by i18next. Further, it asserts the component gets re-rendered on language change or changes to the translations themselves.

{% hint style="info" %}
To learn more about using the **t** function have a look at i18next's documentation:

* [essentials](https://www.i18next.com/essentials.html)
* [interpolation](https://www.i18next.com/interpolation.html)
* [formatting](https://www.i18next.com/formatting.html)
* [plurals](https://www.i18next.com/plurals.html)
* ...
{% endhint %}

Can be nested inside a [I18nextProvider](i18nextprovider.md). If not, you will need to pass the i18next instance via prop `i18n`, in options or by using `setI18n` function \(see below\).

## Sample

```javascript
import React from 'react';
import { translate } from 'react-i18next';

function TranslatableView(props) {
  const { t, tReady } = props;
  // tReady is true if translations were loaded.
  // Use wait option to not render before loaded
  // or render placeholder yourself if not tReady=false

  return (
    <div>
      <h1>{t('keyFromDefault')}</h1>
      <p>{t('anotherNamespace:key.from.another.namespace', { /* options t options */ })}</p>
    </div>
  )
}

export default translate(['defaultNamespace', 'anotherNamespace'])(TranslatableView);

// short for only loading one namespace:
export default translate('defaultNamespace')(TranslatableView);

// short for using defaultNS of i18next
export default translate()(TranslatableView);

// using a function to return namespaces based on props
export default translate((props) => props.namespaces)(TranslatableView);
```

## Using setI18n instead of the i18nextProvider

You can set the i18n instance using the setI18n function to avoid using the i18nextProvider:

```javascript
import { translate } from 'react-i18next';
import i18n from './i18n';

translate.setI18n(i18n);
```

## Set defaults for all your translate hoc components

Below you see how to pass options for one hoc. But most time you like to change those values for every component.

So there are two options:

### a\) Set those on i18next init:

```javascript
i18next.init({
  // ... other options
  react: {
    wait: false,
    withRef: false,
    bindI18n: 'languageChanged loaded',
    bindStore: 'added removed',
    nsMode: 'default'
  }
});
```

### b\) Use the setDefaults function:

```javascript
import { translate } from 'react-i18next';

translate.setDefaults({
  wait: false,
  withRef: false,
  bindI18n: 'languageChanged loaded',
  bindStore: 'added removed',
  nsMode: 'default'
});
```

## The translate hoc options:

```javascript
export default translate(
  'defaultNamespace',
  { wait: true } // options
)(TranslatableView);
```

| _**option**_ | _**type \(default\)**_ | _**description**_ |
| --- | --- | --- |
| wait | boolean \(false\) | assert all provided namespaces are loaded before rendering the component \(can be set [globally](i18next-instance.md) too\) |
| nsMode | string \('default'\) | _default:_ namespaces will be loaded an the first will be set as default or _fallback:_ namespaces will be used as fallbacks used in order provided |
| bindI18n | string \('languageChanged loaded'\) | which events trigger a rerender, can be set to false or string of events |
| bindStore | string \('added removed'\) | which events on store trigger a rerender, can be set to false or string of events |
| omitBoundRerenders | boolean \(true\) | Does not trigger rerenders while state not ready - avoiding unneeded renders on init |
| withRef | boolean \(false\) | store a ref to the wrapped component and access it by `Component. getWrappedInstance();` |
| i18n | object \(undefined\) | pass i18next via options \(useful for [next.js usage](https://github.com/i18next/react-i18next/tree/master/example/nextjs) |
| usePureComponent | boolean \(false\) | use shallowEqual on props change if set to true |

## The translate hoc props:

| _**name**_ | _**type \(default\)**_ | _**description**_ |
| --- | --- | --- |
| i18n | object \(undefined\) | pass i18next instance by props instead of having it on context |
| initialI18nStore | object \(undefined\) | pass in initial translations \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |
| initialLanguage | object \(undefined\) | pass in initial language \(useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29) |

