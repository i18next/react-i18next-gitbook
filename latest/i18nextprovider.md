# I18nextProvider

## What it does

The I18nextProvider does take an i18next instance via prop i18n and passes that down using the context API.

```jsx
import { I18nextProvider } from 'react-i18next';
import i18n from './i18n';
import App from './App';

<I18nextProvider i18n={i18n} defaultNS={'translation'}>
  <App />
</I18nextProvider>
```

## When to use?

You will need to use the provider if you need to support multiple i18next instances - eg. if you provide a component library ([like this example](https://github.com/i18next/react-i18next/tree/master/example/react-component-lib)) or in scenarios for [SSR (ServerSideRendering)](ssr.md). Additionally, you have the ability to manage the default namespace(s) by passing defaultNS.

## I18nextProvider props

| _**name**_    | **type (**_**default)**_       | _**description**_                                                                         |
| ------------- | ------------------------------ | ----------------------------------------------------------------------------------------- |
| **i18n**      | object (undefined)             | pass i18next instance the provider will pass it down to translation components by context |
| **defaultNS** | string \| string[] (undefined) | pass defaultNS to manage the default namespace(s)                                         |
