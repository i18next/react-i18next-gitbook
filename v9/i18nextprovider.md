# I18nextProvider

The provider is responsible to pass the i18next instance passed in by props down to all the [withNamespaces](withnamespaces.md) hocs or [NamespacesConsumer](namespacesconsumer.md) render prop using react context api.

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

As an alternative you can use the [reactI18nextModule](i18next-instance.md) in the i18n instance.

## The I18nextProvider props:

| _**name**_ | **type \(**_**default\)**_ | _**description**_ |
| :--- | :--- | :--- |
| **i18n** | object \(undefined\) | pass i18next instance the provider will pass it down to translation components by context |
| defaultNS | string \(undefined\) | optionally pass down a default namespace to your translate HOC, I18n render prop \(without having to specify it there\) |
| initialI18nStore | object \(undefined\) | pass in initial translations \(useful for [serverside rendering](serverside-rendering.md)\) |
| initialLanguage | string \(undefined\) | pass in initial language \(useful for [serverside rendering](serverside-rendering.md)\) |

