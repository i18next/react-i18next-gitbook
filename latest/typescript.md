# TypeScript

react-i18next has embedded type definitions. If your project is relying on **TypeScript 4.1+**, and you want to enhance IDE Experience and prevent errors \(such as type coercion\), you should follow the instructions below in order to get the `t` function fully-type safe \(`keys` and `return` type\).

{% hint style="info" %}
This is an **optional** feature and was newly added in react-i18next@v11.8.0. [Here](https://github.com/i18next/react-i18next/tree/master/example/react-typescript4.1) you can see some examples of use.
{% endhint %}

## Create a declaration file

TypeScript definitions for react-i18next can be extended by using [Type Augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation) and [Merging Interfaces](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#merging-interfaces). So the first step is creating a declaration file \(`react-i18next.d.ts`\), for example:

```typescript
// import the original type declarations
import 'react-i18next';
// import all namespaces (for the default language, only)
import ns1 from 'locales/en/ns1.json';
import ns2 from 'locales/en/ns2.json';

declare module 'react-i18next' {
  // and extend them!
  interface Resources {
    ns1: typeof ns1;
    ns2: typeof ns2;
  }
}
```

Or, if you want to include all namespaces at once, you can use the following approach:

**`i18n.ts`**
```tsx
export const resources = {
  en: {
    ns1,
    ns2,
  },
} as const;

i18n.use(initReactI18next).init({
  lng: 'en',
  ns: ['ns1', 'ns2'],
  resources,
});
```

**`react-i18next.d.ts`**
```tsx
import { resources } from './i18n';

declare module 'react-i18next' {
  type DefaultResources = typeof resources['en'];
  interface Resources extends DefaultResources {}
}
```

That's all! Your `t` function should be fully typed by now.

## Troubleshooting

### Slow compilation time

In order to fully type the `t` function, we recursively map all nested keys from your primary locale files or objects. Depending on the number of keys your project have, the compilation time could be noticeably affected. If this is negatively influencing your productivity, this feature might not be the best choice for you. In our tests, we got great results when declaring up to 7000 keys, but it may vary according to the project size as well. If needed, you can always open an issue on Github to get some help from us.

### Type errors

If you face this issue:

> Argument of type 'string' is not assignable to parameter of type ...

When using the following approach \(template literal with an expression\):

```typescript
const { t } = useTranslation();
t(`${expression}.title`);
```

Or:

```typescript
const { t } = useTranslation(`${ns}Default`);
```

TypeScript will lose the literal value, and it will infer the `key` as string, which will cause to throw the error above. In this case, you will need to assert the template string `as const`, like this:

```typescript
const { t } = useTranslation();
t(`${expression}.title` as const);
```

For now, this is the only possible workaround. This is a TypeScript limitation which will be handled on [TypeScript 4.2](https://devblogs.microsoft.com/typescript/announcing-typescript-4-2-beta/#smarter-type-alias-preservation).

### Tagged Template Literal

If you are using the tagged template literal syntax for the `t` function, like this:

```typescript
t`key1.key2`;
```

The `keys` and `return` type inference will not work, because [TemplateStringsArray](https://github.com/microsoft/TypeScript/issues/33304) does not accept generic types yet. You can use Tagged Template Literal syntax, but it will accept any string as argument.

