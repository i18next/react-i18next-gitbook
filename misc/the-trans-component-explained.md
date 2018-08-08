# The Trans Component Explained

{% hint style="info" %}
The original explanation was created by Alexander \(@arkross at github\) under following link:  
  
[https://github.com/arkross/arkross.github.io/wiki/Using-react-i18next-Trans-Component](https://github.com/arkross/arkross.github.io/wiki/Using-react-i18next-Trans-Component)  
  
Thank you for providing this in detail explanation.
{% endhint %}

There are 2 parts to understand for using the [Trans component](../components/trans-component.md):

The template \(in your JS code\) and the translation \(in your translation files\). Let's take example from the official documentation:

Example JSX template:

```text
<Trans i18nKey='userMessagesUnread' count={count}>
    Hello <strong title={t('nameTitle')}>{{name: 'Jan'}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

Example Translation:

```text
"userMessagesUnread": "Hello <1><0>{{name}}</0></1>, you have <3>{{count}}</3> unread message. <5>Go to message</5>."
```

I am not going to explain the pluralization here. Instead, I am talking about the segmentation of nodes.

### Where do those `<0>`, `<1>` tags come from? Where are `<2>`and `<4>`?

First, we see Trans component's template. The app splits the children into `nodes`, including **text nodes**. Still going by the official documentation, we get this array:

```text
Trans.children = [
  'Hello ', // index 0
  { children: [{ name: 'Jan'  }] }, // index 1
  ', you have', // index 2
  { count: 10 }, // index 3
  ' unread messages. ', // index 4
  { children: [ 'Go to messages' ] }, // index 5
  '.' // index 6
]
```

But we have `<1><0>{{name}}</0></1>`! I know, just ignore the nested tags like `<0>{{name}}</0>`for now. We have 7 children nodes in the template. Those indexes will be mapped to the **top level** index in your translation e.g.

* `<0></0>` will be replaced with a string node `Hello`.
* `<1></1>` will be replaced with a nested node `<strong title={t('nameTitle')}></strong>`with length 1, see the array `[{name: 'Jan'}]`.
* `<2></2>` will be replaced with a string node `, you have`.
* `<5></5>` will be replaced with a nested node `<Link to="/msgs"></Link>` with length of 1, see the array `[ 'Go to messages' ]`.
* etc, now you get the idea of **top level** indexes.

However, you see in the translation only 3 **top level** indexes are used, namely `<1>`, `<3>`, and `<5>`. It means whatever you write in the template for the unused nodes such as `<0>`, `<2>`, and `<4>`, **will not** affect the result. Without changing the translation, we can rewrite the JSX template like this:

```text
<Trans i18nKey='userMessagesUnread' count={count}>
    This is unused <strong title={t('nameTitle')}>{{name: 'Jan'}}</strong>, this is also unused {{count}} another unused node. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

The result won't change. You can even change them to spaces, as long as the nodes index doesn't change.

### Then it's a waste of index!

No. It's there for readibility. But I agree it can be confusing when writing the translation if the index is not sequential. If you really want it to **not** waste indexes, you can rewrite both template and translation **without changing the result**:

JSX template:

```text
<Trans i18nKey='userMessagesUnread' count={count}>
    <strong title={t('nameTitle')}>{{name: 'Jan'}}</strong>{{count}}<Link to="/msgs">third node</Link>
</Trans>
```

See that now the template does not have any unnecessary string nodes **including spaces** between important nodes. This will change the nodes' indexes.

Translation:

```text
"userMessagesUnread": "Hello <0><0>{{name}}</0></0>, you have <1>{{count}}</1> unread message. <2>Go to message</2>."
```

Now the **top level** index only have `<0>`, `<1>`, and `<2>`. No skipped numbers, no surprises. However, pay attention to readibility. Your choice.

### Enough about top level index. How about the nested one?

Actually it does not differ much from top level index. The nested index is **local** to the encapsulating parent node, so the first children of a node will always be `<0></0>`. Let's have a look at the official example again.

```text
Trans.children = [
  'Hello ',
  { children: [{ name: 'Jan'  }] }, // index 1. It has one children.
  ', you have',
  { count: 10 },
  ' unread messages. ',
  { children: [ 'Go to messages' ] }, // index 5. It has one children.
  '.'
]
```

Let's see what happens within top level index `<1>`:

* It refers to a html node `<strong title={t('nameTitle')}></strong>`.
* It has one child node which can be rendered using `<0></0>`.
* The content of `<0></0>` corresponds to the first child, which is an object: `{name: 'Jan'}`.
* Should you choose to render the property `name` from that object, you use `<0>{{name}}</0>`.

Together with the top level index, this becomes `<1><0>{{name}}</0></1>` in the translation.

Now you might \(or might not\) notice that top level `<5>` also has children with length of 1, but is not used in the translation. Strangely, you can freely change the text within that node in JSX template **but you cannot completely delete it**. That node still needs a child to render properly. Otherwise, only an empty tag will be rendered: `<Link to="/msgs"></Link>`, which is probably not what you want. Top level nodes do not have this problem, though.

### Experimental

Now that we grasp the basic concept, we can use the template in a slightly complex way. Assuming we ignore the `title` attribute in `<strong>` tag and the value of `count`, we focus only on the content of the `<strong>` tag.

#### Example 1

```text
<Trans i18nKey='userMessagesUnread' count={count}>
    <Hello <strong title={t('nameTitle')}>{{name: 'Jan', title='Mr.'}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

```text
"userMessagesUnread": "Hello <1><0>{{title}} {{name}}</0></1>, you have <3>{{count}}</3> unread message. <5>Go to message</5>."
```

#### Example 2

```text
<Trans i18nKey='userMessagesUnread' count={count}>
    Hello <strong title={t('nameTitle')}>{{name: 'Jan'}}{{title: 'Mr.'}}</strong>, you have {{count}} unread message. <Link to="/msgs">Go to messages</Link>.
</Trans>
```

```text
"userMessagesUnread": "Hello <1><1>{{title}}</1> <0>{{name}}</0></1>, you have <3>{{count}}</3> unread message. <5>Go to message</5>."
```

Both examples will render the same output

```text
Hello <strong title='Jan'>Mr. Jan</strong>, you have 1 unread message. <Link to="/msgs">Go to messages</Link>
```

