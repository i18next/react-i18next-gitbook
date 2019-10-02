# Migrating v9 to v10

v10 is a complete rewrite, taking the chance to clean up some complexity added from v1 to v9.

This means you will need to test your application more cautiously before release.

## New in v10

The most obvious change is the hook function for use inside functional components:

```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const [t, i18n] = useTranslation();

  return <p>{t('my translated text')}</p>
}
```

## Components without replacement

The Interpolation component (which was marked as deprecated for a long time and replaced by the Trans Component) was removed finally. You will need to replace it with the Trans Component.

## Migration

Replace your components like described below. If you don't have to use `Suspense` in your existing App you can set `useSuspense: false` in react.init options.react:

```javascript
i18n.init({
  react: {
    useSuspense: false
  }
});
```

## I18nextProvider changes

The `I18nextProvider` no longer provides as many properties as before. Make the necessary changes in your codebase after migrating.

```javascript
// New props
{
  i18n,
  defaultNS,
}

// Old props
{
  i18n,
  defaultNS,
  reportNS,
  lng: i18n && i18n.language,
  t: i18n && i18n.t.bind(i18n),
}
```

## Components v9 -&gt; v10

| Type | v9 | v10 |
| :--- | :--- | :--- |
| hook | - | useTranslation |
| HOC | withNamespaces | withTranslation |
| render prop | NamespacesConsumer | Translation |
| i18next plugin | reactI18nextModule | initReactI18next |
| Provider | I18nextProvider | I18nextProvider |
| Complex Translation | Trans | Trans |
| Interpolations | Interpolate | Trans |

