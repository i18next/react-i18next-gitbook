# I18n and context

## Getting the t function

For getting the **t** function \(providing the translation functionality\) down to your component you got two options:

1. Using the [render prop](../components/i18n-render-prop.md)
2. Using the [hoc](../components/translate-hoc.md)

## Getting the i18n function into the flow

You got following options to pass the [i18next instance](../components/i18next-instance.md) to the hoc, render prop and trans component:

### Use the [provider](../components/i18nextprovider.md)

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

### Use the reactI18nextModule

```javascript
import i18n from 'i18next';
import { reactI18nextModule } from 'react-i18next';


i18n
  .use(reactI18nextModule) // if not using I18nextProvider
  .init({ /* options */ });
  
export default i18n;
```

### Use the internal context

```javascript
import { setI18n } from 'react-i18next';
import i18n from './i18n'; // initialized i18next instance

setI18n(i18n);
```

Details: [https://github.com/i18next/react-i18next/blob/master/src/context.js](https://github.com/i18next/react-i18next/blob/master/src/context.js)

