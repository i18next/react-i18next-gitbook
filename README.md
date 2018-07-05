# Introduction

## What is react-i18next?

react-i18next is an **internationalization** addon for **reactjs** / **reactnative** and is based on [i18next](http://i18next.com).

The module asserts that needed translations get loaded for your components and that your content gets rerendered on language changes.

Based on the zero dependencies and build tools react-i18next is optimal suited for **serverside rendering** too. [Learn more](misc/serverside-rendering.md).

{% hint style="info" %}
If your app is **very simple** and there is:

* No need to trigger rerender on language change
* No need for lazy loading namespaces
* No use case for the [Trans component](components/trans-component.md)

You can just use i18next directly _\(import it - init somewhere - and use i18next.t\)_
{% endhint %}

As react-i18next builds on [i18next](http://i18next.com) you can use it on any other UI framework or on the server \(node.js\) too. As react philosophy - but: **Learn once - translate everywhere**.

![video](https://raw.githubusercontent.com/i18next/react-i18next/master/example/locize-example/video_sample.png)

[watch the video](https://www.youtube.com/watch?v=9NOzJhgmyQE)

## How does my code look like

### Basic string translation

**Before:**

```javascript
<div>Just simple content</div>
```

**After:**

```javascript
<div>{t('simpleContent')}</div>
```

### Using Trans component for complexer component interpolation

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

If you prefer not using semantic keys for your content but prefer using your content - [that's also possible](https://www.i18next.com/principles/fallback.html#key-fallback).

Or have a look at the interactive playground at [codesandbox.io](https://codesandbox.io/s/8n252n822)

## On top: Localization as a service

It even provides with [locize.com](http://locize.com/?utm_source=react_i18next_com&utm_medium=gitbook) a own translation management tool - localization as a service offering.

![](.gitbook/assets/dashboard.png)

[Learn more about the enterprise offering](https://www.i18next.com/for-enterprises.html)

