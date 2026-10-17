---
description: "IcuTrans, the Trans variant for ICU message format (i18next-icu): nest React components in an ICU message, the props reference and Server Component usage."
---

# IcuTrans Component

## Important note

`IcuTrans` is the [`Trans`](trans-component.md) counterpart for the [ICU message format](../misc/using-with-icu-format.md). Use it when your i18next instance uses [i18next-icu](https://github.com/i18next/i18next-icu) and a translation contains React elements (text formatting like `strong`, `em`, link components) that belong to one cohesive sentence.

`Trans` builds its default string from JSX children, and JSX reserves `{curly brackets}` for expressions, so ICU syntax like `{count, plural, ...}` cannot be written there. `IcuTrans` takes the message as a plain string instead (`defaultTranslation`) and a list of component declarations (`content`) that replace the numbered tags `<0>`, `<1>`, ... in it.

**As long as you have no React/HTML nodes integrated into a cohesive sentence, you won't need it**: most of the time you will be using the good old `t` function.

{% hint style="info" %}
`IcuTrans` replaces the deprecated [`icu.macro` Babel macro](../misc/using-with-icu-format.md#using-babel-macros-trans-plural-select-deprecated), which compiled JSX into `IcuTrans` elements. Write `IcuTrans` directly instead.
{% endhint %}

[IcuTrans props reference](icu-trans-component.md#icutrans-props).

{% hint style="warning" %}
IcuTrans does ONLY interpolation. It does not rerender on language change or load any translations needed. Use [`useTranslation` hook](usetranslation-hook.md) or [`withTranslation` HOC](withtranslation-hoc.md) with `IcuTrans` to force a re-render on language changes.
{% endhint %}

```javascript
import React from 'react';
import { IcuTrans, useTranslation } from 'react-i18next';

function MyComponent() {
  // this will force a re-render when language changes or translation files are loaded
  const { t } = useTranslation('myNamespace');

  return <IcuTrans i18nKey="helloWorld" defaultTranslation="Hello World" content={[]} t={t} />;
}
```

{% hint style="info" %}
Have a look at the [i18next-icu](https://github.com/i18next/i18next-icu) README and the [ICU message format reference](https://formatjs.github.io/docs/core-concepts/icu-syntax) for the message syntax (arguments, `plural`, `select`, `selectordinal`, number and date formats).
{% endhint %}

## Samples

### Using with React components

This component enables you to nest any React content to be translated as one cohesive string. Plurals, selects and formatting go into the ICU message itself. `IcuTrans` uses the most relevant `t()` function (from the [context instance](i18nextprovider.md) or the global instance), unless overridden via the `i18n` or `t` props.

_Let's say you want to create following HTML output:_

> Hello **Arthur**, you have 42 unread messages. [Go to messages](icu-trans-component.md).

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
import { IcuTrans, useTranslation } from 'react-i18next';

function MyComponent({ person, messages }) {
  const { t } = useTranslation();
  const { name } = person;
  const count = messages.length;

  return (
    <IcuTrans
      i18nKey="userMessagesUnread"
      defaultTranslation="Hello <0>{name}</0>, you have {count, plural, one {# unread message} other {# unread messages}}. <1>Go to messages</1>."
      values={{ name, count }}
      content={[
        { type: 'strong', props: { title: t('nameTitle') } },
        { type: Link, props: { to: '/msgs' } },
      ]}
      t={t}
    />
  );
}
```
{% endtab %}

{% tab title="TypeScript" %}
```tsx
import { IcuTrans, useTranslation } from 'react-i18next';

function MyComponent({ person, messages }) {
  const { t } = useTranslation();
  const { name } = person;
  const count = messages.length;

  return (
    <IcuTrans
      i18nKey="userMessagesUnread"
      defaultTranslation="Hello <0>{name}</0>, you have {count, plural, one {# unread message} other {# unread messages}}. <1>Go to messages</1>."
      values={{ name, count }}
      content={[
        { type: 'strong', props: { title: t(($) => $.nameTitle) } },
        { type: Link, props: { to: '/msgs' } },
      ]}
      t={t}
    />
  );
}
```
{% endtab %}
{% endtabs %}

_Your en.json (translation strings) will look like:_

```json
{
  "nameTitle": "This is your name",
  "userMessagesUnread": "Hello <0>{name}</0>, you have {count, plural, one {# unread message} other {# unread messages}}. <1>Go to messages</1>."
}
```

_Result with `count = 42`:_

```html
Hello <strong title="This is your name">Arthur</strong>, you have 42 unread messages. <a href="/msgs">Go to messages</a>.
```

The index of a declaration in `content` is the number of its tag: `content[0]` replaces `<0>...</0>`, `content[1]` replaces `<1>...</1>`. The text between the tags becomes the children of that element. There is one key per message: the plural forms live inside the ICU message, not in `_one`/`_other` suffixed keys.

### Nested elements

Declarations can have their own children declarations. Tags inside a tag are numbered again from `0` and refer to `props.children` of the outer declaration:

```jsx
<IcuTrans
  i18nKey="features"
  defaultTranslation="Features: <0><0>Fast</0><1>Reliable</1></0>"
  content={[
    {
      type: 'ul',
      props: {
        children: [{ type: 'li' }, { type: 'li' }],
      },
    },
  ]}
/>

// result
Features: <ul><li>Fast</li><li>Reliable</li></ul>
```

HTML entities in the translation (`&amp;`, `&lt;`, `&nbsp;`, `&copy;`, numeric entities, ...) are decoded to their characters. `IcuTrans` renders a fragment, without a wrapping element.

### Usage in React Server Components (v17.0.21)

`IcuTrans` reads the i18next instance from React context, which Server Components do not have. In a Server Component, import `IcuTransWithoutContext` from its own entry point and pass `t` and `i18n` explicitly:

```jsx
// app/[lng]/page.jsx (Server Component), here with next-i18next's getT
import { getT } from 'next-i18next/server';
import { IcuTransWithoutContext } from 'react-i18next/IcuTransWithoutContext';

export default async function Page() {
  const { t, i18n } = await getT('common');

  return (
    <IcuTransWithoutContext
      t={t}
      i18n={i18n}
      i18nKey="welcome"
      defaultTranslation="Welcome, <0>{name}</0>!"
      content={[{ type: 'strong' }]}
      values={{ name: 'Arthur' }}
    />
  );
}
```

The `react-i18next/IcuTransWithoutContext` entry point does not create a React context when it is imported, the same way `react-i18next/TransWithoutContext` works for `Trans`. Importing from the main `react-i18next` entry point in a Server Component can fail with `createContext is not a function`, depending on how far your bundler tree-shakes the package.

## IcuTrans props

`defaultTranslation` and `content` are required properties, all others are optional. `IcuTrans` and `IcuTransWithoutContext` take the same props.

| _**name**_           | _**type (default)**_    | _**description**_ |
| -------------------- | ----------------------- | ----------------- |
| `i18nKey`            | `string (undefined)`    | The key to look up. If omitted, `defaultTranslation` is used as the key (natural language keys). Can contain the namespace in the form `'ns:key'` (depending on `i18next.options.nsSeparator`), but the dedicated `ns` prop is clearer. The TypeScript types currently require this prop. |
| `defaultTranslation` | `string`                | The message in ICU format with numbered tags (`<0>...</0>`). Used when the key has no translation yet, and as the key when `i18nKey` is omitted. |
| `content`            | `array`                 | Component declarations, one per numbered tag: `{ type, props }`, where `type` is a DOM element name (`'strong'`, `'a'`) or a component (`Link`), and the optional `props` are passed to it, for example `{ type: 'a', props: { href: '/somewhere' } }`. For tags nested inside a tag, put their declarations in `props.children`, see [Nested elements](icu-trans-component.md#nested-elements). |
| `values`             | `object ({})`           | Values for the arguments of the ICU message, including the number for `plural` and `selectordinal`, for example `values={{ name, count }}`. These override the same names in `interpolation.defaultVariables`. |
| `ns`                 | `string \| string[] (undefined)` | Namespace to use. Falls back to the namespace of `t`, then the `defaultNS` of the [provider](i18nextprovider.md), then `i18next.options.defaultNS`. |
| `t`                  | `function (undefined)`  | `t` function to use instead of `i18n.t()`, for example the one returned by [`useTranslation`](usetranslation-hook.md). |
| `i18n`               | `object (undefined)`    | i18next instance to use. Falls back to the instance of the nearest [provider](i18nextprovider.md) (`IcuTrans` only), then to the global instance set by `initReactI18next`. |

Unlike `Trans`, there are no `count`, `context`, `tOptions`, `parent`, `shouldUnescape` or `components` props, and children are ignored. Put plurals and selects into the ICU message and pass their values in `values`. The `react` options for `Trans` in `i18next.init` (`transWrapTextNodes`, `transKeepBasicHtmlNodesFor`, `defaultTransParent`, ...) do not apply to `IcuTrans`.
