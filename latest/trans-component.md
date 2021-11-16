# Trans Component

## Important note

While `<Trans>` gives you a lot of power by letting you interpolate or translate complex React elements, the truth is: in most cases you don't even need it.

**As long you have no React/HTML nodes integrated into a cohesive sentence** (text formatting like `strong`, `em`, link components, maybe others), **you won't need it** - most of the times you will be using the good old `t` function.

You may be looking directly for the [Trans props](https://react.i18next.com/latest/trans-component#trans-props).

{% hint style="warning" %}
It does ONLY interpolation. It does not rerender on language change or load any translations needed. Check [`useTranslation` hook](usetranslation-hook.md) or [`withTranslation` HOC](withtranslation-hoc.md) for those cases.
{% endhint %}

```javascript
import React from 'react';
import { Trans, useTranslation } from 'react-i18next'

function MyComponent() {
  const { t } = useTranslation('myNamespace');

  return <Trans t={t}>Hello World</Trans>;
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

So you learned there is no need to use the Trans component everywhere (the plain `t` function will just do fine in most cases).

This component enables you to nest any React content to be translated as one cohesive string. It supports both plural and interpolation.

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

**After:** With the Trans component just change it to:

```javascript
import { Trans } from 'react-i18next';

function MyComponent({ person, messages }) {
  const { name } = person;
  const count = messages.length;

  return (
    <Trans i18nKey="userMessagesUnread" count={count}>
      Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
    </Trans>
  );
}
```

_Your en.json (translation strings) will look like:_

```javascript
"nameTitle": "This is your name",
"userMessagesUnread": "Hello <1>{{name}}</1>, you have {{count}} unread message. <5>Go to message</5>.",
"userMessagesUnread_plural": "Hello <1>{{name}}</1>, you have {{count}} unread messages.  <5>Go to messages</5>.",
```

{% hint style="info" %}
[**saveMissing**](https://www.i18next.com/overview/configuration-options#missing-keys) will send a valid `defaultValue` based on the component children.\
Also, The `i18nKey` is optional, in case you already use text as translation keys.
{% endhint %}

### Alternative usage which lists the components (v11.6.0)

```javascript
<Trans
  i18nKey="myKey" // optional -> fallbacks to defaults if not provided
  defaults="hello <italic>beautiful</italic> <bold>{{what}}</bold>" // optional defaultValue
  values={{ what: 'world'}}
  components={{ italic: <i />, bold: <strong /> }}
/>
```

This format is useful if you want to interpolate the same node multiple times. Another advantage is the simpler named tags, which avoids the trouble with index guessing - however, this can also be achieved with `transSupportBasicHtmlNodes`, see the next section.

{% hint style="warning" %}
Existing self-closing HTML tag names are reserved keys and won't work. Examples: `link: <Link />`, `img: <img src="" />`, `media: <img src="" />`
{% endhint %}

### Usage with simple HTML elements like \<br /> and others (v10.4.0)

There are two options that allow you to have basic HTML tags inside your translations, instead of numeric indexes. However, this only works for elements without additional attributes (like `className`), having none or a single text children.

Examples of elements that will be readable in translation strings:

* `<br/>`
* `<strong>bold</strong>`
* `<p>some paragraph</p>`

Examples that will be converted to indexed nodes:

* `<i className="icon-gear" />`: no attributes allowed
* `<strong title="something">{{name}}</strong>`: only text nodes allowed
* `<b>bold <i>italic</i></b>`: no nested elements, even simple ones

```jsx
<Trans i18nKey="welcomeUser">
  Hello <strong>{{name}}</strong>. <Link to="/inbox">See my profile</Link>
</Trans>
// JSON -> "welcomeUser": "Hello <strong>{{name}}</strong>. <1>See my profile</1>"

<Trans i18nKey="multiline">
  Some newlines <br/> would be <br/> fine
</Trans>
// JSON -> "multiline": "Some newlines <br/> would be <br/> fine"
```

Here is what can be configured in `i18next.options.react` that affect this behaviour:

| Option                          | Default                      | Description                                                                                                                                                                                                                                                             |
| ------------------------------- | ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `transSupportBasicHtmlNodes`    | `true`                       | Enables keeping the name of simple nodes (e.g. `<br/>`) in translations instead of indexed keys                                                                                                                                                                         |
| `transKeepBasicHtmlNodesFor`    | `['br', 'strong', 'i', 'p']` | Which nodes are allowed to be kept in translations during `defaultValue` generation of `<Trans>`.                                                                                                                                                                       |
| `transWrapTextNodes` (v11.10.0) | `''`                         | Wrap text nodes in a user-specified element. e.g. set it to `span`. By default, text nodes are not wrapped. Can be used to work around a well-known Google Translate issue with React apps. See [facebook/react#11538](https://github.com/facebook/react/issues/11538). |

### Interpolation

You can pass in variables to get interpolated into the translation string by using objects containing those key:values.

```jsx
const person = { name: 'Henry', age: 21 };
const { name, age } = person;

<Trans>
  Hello {{ name }}. // <- = {{ "name": name }}
</Trans>
// Translation string: "Hello {{name}}"


<Trans>
  Hello {{ firstname: person.name }}.
</Trans>
// Translation string: "Hello {{firstname}}"
```

### Plural

You will need to pass the `count` prop:

```jsx
const messages = ['message one', 'message two'];

<Trans i18nKey="newMessages" count={messages.length}>
  You got {{ count: messages.length }} messages.
</Trans>

// Translation strings:
// "newMessages": "You got one message."
// "newMessages_plural": "You got {{count}} messages."
```

### Using with lists (v10.5.0)

You can still use `Array.map()` to turn dynamic content into nodes, using an extra option on a wrapping element:

```jsx
<Trans i18nKey="list_map">
  My dogs are named:
  <ul i18nIsDynamicList>
     {['rupert', 'max'].map(dog => (<li>{dog}</li>))}
  </ul>
</Trans>
// JSON -> "list_map": "My dogs are named: <1></1>"
```

Setting `i18nIsDynamicList` on the parent element will assert the `nodeToString` function creating the string for `saveMissing` will not contain children.

### Alternative usage (components array)

Some use cases, such as the ICU format, might be simpler by just passing content as props:

```javascript
<Trans
  i18nKey="myKey" // optional -> fallbacks to defaults if not provided
  defaults="hello <0>{{what}}</0>" // optional defaultValue
  values={{ what: 'world'}}
  components={[<strong>univers</strong>]}
/>
```

{% hint style="info" %}
`<0>` -> 0 is the index of the component in the components array
{% endhint %}

E.g. this format is needed when using [ICU as translation format](https://github.com/i18next/i18next-icu) as it is not possible to have the needed syntax as children (invalid jsx).

## How to get the correct translation string?

Guessing replacement tags _(<0>\</0>)_ of your component is rather difficult. There are four options to get those translations directly generated by i18next:

1. use React Developer Tools to inspect the `<Trans>` component instance and look at the `props.children` array for array index of the tag in question.
2. use `debug = true` in `i18next.init()` options and watch your console for the missing key output 
3. use the [saveMissing feature](https://www.i18next.com/configuration-options.html#missing-keys) of i18next to get those translations pushed to your backend or handled by a custom function. 
4. understand how those numbers get generated from child index:

**Sample JSX:**

```javascript
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

**Resulting translation string:**

```
"Hello <1>{{name}}</1>, you have {{count}} unread message. <5>Go to message</5>."
```

**The complete the node tree**:

```javascript
Trans.children = [
  'Hello ',                         // 0: only a string
  { children: [{ name: 'Jan' }] },  // 1: <strong> with child object for interpolation
  ', you have ',                    // 2: only a string
  { count: 10 },                    // 3: plain object for interpolation
  ' unread messages. ',             // 4: only a string
  { children: ['Go to messages'] }, // 5: <Link> with a string child
  '.'                               // 6: yep, you guessed: another string
]
```

**Rules:**

* child is a string: nothing to wrap; just take the string  
* child is an object: nothing to do; it's used for interpolation  
* child is an element: wrap it's children in `<x></x>` where `x` is the index of that element's position in the `children` list; handle its children with the same rules (starting `element.children` index at 0 again)

## Trans props

All properties are optional, although you'll need to use `i18nKey` if you're not using natural language keys (text-based).

| _**name**_   | _**type (default)**_       | _**description**_                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ------------ | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `i18nKey`    | `string (undefined)`       | <p>If you prefer to use text as keys you can omit this, and the translation will be used as key. Can contain the namespace by prepending it in the form <code>'ns:key'</code> (depending on <code>i18next.options.nsSeparator</code>)</p><p>But this is not recommended when used in combination with natural language keys, better use the dedicated ns parameter: <code>&#x3C;Trans i18nKey="myKey" ns="myNS">&#x3C;/Trans></code></p> |
| `ns`         | `string (undefined)`       | Namespace to use. May also be embedded in `i18nKey` but not recommended when used in combination with natural language keys, see above.                                                                                                                                                                                                                                                                                                  |
| `t`          | `function (undefined)`     | `t` function to use instead of `i18next.t()`                                                                                                                                                                                                                                                                                                                                                                                             |
| `count`      | `integer (undefined)`      | Numeric value for pluralizable strings                                                                                                                                                                                                                                                                                                                                                                                                   |
| `tOptions`   | `object (undefined)`       | Extra options to pass to `t()` (e.g. `context`, `postProcessor`, ...)                                                                                                                                                                                                                                                                                                                                                                    |
| `parent`     | `node (undefined)`         | A component to wrap the content into (can be globally set on `i18next.init`). **Required for React < v16**                                                                                                                                                                                                                                                                                                                               |
| `i18n`       | `object (undefined)`       | i18next instance to use if not provided                                                                                                                                                                                                                                                                                                                                                                                                  |
| `defaults`   | `string (undefined)`       | Use this instead of using the children as default translation value (useful for ICU)                                                                                                                                                                                                                                                                                                                                                     |
| `values`     | `object (undefined)`       | Interpolation values if not provided in children                                                                                                                                                                                                                                                                                                                                                                                         |
| `components` | `array[nodes] (undefined)` | Components to interpolate based on index of tag                                                                                                                                                                                                                                                                                                                                                                                          |

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
