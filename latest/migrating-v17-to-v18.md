---
description: "Migrate react-i18next from v17 to v18: install it together with i18next 27, React 18 or later, Trans output independent of escapeValue, Server Component imports, the opt-in React type augmentation and the removed icu.macro."
---

# Migrating v17 to v18

react-i18next 18 is released together with i18next 27. Most apps only need to update both packages and their test snapshots. The sections below list every change you may notice, with the one-line fix where there is one.

{% hint style="info" %}
If you're looking for the full list of changes, please have a look in the [CHANGELOG](https://github.com/i18next/react-i18next/blob/master/CHANGELOG.md) file.
{% endhint %}

## Install both majors together

react-i18next 18 needs i18next 27:

```bash
npm install i18next@27 react-i18next@18
```

i18next 27 changes some defaults (`fallbackLng`, `returnEmptyString`, `/` in escaped values, strict interpolation types). Read the [i18next migration guide](https://www.i18next.com/misc/migration-guide#v26.x.x-to-v27.0.0) for those.

If you cannot move to react-i18next 18 yet: on i18next 27, use react-i18next 17.1.13 or later (it brings the types of the `react` init options), or 18.

## React 18 or later

React 16 and 17 are no longer supported (for React Native, that means 0.69 or later). Apps on React 16 or 17 stay on react-i18next 17.

`useSyncExternalStore` now comes from React itself, so the `use-sync-external-store` package is no longer a dependency.

## Trans renders entities as text, whatever escapeValue is

`Trans` used to show escaped values as entities with i18next's default `escapeValue: true`: a value `Tom & Jerry` rendered as `Tom &amp; Jerry`, and a date `17/12/2025` as `17&#x2F;12&#x2F;2025`. Unless you passed `shouldUnescape`, you had to set `escapeValue: false` to get readable text.

Now `Trans` always decodes the entities after it has parsed the translation, like `IcuTrans` already did, in text and in attribute values, both with `react.unescape` (the default decodes the common entities). Its output is the same for `escapeValue: true` and `false`:

* values render as text, never as markup (this part is in place since v17.1.10): to render markup, use `components` or children, or `{{- var}}` in the translation for a value the translator may insert unescaped
* entities in the translation itself (`&amp;`, `&lt;`, `&nbsp;`, ...) render as their characters
* the `shouldUnescape` prop and `react.transDefaultProps.shouldUnescape` are ignored. Remove them. In development and with `debug: true`, `Trans` warns about them (code `DEPRECATED_OPTION`).

**What to do:** update snapshot tests that contain `&amp;`, `&#x2F;`, `&lt;` and similar inside `Trans` output, and remove `shouldUnescape`.

## escapeValue guidance

`Trans` and `IcuTrans` no longer depend on `interpolation.escapeValue`. `escapeValue: false` is still fine for React apps, because React escapes the text it renders. Keep `escapeValue: true` if you put `t()` output into `dangerouslySetInnerHTML` or another HTML sink.

## Trans and IcuTrans follow language changes on their own

`Trans` and `IcuTrans` now re-render on the same events as `useTranslation`: `react.bindI18n` (default `languageChanged`) and `react.bindI18nStore`. You no longer need a `useTranslation()` call or a changing `key` in a parent only to update them after `changeLanguage()`.

* With a `t` prop, the passed `t` still decides the language: a `t` from `getFixedT('en')` keeps rendering English.
* They do not suspend or load anything on their own; that stays the job of `useTranslation` and friends.
* `TransWithoutContext` and `IcuTransWithoutContext` use no hooks and do not re-render on their own.
* Test mocks of the instance need `on` and `off` (see [SSR: useSSR and withSSR](#ssr-usessr-and-withssr) for all mock additions).

## The i18n instance from useTranslation

The `i18n` returned by `useTranslation` (and passed by `withTranslation` and `Translation`) used to be a copy of the instance, taken when the language changed. A copy held across `await i18n.changeLanguage('de')` still reported the old language, and properties set on it never reached the instance.

It now forwards every read and write to the instance, so it always shows the current state. It is still a new object whenever the language changes, so dependency arrays, `memo` and the React Compiler notice the change as before, and `i18n === i18next` is still `false`.

Because an older object now reads the current language too, comparing the language of the previous `i18n` with the current one no longer sees a change.

**What to do:** compare the objects themselves, or keep the previous language yourself:

```javascript
// before: never true in v18, both objects read the current language
componentDidUpdate(prevProps) {
  if (prevProps.i18n.language !== this.props.i18n.language) this.reload();
}

// v18: a new object means the language state changed
componentDidUpdate(prevProps) {
  if (prevProps.i18n !== this.props.i18n) this.reload();
}
```

The same goes for hooks: compare `usePrevious(i18n) !== i18n`, or keep the previous `i18n.language` in a ref.

## react options are per instance

`initReactI18next` used to copy the `react` options of the instance it was registered on into module-wide defaults, so a second instance without `react` options inherited them (for example `useSuspense: false`). Now each instance uses its own `react` options on top of the defaults.

**What to do:** if you create several instances and only one sets `react` options, set them on every instance, or call `setDefaults()` once:

```javascript
import { setDefaults } from 'react-i18next';

setDefaults({ useSuspense: false });
```

## SSR: useSSR and withSSR

`useSSR` and `withSSR` keep their signatures. They now call i18next's `hydrate(resources, lng)` (since i18next 26.7) instead of writing the instance's internal flags.

**What to do:** only custom code that set `isInitialized`, `initializedStoreOnce`, `initializedLanguageOnce` or `services.resourceStore.data` itself should call `i18n.hydrate(initialI18nStore, initialLanguage)` instead. The flags are still writable in i18next 27, but deprecated.

Test mocks of the i18next instance need small additions:

* `on` and `off` wherever `Trans` or `IcuTrans` render with them, since both subscribe to events now (with `bindI18nStore` set, also `store.on` and `store.off`): a minimal mock like `{ t, options, language }` throws `i18n.on is not a function`
* a `hydrate` method where they are used with `useSSR` or `withSSR`
* `once` where translations load before `init()` has finished

Tests that use a real instance (`i18next.createInstance()`) need nothing.

## Server Components can import from react-i18next

In a React Server Component, `import { Trans } from 'react-i18next'` used to fail with `createContext is not a function`. The package now has a `react-server` export condition (used by Next.js and other RSC bundlers) with what works without context and hooks:

* `Trans` and `IcuTrans` (the context-free variants), `TransWithoutContext` and `IcuTransWithoutContext`
* `nodesToString`, `initReactI18next`, `setI18n`/`getI18n`, `setDefaults`/`getDefaults`

Hooks, HOCs, `Translation`, `I18nextProvider`, `I18nContext`, `useSSR`, `withSSR`, `composeInitialProps` and `getInitialProps` stay client-only: importing them in a Server Component is a build error instead of a crash at runtime.

Without an `i18n` prop, the server `Trans` and `IcuTrans` use the global instance set by `initReactI18next`. That works for an app with a single instance. With an instance per request, pass that instance as `i18n` (`t` is optional). A `t` alone is not enough: a `t` from `getFixedT` carries no instance, so the options, formatting and the language for the values would still come from the global one. In development and with `debug: true`, they warn when `i18n` is missing (code `GLOBAL_INSTANCE_ON_SERVER`):

```jsx
// app/[lng]/page.jsx (Server Component), here with next-i18next's getT
import { getT } from 'next-i18next/server';
import { Trans } from 'react-i18next';

export default async function Page() {
  const { t, i18n } = await getT('common');
  return (
    <Trans t={t} i18n={i18n} i18nKey="welcome" components={{ bold: <strong /> }} />
  );
}
```

The `react-i18next/TransWithoutContext` and `react-i18next/IcuTransWithoutContext` entry points keep working.

## TypeScript

### allowObjectInHTMLChildren needs an import

react-i18next no longer changes React's `HTMLAttributes['children']` type for every app. If you set `allowObjectInHTMLChildren: true` (to write objects like `{{ name }}` inside HTML elements nested in `Trans`), add one import next to that declaration:

```typescript
// i18next.d.ts
import 'react-i18next/augment-react';

declare module 'i18next' {
  interface CustomTypeOptions {
    allowObjectInHTMLChildren: true;
    // ...
  }
}
```

With current `@types/react`, this augmentation needs `skipLibCheck: true`: `@types/react` declares `children` in `DOMAttributes`, and widening it in `HTMLAttributes` fails library checking with TS2430. Without `allowObjectInHTMLChildren` you need nothing; `i18nIsDynamicList` keeps working without the import.

### I18nContext can be undefined

`useContext(I18nContext)` returns `undefined` outside an `I18nextProvider`, and its value also carries `defaultNS`. The type now says so: `{ i18n: i18n; defaultNS?: string | string[] } | undefined`. Add a check or use `useTranslation()`. `getI18n()` keeps its type.

### ReactOptions

The type of the `react` init options now comes from react-i18next: i18next 27 declares an empty `ReactOptions` interface and react-i18next fills it in. `import type { ReactOptions } from 'i18next'` keeps working, and `ReactOptions` can also be imported from `react-i18next`. A program that does not load react-i18next's types no longer checks the `react` options.

### Smaller changes

* `useSSR` accepts its third argument `{ i18n }` and `useTranslation` its `bindI18nStore` option in the types, as at runtime.
* `shouldUnescape` is marked `@deprecated`.
* With i18next 27's `strictInterpolationChecks`, `t('key')` needs the variables of the string. `Trans` and `IcuTrans` keep `values` optional.

## icu.macro is removed

The `react-i18next/icu.macro` Babel macro (deprecated since v17.0.21) and the placeholder exports `date`, `time`, `number`, `select`, `plural` and `selectOrdinal` of the main entry are removed. Write [`IcuTrans`](icu-trans-component.md) directly:

```jsx
// before
import { Trans } from 'react-i18next/icu.macro';

<Trans i18nKey="welcome" values={{ name }}>
  Welcome back, <strong>{name}</strong>!
</Trans>

// after
import { IcuTrans } from 'react-i18next';

<IcuTrans
  i18nKey="welcome"
  defaultTranslation="Welcome back, <0>{name}</0>!"
  content={[{ type: 'strong' }]}
  values={{ name }}
/>
```

Plurals and selects go into the ICU message, for example `{count, plural, one {# message} other {# messages}}`. You can remove `babel-plugin-macros` if nothing else uses it.

## UMD and AMD builds

The UMD build (`react-i18next.js`, `react-i18next.min.js`, `dist/umd/`) no longer contains a copy of i18next: it uses the global `i18next`, so load i18next's UMD build first. It is also less than half the size.

```html
<script src="https://unpkg.com/i18next@27/i18next.min.js"></script>
<script src="https://unpkg.com/react-i18next@18/react-i18next.min.js"></script>
```

The AMD build (`dist/amd/`) is removed. RequireJS and other AMD loaders can load the UMD file instead, with a module path for `i18next`.

## Package

* `engines.node` is `>=22`.
* The build targets ES2020 syntax (optional chaining, `??`), pinned instead of following browserslist `defaults`.
* `@babel/runtime` is no longer a dependency.

## Tooling

* [i18next-cli](https://github.com/i18next/i18next-cli): update to the release published with i18next 27.
* [next-i18next](https://github.com/i18next/next-i18next): its peers already allow both majors.
