# Getting started

## Installation

### Install using npm

react-i18next can be added to your project using **npm**:

```bash
# npm
$ npm install react-i18next --save
```

The default export is UMD compatible \(commonjs, requirejs, global\).

In the `/dist` folder you find additional builds for `commonjs`, `es6 modules`.

{% hint style="info" %}
The module is optimized to load by webpack, rollup, ... The correct entry points are already configured in the package.json. There should be no extra setup needed to get the best build option.
{% endhint %}

### Load from CDN

You can also add a script tag to load react-i18next from one of the CDNs providing it, eg.:

**unpkg.com**

* [https://unpkg.com/react-i18next/react-i18next.js](https://unpkg.com/react-i18next/react-i18next.js)
* [https://unpkg.com/react-i18next/react-i18next.min.js](https://unpkg.com/react-i18next/react-i18next.min.js)

## Translation "how to"

{% hint style="info" %}
You should read the [i18next](https://www.i18next.com) documentation at some point as we do not repeat all the [configuration options](https://www.i18next.com/overview/configuration-options) and translation functionalities like [plurals](https://www.i18next.com/translation-function/plurals), [formatting](https://www.i18next.com/translation-function/formatting), [interpolation](https://www.i18next.com/translation-function/interpolation), ... here.
{% endhint %}

You got two options to translate your content:

### Simple content

Simple content can easily be translated using the provided `t` function.

**Before:**

```javascript
<div>Just simple content</div>
```

**After:**

```javascript
<div>{t('simpleContent')}</div>
```

{% hint style="info" %}
You will get the t function by using the [NamespacesConsumer](components/namespacesconsumer.md) render prop or [withNamespaces](components/withnamespaces.md) hoc.
{% endhint %}

### JSX tree

Sometimes you might want to include html formatting or components like links into your translations. \(Always try to get the best result for your translators - the final string to translate should be a complete sentence\).

**Before:** Your react code would have looked something like:

```javascript
<div>
  Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
</div>
```

**After:** With the trans component just change it to:

```javascript
<Trans i18nKey="userMessagesUnread" count={count}>
  Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

{% hint style="info" %}
Learn more about the Trans Component [here](components/trans-component.md)
{% endhint %}

## Basic sample 

This basic sample tries to add i18n in a one file sample.

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import i18n from "i18next";
import { withI18n, reactI18nextModule } from "react-i18next";

i18n
  .use(reactI18nextModule) // passes i18n down to react-i18next
  .init({
    resources: {
      en: {
        translation: {
          "Welcome to React": "Welcome to React and react-i18next"
        }
      }
    },
    lng: "en",
    fallbackLng: "en",

    interpolation: {
      escapeValue: false
    }
  });

class App extends Component {
  render() {
    const { t } = this.props;

    return <h2>{t('Welcome to React')}</h2>;
  }
}
const AppWithI18n = withI18n()(App); // pass `t` function to App

// append app to dom
ReactDOM.render(
  <AppWithI18n />,
  document.getElementById("root")
);
```

#### RESULT:

![Preview of content](.gitbook/assets/screen-shot-2018-09-30-at-16.58.18.png)

{% hint style="info" %}
This sample while very simple does come with some [drawbacks](guides/the-drawbacks-of-other-i18n-solutions.md) to getting the full potential from using react-i18next you should read the extended [step by step guide](guides/step-by-step-guide.md).
{% endhint %}

## Extended samples

{% hint style="success" %}
For complete code and samples: [have a look at the samples \(react, react-native, nextjs](https://github.com/i18next/react-i18next/tree/master/example), ...\).

Or have a look at the interactive [codesandbox](https://codesandbox.io/s/l4qrory2nl).
{% endhint %}



