# Drawbacks of other i18n solutions

Let's make the sample using our own base i18n framework [i18next](https://i18next.com). Like all other solutions, some come [drawbacks](the-drawbacks-of-other-i18n-solutions.md#the-drawbacks). These will be highlighted after samples.

## Using a pure javascript i18n framework

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import i18n from "i18next";

// translation catalog
const resources = {
  en: {
    translation: {
      "welcome": "Welcome to React and react-i18next"
    }
  }
};

// initialize i18next with catalog and language to use
i18n.init({
  resources,
  lng: "en"
});

class App extends Component {
  render() {
    return <h2>{i18n.t('welcome')}</h2>;
  }
}

// append app to dom
ReactDOM.render(
  <App />,
  document.getElementById("root")
);
```

## More react adapted "react-i18n"

The above is basically how every i18n framework for react works. The translations and language get set when initiated and a translation function is made available. You could easily extend this hiding the i18n.init inside a provider and pass down the function by context to another component to translate strings.

So let's make this more visible with some pseudo code:

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import { I18nProvider, FormattedString } from "i18nLib";

// import translation catalog
import resources from './catalog-en.json';

class App extends Component {
  render() {
    return <h2><FormattedString msg="welcome" /></h2>;
  }
}

// append app to dom
ReactDOM.render(
  <I18nProvider lng="en" resources={resources}>
    <App />,
  </I18nProvider>
  document.getElementById("root")
);
```

## The drawbacks

Before we come to the drawbacks let's highlight some advantage of those solutions above - they are very simple to get started.

### Changing the language

Can you easily change the language? Get the translations in other language loaded? Does the language change trigger a rerender?

That's what the [withTranslation](../latest/withtranslation-hoc.md) higher order component or [useTranslation](../latest/usetranslation-hook.md) hook do!

### Scale and split your translations into multiple files

When your project gets bigger you do not only want code splitting but you also like to load translations on demand to avoid loading all translations upfront which would result in bad load times for your website.

With loading translations asynchronous there comes another problem - does your framework handle the pending state during loading translation?

That's what the [withTranslation](../latest/withtranslation-hoc.md) higher order component or [useTranslation](../latest/usetranslation-hook.md) hook do!

### Can you translate combined jsx nodes in one sentence

Let's take following content:

```javascript
<p>
  Hello <strong>{name}</strong>, you have 
  <Link to="/msgs">{count} unread message(s)</Link>.
</p>
```

In most frameworks you will end having to split this into multiple translation strings. But for your translators it would make sense to have this as one sentence to translate like eg.:

```text
Hello <1>{name}</1>, you have <3>{count} unread message(s)</3>.
```

You can do this using the [Trans component](../latest/trans-component.md).

