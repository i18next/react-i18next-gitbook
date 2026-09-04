---
description: "Testing components that use react-i18next: export the plain component or test without stubbing, with an example i18next configuration for tests."
---

# Testing

For testing purpose of your component you should export the pure component without extending with the withTranslation hoc and test that:

```javascript
export MyComponent;
export default withTranslation('ns')(MyComponent);
```

In the test, test the myComponent export passing a t function mock:

```javascript
import { MyComponent } from './myComponent';

<MyComponent t={key => key} />
```

Or create a manual mock in `__mocks__/react-i18next.js` (picked up automatically by jest) that covers `useTranslation`, `withTranslation` and `<Trans>` in one place, so components mixing all three render their keys and children as plain text:

```jsx
const React = require('react');
const reactI18next = require('react-i18next');

const useMock = [(k) => k, { changeLanguage: () => new Promise(() => {}) }];
useMock.t = (k) => k;
useMock.i18n = { changeLanguage: () => new Promise(() => {}) };

module.exports = {
  ...reactI18next,
  withTranslation: () => (Component) => (props) => <Component t={(k) => k} {...props} />,
  Trans: ({ children, i18nKey }) => children ?? i18nKey,
  useTranslation: () => useMock,
};
```

A more complete version of this mock (including a `<Trans>` mock that renders nested elements) is part of the runnable jest example: [example/test-jest/src/\_\_mocks\_\_/react-i18next.js](https://github.com/i18next/react-i18next/blob/master/example/test-jest/src/__mocks__/react-i18next.js)

Or mock it like:

```javascript
jest.mock('react-i18next', () => ({
  // this mock makes sure any components using the translate HoC receive the t function as a prop
  withTranslation: () => Component => {
    Component.defaultProps = { ...Component.defaultProps, t: (i18nKey) => i18nKey };
    // or with TypeScript:
    //Component.defaultProps = { ...Component.defaultProps, t: (i18nKey: string) => i18nKey };
    return Component;
  },
}));
```

Or, when using the `useTranslation` hook instead of `withTranslation`, mock it like:

```javascript
jest.mock('react-i18next', () => ({
  // this mock makes sure any components using the translate hook can use it without a warning being shown
  useTranslation: () => {
    return {
      t: (i18nKey) => i18nKey,
      // or with TypeScript:
      //t: (i18nKey: string) => i18nKey,
      i18n: {
        changeLanguage: () => new Promise(() => {}),
      },
    };
  },
  initReactI18next: {
    type: '3rdParty',
    init: () => {},
  }
}));
```

or, you can also spy the `t` function:

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-jsx"><code class="lang-jsx"><strong>// implementation
</strong>import React from 'react';
import { useTranslation } from 'react-i18next';

export default function CustomComponent() {
  const { t } = useTranslation();

  return &#x3C;div>{t('some.key', { some: 'variable' })}&#x3C;/div>;
}

<strong>// test
</strong>import React from 'react';
import { render, screen } from '@testing-library/react';
import UseTranslationWithInterpolation from './UseTranslationWithInterpolation';
import { useTranslation } from 'react-i18next';

jest.mock('react-i18next', () => ({
  useTranslation: jest.fn(),
}));

it('test render', () => {
  const useTranslationSpy = useTranslation;
  const tSpy = jest.fn((str) => str);
  useTranslationSpy.mockReturnValue({
    t: tSpy,
    i18n: {
      changeLanguage: () => new Promise(() => {}),
    },
  });

  render(&#x3C;UseTranslationWithInterpolation />);

  expect(screen.getByText('some.key')).toBeInTheDocument();

  // If you want you can also check how the t function has been called,
  // but basically this is testing your mock and not the actual code.
  expect(tSpy).toHaveBeenCalledTimes(1);
  expect(tSpy).toHaveBeenLastCalledWith('some.key', { some: 'variable' });
});
</code></pre>
{% endtab %}

{% tab title="TypeScript" %}
<pre class="language-tsx"><code class="lang-tsx"><strong>// implementation
</strong>import React from 'react';
import { useTranslation } from 'react-i18next';

export default function CustomComponent() {
  const { t } = useTranslation();

  return &#x3C;div>{t($ => $.some.key, { some: 'variable' })}&#x3C;/div>;
}

<strong>// test
</strong>import React from 'react';
import { render, screen } from '@testing-library/react';
import UseTranslationWithInterpolation from './UseTranslationWithInterpolation';
import { useTranslation } from 'react-i18next';

jest.mock('react-i18next', () => ({
  useTranslation: jest.fn(),
}));

it('test render', () => {
  const useTranslationSpy = useTranslation;
  const tSpy = jest.fn((str) => str);
  useTranslationSpy.mockReturnValue({
    t: tSpy,
    i18n: {
      changeLanguage: () => new Promise(() => {}),
    },
  });

  render(&#x3C;UseTranslationWithInterpolation />);

  expect(screen.getByText('some.key')).toBeInTheDocument();

  // If you want you can also check how the t function has been called,
  // but basically this is testing your mock and not the actual code.
  expect(tSpy).toHaveBeenCalledTimes(1);
  expect(tSpy).toHaveBeenLastCalledWith('some.key', { some: 'variable' });
});
</code></pre>
{% endtab %}
{% endtabs %}

{% hint style="success" %}
You can find a full sample for testing with jest here: [https://github.com/i18next/react-i18next/tree/master/example/test-jest](https://github.com/i18next/react-i18next/tree/master/example/test-jest)
{% endhint %}

## Testing without stubbing

Alternatively, you could also test I18next without stubbing anything, by providing the correct configuration and fully wrapping your container in the provider.

### Example configuration for testing

```javascript
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

i18n
  .use(initReactI18next)
  .init({
    lng: 'en',
    fallbackLng: 'en',

    // have a common namespace used around the full app
    ns: ['translationsNS'],
    defaultNS: 'translationsNS',

    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    resources: { en: { translationsNS: {} } },
  });

export default i18n;
```

### Example test using this configuration

```javascript
import React from 'react';
import { Provider } from 'react-redux';
import { render, fireEvent } from '@testing-library/react';
import { I18nextProvider } from 'react-i18next';
import configureStore from 'redux-mock-store';
import ContactTable from './ContactTable';
import actionTypes from '../constants';
import i18n from '../i18nForTests';

const mockStore = configureStore([]);
const store = mockStore({ contacts: [ ] });

it('dispatches SORT_TABLE', () => {
  const { container } = render(
    <Provider store={store}>
      <I18nextProvider i18n={i18n}>
        <ContactTable />
      </I18nextProvider>
    </Provider>
  );
  fireEvent.click(container.querySelector('.sort'));
  const actions = store.getActions();
  expect(actions).toEqual([{ type: actionTypes.SORT_TABLE }]);
});
```

As translations aren't provided, `this.props.i18n.language` will be `undefined`. In case your application relies on that value you can mock resources by adding these lines to the object passed to init:

```
i18n
  .init({
    ...
    fallbackLng: 'en',
    resources: {
      en: {},
      de: {}
    }
  })
```

Now in your component `this.props.i18n.language` will return `en`.
