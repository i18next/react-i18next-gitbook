# Testing

For testing purpose of your component you should export the pure component without extending with the translate hoc and test that:

```javascript
export MyComponent;
export default translate('ns')(MyComponent);
```

in the test test the myComponent export passing a t function mock:

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
  translate: () => Component => {
    Component.defaultProps = { ...Component.defaultProps, t: () => "" };
    return Component;
  },
}));
```

## Testing without stubbing

Alternatively, you could also test I18next without stubbing anything, by providing the correct configuration and fully wrapping your container in the provider.

### Example configuration for testing

```javascript
import i18n from 'i18next';

i18n
  .init({
    fallbackLng: 'cimode',
    debug: false,
    saveMissing: false,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    // react i18next special options (optional)
    react: {
      wait: false,
      nsMode: 'fallback', // set it to fallback to let passed namespaces to translated hoc act as fallbacks
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

