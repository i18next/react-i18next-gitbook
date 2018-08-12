# Introduction

## What is react-i18next?

react-i18next is an **internationalization** addon for **reactjs** / **reactnative** and is based on [i18next](http://i18next.com).

{% hint style="info" %}
You should read the [i18next](https://www.i18next.com) documentation at some point as we do not repeat all the [configuration options](https://www.i18next.com/overview/configuration-options) or translation functionalities like [plurals](https://www.i18next.com/translation-function/plurals), [formatting](https://www.i18next.com/translation-function/formatting), [interpolation](https://www.i18next.com/translation-function/interpolation), ...
{% endhint %}

The module asserts that needed translations get loaded for your components and that your content gets rerendered on language changes.

Based on the zero dependencies and build tools react-i18next is optimally suited for **serverside rendering** too. [Learn more](../misc/serverside-rendering.md).

As react-i18next builds on [i18next](http://i18next.com) you can use it on any other UI framework or on the server \(node.js\) too. As react philosophy - but: **Learn once - translate everywhere**.

![video](https://raw.githubusercontent.com/i18next/react-i18next/master/example/locize-example/video_sample.png)

[watch the video](https://www.youtube.com/watch?v=9NOzJhgmyQE)

## What does my code look like

**Before:** Your react code would have looked something like:

```javascript
...
<div>Just simple content</div>
<div>
    Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <a>Go to messages</a>.
</div>
...
```

**After:** With the trans component just change it to:

```javascript
...
<div>{t('simpleContent')}</div>
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <a>Go to messages</a>.
</Trans>
...
```

If you prefer not using semantic keys for your content but prefer using your content - [that's also possible](https://www.i18next.com/principles/fallback.html#key-fallback).

Or have a look at the interactive playground on [codesandbox](https://codesandbox.io/s/l4qrory2nl)

## On top: Localization as a service

It even provides with [locize.com](http://locize.com/?utm_source=react_i18next_com&utm_medium=gitbook) a own translation management tool - localization as a service offering.

![](../.gitbook/assets/dashboard.png)

[Learn more about the enterprise offering](https://www.i18next.com/for-enterprises.html)

