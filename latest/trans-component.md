# Trans Component

## Important note

While the Trans components gives you a lot of power by letting you interpolate or translate complex react elements - the truth is - in most cases you won't need it.

**As long you have no react nodes you like to be integrated into a translated text** \(text formatting, like `strong`, `i`, ...\) **or adding some link component - you won't need it** - most can be done by using the good old `t` function.

{% hint style="warning" %}
It does ONLY interpolation. It does not rerender on language change or load any translations needed. Use useTranslation, withTranslation for those cases.
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
Using the **t** function have a look at i18next documentation:

* [essentials](https://www.i18next.com/essentials.html)
* [interpolation](https://www.i18next.com/interpolation.html)
* [formatting](https://www.i18next.com/formatting.html)
* [plurals](https://www.i18next.com/plurals.html)
* ...
{% endhint %}

## Samples

### Using with react components

So you learned there is no need to use the Trans component everywhere \(the plain t function will just do fine in most cases\).

This component enables you to nest any react content to be translated as one string. Supports both plural and interpolation.

_Let's say you want to create following html output:_

> Hello **Arthur**, you have 42 unread messages. [Go to messages](../legacy-v9/trans-component.md).

**Before:** Your react code would have looked something like:

```javascript
import React from 'react';

function MyComponent({ person, messages }) {
  const { name } = person;
  const count = messages.length;

  return (
    <div>
      Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
    </div>
  );
}
```

**After:** With the trans component just change it to:

```javascript
import React from 'react';
import { Trans } from 'react-i18next'

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

_Your en.json \(translation strings\) will look like:_

```javascript
"userMessagesUnread": "Hello <1>{{name}}</1>, you have {{count}} unread message. <5>Go to message</5>.",
"userMessagesUnread_plural": "Hello <1>{{name}}</1>, you have {{count}} unread messages.  <5>Go to messages</5>.",
```

{% hint style="info" %}
[**saveMissing**](https://www.i18next.com/overview/configuration-options#missing-keys) will send a valid defaultValue
{% endhint %}

### Alternative usage \(v11.6.0\)

```javascript
<Trans
  i18nKey="myKey" // optional -> fallbacks to defaults if not provided
  defaults="hello <italic>beautiful</italic> <bold>{{what}}</bold>" // optional defaultValue
  values={{ what: 'world'}}
  components={{ italic: <i />, bold: <strong /> }}
/>
```

This format is useful to interpolate a component multiple times. Another advantage is the simpler &lt;&gt; named tags -&gt; which makes guessing indexes a thing of the past.

{% hint style="warning" %}
Existing self-closing HTML tag names are reserved keys. `link: <Link />`, `img: <img src="" />`, `media: <img src="" />` won't work
{% endhint %}

### Using for &lt;br /&gt; and other simple html elements in translations \(v10.4.0\)

{% hint style="info" %}
This was newly added in react-i18next@**v10.4.0**

Allows elements not having additional attributes like className and only no children \(void\) or one text child:

* &lt;br/&gt;  
* &lt;strong&gt;bold&lt;/strong&gt;  
* &lt;p&gt;some paragraph&lt;/p&gt;  

but not:

* &lt;i className="icon-gear" /&gt;  // no attributes allowed
* &lt;strong title="something"&gt;bold something&lt;/strong&gt; // no attr
* &lt;bold&gt;bold&lt;i&gt;italic&lt;/i&gt;&lt;/b&gt; // no inner elements - only strings!
{% endhint %}

It allows you to have basic html tags inside your translations which will get converted to valid react elements:

```jsx
<Trans i18nKey="welcomeUser">
  Hello <strong>{{name}}</strong>.
</Trans>
// JSON -> "welcomeUser": "Hello <1>{{name}}</1>.",
<Trans i18nKey="multiline">
  Some newlines <br/> would be <br/> fine
</Trans>
// JSON -> "multiline": "Some newlines <1/> would be <3/> fine"
```

You can use i18next.options.react to adapt this behaviour:

| option | default | description |
| :--- | :--- | :--- |
| `transSupportBasicHtmlNodes` | `true` | convert eg. &lt;br/&gt; found in translations to a react component of type br |
| `transKeepBasicHtmlNodesFor` | `['br', 'strong', 'i', 'p']` | Which nodes not to convert in defaultValue generation in the Trans component. |

### Interpolation

You can pass in variables to get interpolated into the translation string by passing down objects in node children containing those key:values.

```jsx
const person = { name: 'Henry', age: 21 };
const { name, age } = person;

<Trans>
  Hello {{ name }}. // <- = {{ name: name }}
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

### Using with lists \(v10.5.0\)

You can use list.map as children. Mapping dynamic content.

```jsx
<Trans i18nKey="list_map">
  My dogs are named:
  <ul i18nIsDynamicList>
     {['rupert', 'max'].map(dog => (<li>{dog}</li>))}
  </ul>
</Trans>
// JSON -> "list_map": "My dogs are named: <1></1>"
```

Setting `i18nIsDynamicList` on the wrapping element will assert the nodeToString function creating the string for saveMissing will not contain children.

### Alternative usage \(components array\)

Some use cases might be simpler by just passing content as props:

```javascript
<Trans
  i18nKey="myKey" // optional -> fallbacks to defaults if not provided
  defaults="hello <0>{{what}}</0>" // optional defaultValue
  values={{ what: 'world'}}
  components={[<strong>univers</strong>]}
/>
```

{% hint style="info" %}
`<0>` -&gt; 0 is the index of the component in the components array
{% endhint %}

Eg. this format is needed when using [ICU as translation format](https://github.com/i18next/i18next-icu) as it is not possible to have the needed syntax as children \(invalid jsx\).

## How to get the correct translation string?

Guessing replacement tags _\(&lt;0&gt;&lt;/0&gt;\)_ of your component is rather difficult. There are four options to get those translations directly generated by i18next:

1. use React Developer Tools to inspect the `<Trans>` component instance and look at the `props.children` array for array index of the tag in question.
2. use `debug = true` in i18next init call and watch your console for the missing key output 
3. use the [saveMissing feature](https://www.i18next.com/configuration-options.html#missing-keys) of i18next to get those translations pushed to your backend or handled by a custom missing key handler. 
4. understand how those numbers get generated from child index:

**jsx:**

```javascript
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

**results in string:**

```text
"Hello <1>{{name}}</1>, you have {{count}} unread message. <5>Go to message</5>."
```

**based on** the node tree**:**

```javascript
Trans.children = [
  'Hello ',                           // index 0: only a string
  { children: [{ name: 'Jan'  }] },   // index 1: element strong -> child object for interpolation
  ', you have',                       // index 2: only a string
  { count: 10 },                      // index 3: just object for interpolation
  ' unread messages. ',               // index 4
  { children: [ 'Go to messages' ] }, // index 5: element link -> child just a string
  '.'
]
```

**Rules:**

* child is a string =&gt; nothing to wrap just take the string  
* child is an object =&gt; nothing to do it's used for interpolation  
* child is an element: wrap it's children in &lt;i&gt;&lt;/i&gt; where i is the index of that element position in children and handle it's children with same rules \(starting element.children index at 0 again\)

## Trans props

| _**name**_ | _**type \(default\)**_ | _**description**_ |
| :--- | :--- | :--- |
| `i18nKey` | `string (undefined)` | is optional. if you prefer to use text as keys you can omit that and the translation will be used as a key. Can contain the used namespace by prepending it key in form `'ns:key'` |
| `ns` | `string (undefined)` | namespace to `use` |
| `t` | `function (undefined)` | `t` function to use instead of i18next.t |
| `count` | `integer (undefined)` | optional count if you use a plural |
| `tOptions` | `object (undefined)` | optional options you like to pass to `t` function call \(eg. `context`, `postProcessor`, ...\) |
| `parent` | `node (undefined)` | a component to wrap the content into \(default none, can be globally set on i18next.init\) -&gt; needed for **react &lt; v16** |
| `i18n` | `object (undefined)` | i18next instance to use if not provided |
| `defaults` | `string (undefined)` | use this instead of default content in children \(useful when using ICU\) |
| `values` | `object (undefined)` | interpolation values if not provided in children |
| `components` | `array[nodes] (undefined)` | components to interpolate based on index of tag , ... |

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
  }
});
```

{% hint style="warning" %}
Please be aware if you are using **React 15 or below**, you need to set the `defaultTransParent` or `parent` in props.
{% endhint %}

