# withTranslation \(HOC\)

## What it does

The `withTranslation` is a classic HOC \(higher order component\) and gets the `t` function and `i18n` instance inside your component via props.

```jsx
import React from 'react';
import { withTranslation } from 'react-i18next';

function MyComponent({ t, i18n }) {
  return <p>{t('my translated text')}</p>
}

export default withTranslation()(MyComponent);
```

While you most time only need the t function to translate your content you also get the i18n instance to eg. change the language.

```javascript
i18n.changeLanguage('en-US');
```

{% hint style="info" %}
The `withTranslation` HOC will trigger a [Suspense](https://reactjs.org/docs/code-splitting.html#suspense) if not ready \(eg. pending load of translation files\). You can set `useSuspense` to false if prefer not using Suspense.
{% endhint %}

## When to use?

Use the `withTranslation` HOC to wrap **any component \(class or function\)** to access the translation function or i18n instance. 

## withTranslation params

### Loading namespaces

```javascript
// load a specific namespace
// the t function will be set to that namespace as default
withTranslation('ns1')(MyComponent);

// inside your component MyComponent
this.props.t('key'); // will be looked up from namespace ns1

// load multiple namespaces
// the t function will be set to first namespace as default
withTranslation(['ns1', 'ns2', 'ns3'])(MyComponent);

// inside your component MyComponent
this.props.t('key'); // will be looked up from namespace ns1
this.props.t('ns2:key'); // will be looked up from namespace ns2
```

### Overriding the i18next instance

```javascript
// passing in an i18n instance
// use only if you do not like the default instance
// set by i18next.use(initReactI18next) or the I18nextProvider
import i18n from './i18n';

const ExtendedComponent = withTranslation('ns1')(MyComponent);

<ExtendedComponent i18n={i18n} />
```

### Not using Suspense

```javascript
// use tReady prop in MyComponent to check if translations
// are already loaded or not
const ExtendedComponent = withTranslation()(MyComponent);

<ExtendedComponent useSuspense={false} />
```

## Problems

### Does not hoist non-react statics

Use [hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics) yourself:

```jsx
import React, { Component } from 'react';
import { withTranslation } from 'react-i18next';
import hoistStatics from 'hoist-non-react-statics';

class MyComponent extends Component {
  static ...
}

export default hoistStatics(withTranslation()(MyComponent), MyComponent);
```

Or simply hoist the one/two statics yourself:

```jsx
import React, { Component } from 'react';
import { withTranslation } from 'react-i18next';
import hoistStatics from 'hoist-non-react-statics';

class MyComponent extends Component {
  static ...
}

const Extended = withTranslation()(MyComponent);
Extended.static = MyComponent.static;

export default Extended;
```

