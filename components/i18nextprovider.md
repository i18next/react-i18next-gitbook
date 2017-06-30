# I18nextProvider

The provider is responsible to pass the i18next instance down to all the translate hocs using react context.

```js
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

For the i18n instance have a look at the [i18n page](/components/i18next-instance.md).

### The i18nextProvider can use some props it gets passed:

options | default | description
--------|---------|-------------
i18n    | undefined   | pass i18next instance the provider will pass it down to translation components by context
initialI18nStore | undefined | pass in initial translations (useful for [serverside rendering](/misc/universal-rendering.md))
initialLanguage | undefined | pass in initial language (useful for [serverside rendering](/misc/universal-rendering.md))








