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

Or use [https://github.com/kadirahq/react-stubber](https://github.com/kadirahq/react-stubber) to stub i18n functionality:

```javascript
const tDefault = (key) => key;
const StubbableInterpolate = mayBeStubbed(Interpolate);
const stubInterpolate = function () {
  stub(StubbableInterpolate, (props, context) => {
    const t = (context && context.t) || tDefault;
    return (<span>{t(props.i18nKey)}</span>);
  });
};
```

Or mock it like:

```javascript
jest.mock('react-i18next', () => ({
  // this mock makes sure any components using the translate HoC receive the t function as a prop
  withTranslation: () => Component => {
    Component.defaultProps = { ...Component.defaultProps, t: () => "" };
    return Component;
  },
}));
```

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
    fallbackLng: 'en',

    // have a common namespace used around the full app
    ns: ['translations'],
    defaultNS: 'translations',

    debug: true,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },
  });

export default i18n;
```

### Example test using this configuration

```javascript
import React from 'react';
import { Provider } from 'react-redux';
import { mount } from 'enzyme';
import { I18nextProvider } from 'react-i18next';
import configureStore from 'redux-mock-store';
import ContactTable from './ContactTable';
import actionTypes from '../constants';
import i18n from '../i18nForTests';

const mockStore = configureStore([]);
const store = mockStore({ contacts: [ ] });

it('dispatches SORT_TABLE', () => {
  const enzymeWrapper = mount(
    <Provider store={store}>
      <I18nextProvider i18n={i18n}>
        <ContactTable />
      </I18nextProvider>
    </Provider>
  );
  enzymeWrapper.find('.sort').simulate('click');
  const actions = store.getActions();
  expect(actions).toEqual([{ type: actionTypes.SORT_TABLE }]);
});
```

As translations aren't provided, `this.props.i18n.language` will be `undefined`. In case your application relies on that value you can mock resources by adding these lines to the object passed to init:

```text
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

