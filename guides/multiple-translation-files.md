# Multiple Translation Files

One of the advantages of react-i18next is based on i18next it supports the separation of translations into multiple files - which are called namespaces in i18next context -&gt; as you're accessing keys from a namespace defining that as a prefix:

So while this takes the translation from the defined default namespace:

```javascript
i18next.t('look.deep');
```

This will lookup the key in a namespace \(file\) called common.json:

```javascript
i18next.t('common:look.deep');
```

In order to use multiple namespaces/translation files, you need to specify it when calling [`useTranslation`](https://react.i18next.com/latest/usetranslation-hook) :

```javascript
const { t } = useTranslation(['translation', 'common']);
```

[`withTranslation`](https://react.i18next.com/latest/withtranslation-hoc):

```javascript
withTranslation(['translation', 'common'])(MyComponent);
```

or [`Translation`](https://react.i18next.com/latest/translation-render-prop):

```javascript
<Translation ns={['translation', 'common']}>
{
  (t) => <p>{t('common:look.deep')}</p>
}
</Translation>
```

## Separating translation files

In i18next you got a lot of options to add translations on init, in your code calling API methods or using one of the backend implementation. For a detailed write up check out the ["Add or load translation guide on i18next.com"](https://www.i18next.com/how-to/add-or-load-translations).

With react-i18next you can use any of the components passing down the `t` function to your components to load namespaces:

* [useTranslation \(hook\)](../latest/usetranslation-hook.md)
* [withTranslation \(HOC\)](../latest/withtranslation-hoc.md)
* [Translation \(render prop\)](../latest/translation-render-prop.md)

All take arguments to define which namespaces to load and will Suspense rendering until those got loaded.

So you do not need to load all translations upfront enabling you to create huge react based applications without slowing down loading of the first page cause all translations need to be loaded upfront \(hello other i18n implementations\).



