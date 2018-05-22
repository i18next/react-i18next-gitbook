# Introduction

## What is react-i18next?

![Travis](https://img.shields.io/travis/i18next/react-i18next/master.svg?style=flat-square)

![Code Climate](https://codeclimate.com/github/codeclimate/codeclimate/badges/gpa.svg)

![Coveralls](https://img.shields.io/coveralls/i18next/react-i18next/master.svg?style=flat-square)

![Package Quality](http://npm.packagequality.com/shield/react-i18next.svg)

![npm version](https://img.shields.io/npm/v/react-i18next.svg?style=flat-square)

![Bower](https://img.shields.io/bower/v/react-i18next.svg?style=flat-square)

![David](https://img.shields.io/david/i18next/react-i18next.svg?style=flat-square)

react-i18next is an **internationalization** addon for **reactjs** / **reactnative** and is based on [i18next](http://i18next.com).

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
    Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
</div>
...
```

**After:** With the trans component just change it to:

```javascript
...
<div>{t('simpleContent')}</div>
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
...
```

If you prefer not using semantic keys for your content but prefer using your content - [that's also possible](https://www.i18next.com/principles/fallback.html#key-fallback).

Or have a look at the interactive playground at [webpackbin](https://www.webpackbin.com/bins/-KvzvUPFsBVI_74Jll99)

## On top: Localization as a service

It even provides with [locize.com](http://locize.com/?utm_source=react_i18next_com&utm_medium=gitbook) a own translation management tool - localization as a service offering.

![](../.gitbook/assets/dashboard.png)

[Learn more about the enterprise offering](https://www.i18next.com/for-enterprises.html)

