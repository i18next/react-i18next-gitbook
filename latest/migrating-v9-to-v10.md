# Migrating v9 to v10

v10 is a complete rewrite taking the chance to clean up some complexity added from v1 to v9.

This means you will need to test your application more cautious before release.

## New in v10

Most obvious the hook function to use inside functional components:

```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

export function MyComponent() {
  const [t, i18n] = useTranslation();
  
  return <p>{t('my translated text')}</p>
}
```

## Components without replacement

The Interpolation which was for a long time marked as deprecated and replaced by the Trans Component was removed finally. So you will need to replace it with the Trans Component.

## Migration

Replace your components like described below. Not having to use `Suspense` in your existing App you can set `useSuspense: false` in react.init options.react:

```javascript
i18n.init({
  react: {
    useSuspense: false
  }
});
```

## I18nextProvider changes

The `I18nextProvider` does no longer provides as much properties as before. So please make the necessary changes in your codebase after migrating.

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

