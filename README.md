# What is react-i18next?

<iframe src="https://ghbtns.com/github-btn.html?user=i18next&repo=react-i18next&type=star&count=true" frameborder="0" scrolling="0" width="170px" height="20px"></iframe>

[![Travis](https://img.shields.io/travis/i18next/react-i18next/master.svg?style=flat-square)](https://travis-ci.org/i18next/react-i18next)
[![Code Climate](https://codeclimate.com/github/codeclimate/codeclimate/badges/gpa.svg)](https://codeclimate.com/github/i18next/react-i18next)
[![Coveralls](https://img.shields.io/coveralls/i18next/react-i18next/master.svg?style=flat-square)](https://coveralls.io/github/i18next/react-i18next)
[![Package Quality](http://npm.packagequality.com/shield/react-i18next.svg)](http://packagequality.com/#?package=react-i18next)
[![npm version](https://img.shields.io/npm/v/react-i18next.svg?style=flat-square)](https://www.npmjs.com/package/react-i18next)
[![Bower](https://img.shields.io/bower/v/react-i18next.svg?style=flat-square)]()
[![David](https://img.shields.io/david/i18next/react-i18next.svg?style=flat-square)](https://david-dm.org/i18next/react-i18next)



react-i18next is an **internationalization** addon for **reactjs** and is based on [i18next](http://i18next.com).

The module asserts that needed translations get loaded for your components and that your content gets rerendered on language changes.

As react-i18next builds on [i18next](http://i18next.com) you can use it on any other UI framework or on the server (node.js) too. As react philosophy: **Learn once - use everywhere**.

# How does my code look like

**Before:** Your react code would have looked something like:

```js
...
<div>Just simple content</div>
<div>
    Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
</div>
...
```

**After:** With the trans component just change it to:

```js
...
<div>{t('simpleContent')}</div>
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
...
```

If you prefer not using semantic keys for your content but prefer using your content - [that's also possible](https://www.i18next.com/principles/fallback.html#key-fallback).


# On top: Localization as a service


It even provides with [locize.com](http://locize.com) a own translation management tool - localization as a service offering.

<img src="/assets/img/dashboard.png" width="50%" />

[Learn more about the enterprise offering](https://www.i18next.com/for-enterprises.html)