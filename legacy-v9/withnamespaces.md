# withNamespaces (v9)

{% hint style="info" %}
Was introduced in v8.0.0. Not available in older versions.
{% endhint %}

The withNamespaces [hoc](https://reactjs.org/docs/higher-order-components.html) is responsible for passing the [**t** function](https://www.i18next.com/overview/api#t) to your component. It enables all the translation functionality provided by i18next. Further, it asserts your component gets re-rendered on language change or changes to the translation catalog itself (loaded translations).

```javascript
withNamespaces(namespaces, options)(MyComponent);
```

{% hint style="info" %}
To learn more about using the **t** function have a look at i18next documentation:

* [essentials](https://www.i18next.com/essentials.html)
* [interpolation](https://www.i18next.com/interpolation.html)
* [formatting](https://www.i18next.com/formatting.html)
* [plurals](https://www.i18next.com/plurals.html)
* ...
{% endhint %}

## Sample

```javascript
import React from 'react';
import { withNamespaces } from 'react-i18next';

function TranslatableView(props) {
  const { t, tReady } = props;
  // tReady is true if translations were loaded.
  // Use wait option to not render before loaded
  // or render placeholder yourself if not tReady=false

  return (
    <div>
      <h1>{t('keyFromDefault')}</h1>
      <p>{t('anotherNamespace:key.from.another.namespace', { /* options t options */ })}</p>
    </div>
  )
}

export default withNamespaces([
  'defaultNamespace',
  'anotherNamespace'
], { /* additional options see below */ })(TranslatableView);

// or: short for only loading one namespace:
export default withNamespaces('defaultNamespace')(TranslatableView);

// or: short for using defaultNS defined in i18next
export default withNamespaces()(TranslatableView);

// or: using a function to return namespaces based on props
export default withNamespaces((props) => props.namespaces)(TranslatableView);
```

If not using the [reactI18nextModule](i18next-instance.md) this hoc should be nested inside a [I18nextProvider](i18nextprovider.md). Alternatively you can pass the i18next instance via prop `i18n`.

## Using with TypeScript

To help you using TypeScript and the @withNamespaces decorator here is a trival example:

```jsx
import * as React from 'react';
import { withNamespaces, WithNamespaces } from 'react-i18next';

class MyComponent extends React.PureComponent<WithNamespaces> {
  public render() {
    const { t } = this.props;
    return (
      <React.Fragment>
        <span>{t('my-component-content')}</span>
      </React.Fragment>
    );
  }
}
export default withNamespaces()(MyComponent);
```



## Set defaults for all used withNamespaces

Most time you like to change those values for every component.

Set those on i18next init:

```javascript
i18n.init({
  // ... other options
  react: {
    wait: false,
    withRef: false,
    bindI18n: 'languageChanged loaded',
    bindStore: 'added removed',
    nsMode: 'default'
  }
});
```

## withNamespaces options:

```javascript
export default withNamespaces(
  'defaultNamespace',
  { wait: true } // <-- options
)(TranslatableView);
```

| _**option**_       | _**type (default)**_              | _**description**_                                                                                                                                                                                                                                                     |
| ------------------ | --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **wait**           | boolean (false)                   | <p>assert all provided namespaces are loaded before rendering the component (can be set <a href="i18next-instance.md">globally</a> too)</p><p></p><p><strong>In most cases you like to set this to true.</strong> If not handling not ready by evaluating tReady.</p> |
| nsMode             | string ('default')                | <p><em>default:</em> namespaces will be loaded an the first will be set as default or</p><p></p><p><em>fallback:</em> namespaces will be used as fallbacks used in order provided</p>                                                                                 |
| innerRef           | function or object (undefined)    | <p>either pass in a object React.createRef or a ref function like (c) => this.myRef = c;</p><p></p><p><a href="https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509">read more</a></p>                                                                    |
| bindI18n           | string ('languageChanged loaded') | which events trigger a rerender, can be set to false or string of events                                                                                                                                                                                              |
| bindStore          | string ('added removed')          | which events on store trigger a rerender, can be set to false or string of events                                                                                                                                                                                     |
| omitBoundRerenders | boolean (true)                    | Does not trigger rerenders while state not ready - avoiding unneeded renders on init                                                                                                                                                                                  |
| i18n               | object (undefined)                | pass i18next via options (useful for [next.js usage](https://github.com/i18next/react-i18next/tree/v9.x.x/example/nextjs)                                                                                                                                             |
| usePureComponent   | boolean (false)                   | use shallowEqual on props change if set to true                                                                                                                                                                                                                       |

## withNamespaces props:

| _**name**_       | _**type (default)**_ | _**description**_                                                                                                              |
| ---------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| i18n             | object (undefined)   | pass i18next instance by props instead of having it on context                                                                 |
| initialI18nStore | object (undefined)   | pass in initial translations (useful for [next.js usage](https://github.com/i18next/react-i18next/tree/v9.x.x/example/nextjs)) |
| initialLanguage  | object (undefined)   | pass in initial language (useful for [next.js usage](https://github.com/i18next/react-i18next/tree/v9.x.x/example/nextjs))     |
