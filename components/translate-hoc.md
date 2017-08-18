# Translate HOC

The translate hoc is responsible to pass the `t` function to your component which enables all the translation functionality provide by i18next. Further it asserts the component gets rerendered on language change or changes to the translations themself.

To learn more about using the `t` function have a look at i18next documentation:

- [essentials](https://www.i18next.com/essentials.html)
- [interpolation](https://www.i18next.com/interpolation.html)
- [formatting](https://www.i18next.com/formatting.html)
- [plurals](https://www.i18next.com/plurals.html)
- ...

**Important:** needs to be nested inside a [I18nextProvider](/components/i18nextprovider.md) or you will need to pass the i18next instance via prop i18n.

```js
import React from 'react';
import { translate } from 'react-i18next';

function TranslatableView(props) {
  const { t } = props;

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


```

### Set defaults for all your translate hoc components

Below you see how to pass options for one hoc. But most time you like to change those values for every component.

So there are two options:

#### a) Set those on i18next init:

```js
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

You can set every option excluding the `translateFuncName` as we need that before creating an instance to set the childContextTypes.

#### b) Use the setDefaults function:

```js
import translate from 'react-i18next';

translate.setDefaults({
  wait: false,
  withRef: false,
  bindI18n: 'languageChanged loaded',
  bindStore: 'added removed',
  nsMode: 'default',
  translateFuncName: 't'
});
```

Here you can also set the default for `translateFuncName` property.


### The translate hoc can take a few options:

```js
export default translate('defaultNamespace', { wait: true })(TranslatableView);
```

options | default | description
--------|---------|-------------
wait    | false   | assert all provided namespaces are loaded before rendering the component (can be set [globally](/components/i18next-instance.md) too)
nsMode  | 'default' | *default:* namespaces will be loaded an the first will be set as default or *fallback:* namespaces will be used as fallbacks used in order provided
bindI18n | 'languageChanged loaded' | which events trigger a rerender, can be set to false or string of events
bindStore | 'added removed' | which events on store trigger a rerender, can be set to false or string of events
withRef | false | store a ref to the wrapped component and access it by `decoratedComponent.getWrappedInstance();`
translateFuncName | 't' | name for the t function added to props
i18n | undefined | pass i18next via options (useful for [next.js usage](https://github.com/i18next/react-i18next/tree/master/example/nextjs)

### The translate hoc can use some props it gets passed:

options | default | description
--------|---------|-------------
i18n    | undefined   | pass i18next instance by props instead of having it on context
initialI18nStore | undefined | pass in initial translations (useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29)
initialLanguage | undefined | pass in initial language (useful for [next.js usage](https://github.com/i18next/react-i18next/blob/master/example/nextjs/pages/index.js#L29)







