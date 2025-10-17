# IcuTrans Component



{% hint style="success" %}
🎉 Announcing [`i18next-cli`](https://github.com/i18next/i18next-cli):\
&#x20;       The New Official Toolkit for i18next.\
⇒ [Learn More](https://www.locize.com/blog/i18next-cli)
{% endhint %}

## Important note

This component is an alternative component to [`Trans`](./trans-component.md) which is designed for use as the internal implementation of [The `icu.macro` Babel macro](../misc/using-with-icu-format.md). Using it directly is possible, but not recommended.

While `<IcuTrans>` gives you a lot of power by letting you interpolate or translate complex React elements, the truth is: in most cases don't need this power.

**As long you have no React/HTML nodes integrated into a cohesive sentence** (text formatting like `strong`, `em`, link components, maybe others), **you won't need it** - most of the times you will be using the good old `t` function.

[IcuTrans props reference](https://react.i18next.com/latest/icu-trans-component#icutrans-props).

{% hint style="warning" %}
IcuTrans does ONLY interpolation. It does not rerender on language change or load any translations needed. Use [`useTranslation` hook](usetranslation-hook.md) or [`withTranslation` HOC](withtranslation-hoc.md) with `IcuTrans` to force a re-render on language changes.
{% endhint %}

```javascript
import React from 'react';
import { IcuTrans, useTranslation } from 'react-i18next'

function MyComponent() {
  // this will force a re-render when language changes or translation files are loaded
  const { t } = useTranslation('myNamespace');

  return <IcuTrans defaultTranslation="Hello World" content={[]} t={t}>Hello World</IcuTrans>;
}
```

{% hint style="info" %}
Have a look at the [i18next documentation](https://www.i18next.com) for details on the the `t` function:

* [essentials](https://www.i18next.com/translation-function/essentials.html)
* [interpolation](https://www.i18next.com/translation-function/interpolation.html)
* [formatting](https://www.i18next.com/translation-function/formatting.html)
* [plurals](https://www.i18next.com/translation-function/plurals.html)
{% endhint %}

## Samples

### Using with React components

For use cases where you need to embed React components into your translations, `IcuTrans` can be used.

This component enables you to nest any React content to be translated as one cohesive string. It supports both plural and interpolation. The `<Trans>` component will automatically use the most relevant `t()` function (from the [context instance](https://react.i18next.com/latest/i18nextprovider) or the global instance), unless overridden via the `i18n` or `t` props.

_Let's say you want to create following HTML output:_

> Hello **Arthur**, you have 42 unread messages. [Go to messages](../legacy-v9/trans-component.md).

**Before:** Your untranslated React code would have looked something like:

```javascript
function MyComponent({ person, messages }) {
  const { name } = person;
  const count = messages.length;

  return (
    <>
      Hello <strong title="This is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
    </>
  );
}
```

**After:** With the IcuTrans component change it to:

{% tabs %}
{% tab title="JavaScript" %}
```jsx
import { IcuTrans } from 'react-i18next';

function MyComponent({ person, messages }) {
  const { name } = person;
  const count = messages.length;

  return (
    <IcuTrans
      i18nKey="userMessagesUnread" // optional -> fallbacks to defaults if not provided
      defaultTranslation="hello <0>{{name}}</0>, you have {{count}} unread message. <1>Go to messages</1>."
      values={{ name, count }}
      content={[
        {
          type: "strong",
          props: {
            title: t('nameTitle'),
          },
        },
        {
          type: Link,
          props: {
            to: "/msgs",
          },
        },
      ]}
    />
  );
}
```
{% endtab %}

{% tab title="TypeScript" %}
```tsx
import { IcuTrans } from 'react-i18next';

function MyComponent({ person, messages }) {
  const { name } = person;
  const count = messages.length;

  return (
    <IcuTrans
      i18nKey="userMessagesUnread" // optional -> fallbacks to defaults if not provided
      defaultTranslation="hello <0>{{name}}</0>, you have {{count}} unread message. <1>Go to messages</1>."
      values={{ name, count }}
      content={[
        {
          type: "strong",
          props: {
            title: t(($) => $.nameTitle),
          },
        },
        {
          type: Link,
          props: {
            to: "/msgs",
          },
        },
      ]}
    />
  );
}
```
{% endtab %}
{% endtabs %}

_Your en.json (translation strings) will look like:_

```javascript
"nameTitle": "This is your name",
"userMessagesUnread_one": "Hello <1>{{name}}</1>, you have {{count}} unread message. <5>Go to message</5>.",
"userMessagesUnread_other": "Hello <1>{{name}}</1>, you have {{count}} unread messages.  <5>Go to messages</5>.",
```

## IcuTrans props

`defaultTranslation` and `contents` are required properties, all others are optional. You'll need to use `i18nKey` if you're not using natural language keys (text-based).

| _**name**_           | _**type (default)**_       | _**description**_                                                                                                                                                                                                                                                                                                                                                                                                                        |
| -------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `i18nKey`            | `string (undefined)`       | <p>If you prefer to use text as keys you can omit this, and the translation will be used as key. Can contain the namespace by prepending it in the form <code>'ns:key'</code> (depending on <code>i18next.options.nsSeparator</code>)</p><p>But this is not recommended when used in combination with natural language keys, better use the dedicated ns parameter: <code>&#x3C;Trans i18nKey="myKey" ns="myNS">&#x3C;/Trans></code></p> |
| `ns`                 | `string (undefined)`       | Namespace to use. May also be embedded in `i18nKey` but not recommended when used in combination with natural language keys, see above.                                                                                                                                                                                                                                                                                                  |
| `t`                  | `function (undefined)`     | `t` function to use instead of the global `i18next.t()` or the `t()` function provided by the nearest [provider](https://react.i18next.com/latest/i18nextprovider).                                                                                                                                                                                                                                                                      |
| `count`              | `integer (undefined)`      | Numeric value for pluralizable strings                                                                                                                                                                                                                                                                                                                                                                                                   |
| `context`            | `string (undefined)`       | Value used for the [context feature](https://www.i18next.com/translation-function/context).                                                                                                                                                                                                                                                                                                                                              |
| `tOptions`           | `object (undefined)`       | Extra options to pass to `t()` (e.g. `context`, `postProcessor`, ...)                                                                                                                                                                                                                                                                                                                                                                    |
| `parent`             | `node (undefined)`         | A component to wrap the content into (can be globally set on `i18next.init`). **Required for React < v16**                                                                                                                                                                                                                                                                                                                               |
| `i18n`               | `object (undefined)`       | i18next instance to use if not provided by context                                                                                                                                                                                                                                                                                                                                                                                       |
| `defaultTranslation` | `string`                   | Use this instead of using the children as default translation value (useful for ICU)                                                                                                                                                                                                                                                                                                                                                     |
| `values`             | `object (undefined)`       | Interpolation values                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `contents`           | `array[TypeDef]`           | Components to interpolate based on index of tag. This should be either `{ type: ComponentFunction }` or for built-in DOM elements `{ type: "a" }`, for example. `props` can be used to pass props to the component `{ type: "a", props: { href="/somewhere" } }`                                                                                                                                                                         |
| `shouldUnescape`     | `boolean (false)`          | HTML encoded tags like: `&lt; &amp; &gt;` should be unescaped, to become: `< & >`                                                                                                                                                                                                                                                                                                                                                        |

### i18next options

```javascript
i18next.init({
  // ...
  react: {
    // ...
    hashTransKey: function(defaultValue) {
      // return a key based on defaultValue or if you prefer to just remind you should set a key return false and throw an error
    },
    defaultTransParent: 'div', // a valid react element - required before react 16
    transEmptyNodeValue: '', // what to return for empty Trans
    transSupportBasicHtmlNodes: true, // allow <br/> and simple html elements in translations
    transKeepBasicHtmlNodesFor: ['br', 'strong', 'i'], // don't convert to <1></1> if simple react elements
    transWrapTextNodes: '', // Wrap text nodes in a user-specified element.
                            // i.e. set it to 'span'. By default, text nodes are not wrapped.
                            // Can be used to work around a well-known Google Translate issue with React apps. See: https://github.com/facebook/react/issues/11538
                            // (v11.10.0)
  }
});
```

{% hint style="warning" %}
Please be aware if you are using **React 15 or below**, you are required to set the `defaultTransParent` option, or pass a `parent` via props.
{% endhint %}

{% hint style="danger" %}
**Are you having trouble when your website is ran through Google Translate?**\
Google Translate seems to manipulate the DOM and makes React quite unhappy!\
**There's a work around:** you can wrap text nodes with`<span>` using `transWrapTextNodes: 'span'`.

_If you want to know more about the Google Translate issue with React, have a look at_ [_this_](https://github.com/facebook/react/issues/11538#issuecomment-390386520)_._
{% endhint %}
