# Overview

react-i18next depends on i18next to provide the localization features. So there are two main things flowing through your render tree:

1. The [i18next instance](i18next-instance.md) \(short **i18n**\)
2. The [translation function](https://www.i18next.com/overview/api#t) \(short **t**\)

{% hint style="info" %}
While we primary rely on react context to pass down **i18n** and **t** the components are build to also accept those via props, options or in case of i18n via internal context / reactI18nextModule.
{% endhint %}

### Components

| Component | Props | Provides | Consumes |
| :--- | :--- | :--- | :--- |
| [I18nextProvider](i18nextprovider.md) | **i18n,** defaultNS | **i18n,** defaultNs |  |
| [I18n](i18n-render-prop.md) is a render prop |  | **t**, i18n | i18n |
| [Translate HOC](translate-hoc.md) |  | **t**, i18n | i18n |
| [Trans Component](trans-component.md) is used to translate complexer nodes where just using **t** is insufficient |  |  | **t**, **i18n** |

This means your tree will look something like this _\(assuming you use the options I18nextProvider\):_

{% hint style="info" %}
**I18nextProvider** --&gt; **I18n** or **Translate HOC** --&gt; **Trans** or using **t** in your component  
__
{% endhint %}

```javascript
import { I18nextProvider, I18n, Trans, translate } from 'react-i18next';
import i18n from `./i18n`; // the initialized i18next instance

export default function App () {
  return (
    <I18nextProvider i18n={i18n}>
      <I18n>
        {
          t => <h1>{t('key')}</h1>
        }
      </I18n>
      <MyComponentWithHoc />
    </I18nextProvider>
  )
}

function MyComponent({ t }) {
  return (
    <Trans i18nKey="userMessagesUnread" count={count}>
      Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
    </Trans>
  )
}

const MyComponentWithHoc = translate()(myComponent);
```

