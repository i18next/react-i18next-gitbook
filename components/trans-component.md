# Trans component

This component enables you to nest any react content to be translated as one string. Supports both plural and interpolation.

Lets say you want to create following html output:

Hello **Arthur**, you have 42 unread messages. [Go to messages](/#).

Your react code would have looked something like:

```js
<div>
    Hello <strong title="this is your name">{name}</strong>, you have {count} unread message(s). <Link to="/msgs">Go to messages</Link>.
</div>
```

With the trans component just change it to:

```js
<Trans i18nKey="userMessagesUnread" count={count}>
    Hello <strong title={t('nameTitle')}>{{name}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

Your json will look like:

en:

```json
"userMessagesUnread": "Hello <1><0>{{name}}</0></1>, you have <3>{{count}}</3> unread message. <5>Go to message</5>.",
"userMessagesUnread_plural": "Hello <1><0>{{name}}</0></1>, you have <3>{{count}}</3> unread messages.  <5>Go to messages</5>.",
```


