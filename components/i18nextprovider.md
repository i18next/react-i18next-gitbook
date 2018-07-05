# I18nextProvider

The provider is responsible to pass the i18next instance passed in by props down to all the [translate hocs](translate-hoc.md) or [I18n](i18n-render-prop.md) using react context.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { I18nextProvider } from 'react-i18next';

import App from './App'; // your entry page
import i18n from './i18n'; // initialized i18next instance

ReactDOM.render(
  <I18nextProvider i18n={ i18n }>
    <App />
  </I18nextProvider>,
  document.getElementById('app')
);
```

For the i18n instance have a look at the [i18next instance page](i18next-instance.md).

## The I18nextProvider props:

| _**name**_ | **type \(**_**default\)**_ | _**description**_ |
| --- | --- | --- |
| **i18n** | object \(undefined\) | pass i18next instance the provider will pass it down to translation components by context |
| initialI18nStore | object \(undefined\) | pass in initial translations \(useful for [serverside rendering](../misc/serverside-rendering.md)\) |
| initialLanguage | string \(undefined\) | pass in initial language \(useful for [serverside rendering](../misc/serverside-rendering.md)\) |

