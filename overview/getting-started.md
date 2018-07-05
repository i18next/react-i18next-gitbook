# Getting started

## Installation

### Install using npm, bower

react-i18next can be added to your project using **npm** or **bower**:

```bash
# npm
$ npm install react-i18next --save

# bower
$ bower install react-i18next
```

The default export is UMD compatible \(commonjs, requirejs, global\).

In the `/dist` folder you find additional builds for `commonjs`, `es6 modules`.

{% hint style="info" %}
_Optimized to load react-i18next in webpack, rollup, ... The correct entry points are already configured in the package.json so there should be no extra setup to get the best build option._
{% endhint %}

### Load from CDN

You can also directly add a script tag loading i18next from one of the CDNs providing it:

**unpkg.com**

* [https://unpkg.com/react-i18next/react-i18next.js](https://unpkg.com/react-i18next/react-i18next.js)
* [https://unpkg.com/react-i18next/react-i18next.min.js](https://unpkg.com/react-i18next/react-i18next.min.js)

## Translation "how to"

You got two options to translate your content:

### Simple content

**Before:**

```javascript
<div>Just simple content</div>
```

**After:**

```javascript
<div>{t('simpleContent')}</div>
```

{% hint style="info" %}
You will get passed down the t function by using the [render prop](../components/i18n-render-prop.md) or [hoc](../components/translate-hoc.md).
{% endhint %}

### Complexer jsx tree

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
Learn more about the Trans Component [here](../components/trans-component.md)
{% endhint %}

## Basic sample 

### using render props

This basic sample uses render props and passes the[ i18next instance](../components/i18next-instance.md) to it via internal context handling by using the `reactI18nextModule` on i18next.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { I18n } from 'react-i18next';

import i18n from './i18n'; // initialized i18next instance using reactI18nextModule

class App extends React.Component {
  render() {
    return (
      <I18n>
        {
          (t, { i18n }) => (
            <div>
              <h1>{t('appName')}</h1>
              <button 
                onClick={() => { i18n.changeLanguage('de'); }}>{t('nav.linkDE')}
              </button>
              <button
                onClick={() => { i18n.changeLanguage('en'); }}>{t('nav.linkEN')}
              </button>
              <a
                href='https://github.com/i18next/react-i18next'
                target='_blank'
              >
                {t('nav:link1')}
              </a>
            </div>
          )
        }
      </I18n>
    )
  }
}

ReactDOM.render(<App />, document.getElementById('app'));
```

### using HOC and Provider

This basic sample uses the [I18nextProvider](../components/i18nextprovider.md) and the [translate hoc](../components/translate-hoc.md).

{% hint style="info" %}
_Instead of the provider you also just can use the reactI18nextModule._
{% endhint %}

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { I18nextProvider, translate } from 'react-i18next';

import i18n from './i18n'; // initialized i18next instance

@translate(['view', 'nav'], { wait: true })
class TranslatableView extends React.Component {
  render() {
    const { t } = this.props;

    const toggle = lng => i18n.changeLanguage(lng);

    return (
      <div>
        <h1>{t('appName')}</h1>
        <button 
          onClick={() => toggle('de')}>{t('nav.linkDE')}
        </button>
        <button
          onClick={() => toggle('en')}>{t('nav.linkEN')}
        </button>
        <a
          href='https://github.com/i18next/react-i18next'
          target='_blank'
        >
          {t('nav:link1')}
        </a>
      </div>
    )
  }
}

ReactDOM.render(
  <I18nextProvider i18n={ i18n }>
    <TranslatableView />
  </I18nextProvider>,
  document.getElementById('app')
);
```

## Extended samples

{% hint style="success" %}
For complete code and samples: [have a look at the samples \(react, react-native, nextjs](https://github.com/i18next/react-i18next/tree/master/example), ...\).

Or have a look at the interactive [codesandbox](https://codesandbox.io/s/l4qrory2nl).
{% endhint %}



