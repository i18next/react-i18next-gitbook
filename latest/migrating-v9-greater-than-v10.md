# Migrating v9 -&gt; v10

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

