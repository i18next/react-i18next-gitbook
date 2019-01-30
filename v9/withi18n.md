# withI18n

{% hint style="info" %}
Was introduced in v8.0.0. Not available in older versions.
{% endhint %}

`withI18n` is a basic higher order component which passes mainly the `t` function down to the wrapped component.

```jsx
import React, { Component } from "react";
import { withI18n } from "react-i18next";

class MyComponent extends Component {
  render() {
    const { t } = this.props;

    return <h2>{t('Welcome to React')}</h2>;
  }
}
export default withI18n()(MyComponent);
```

{% hint style="danger" %}
This component won't trigger a rerender on language change. The passed down `t` function will be the same as calling `i18n.t` directly and not be bound to a specific namespace or even trigger a load of a namespace.

If you do **not pass in translations** on `i18n.init` we highly encourage using the [withNamespaces](withnamespaces.md) hoc.
{% endhint %}

