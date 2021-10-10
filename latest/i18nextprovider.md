# I18nextProvider

## What it does

The I18nextProvider does take an i18next instance via prop i18n and passes that down using the context API.

```jsx
import { I18nextProvider } from 'react-i18next';
import i18n from './i18n';
import App from './App';

<I18nextProvider i18n={i18n}>
  <App />
</I18nextProvider>
```

## When to use?

You will only need to use the provider in scenarios for SSR (ServerSideRendering) or if you need to support multiple i18next instances - eg. if you provide a component library. 

## I18nextProvider props

| _**name**_ | **type (**_**default)**_ | _**description**_                                                                         |
| ---------- | ------------------------ | ----------------------------------------------------------------------------------------- |
| **i18n**   | object (undefined)       | pass i18next instance the provider will pass it down to translation components by context |
