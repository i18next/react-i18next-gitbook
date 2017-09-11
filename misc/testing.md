# Testing

For testing purpose of your component you should export the pure component without extending with the translate hoc and test that:

```js
export MyComponent;
export default translate('ns')(MyComponent);
```

in the test test the myComponent export passing a t function mock:

```js
import { MyComponent } from './myComponent';

<MyComponent t={key => key} />
```

Or use [https://github.com/kadirahq/react-stubber](https://github.com/kadirahq/react-stubber) to stub i18n functionality:

```js
const tDefault = (key) => key;
const StubbableInterpolate = mayBeStubbed(Interpolate);                                                                              
const stubInterpolate = function () {                                                                                                
    stub(StubbableInterpolate, (props, context) => {                                                                             
        const t = (context && context.t) || tDefault;                                                                               
        return (<span>{t(props.i18nKey)}</span>);                                                                                    
    });                                                                                                                          
};
```

## Testing without stubbing

Alternatively, you could also test I18next without stubbing anything, by providing the correct configuration and fully wrapping your container in the provider. 

### Example configuration for testing

```javascript
import i18n from 'i18next';

i18n
  .init({
    fallbackLng: 'cimode',
    debug: true,
    saveMissing: false,

    interpolation: {
      escapeValue: false, // not needed for react!!
    },

    // react i18next special options (optional)
    react: {
      wait: false,
      nsMode: 'default', // set it to fallback to let passed namespaces to translated hoc act as fallbacks
    },
  });


export default i18n;
```

### Example test using this configuration

```javascript
import { Provider } from 'react-redux';
import { mount } from 'enzyme';
import { I18nextProvider } from 'react-i18next';
import configureStore from 'redux-mock-store';
import ContactTable from './ContactTable'; // This is a React container under test, which imports a decorated component
import actionTypes from '../constants';
import i18n from '../i18nForTests';

const mockStore = configureStore([]);
const store = mockStore({ contacts: [] });

it('dispatches SORT_TABLE', () => {
  const enzymeWrapper = mount(
    <Provider store={store}>
      <I18nextProvider i18n={i18n}>
        <ContactTable />
      </I18nextProvider>
    </Provider>
  );
  console.log(enzymeWrapper.html());                                                                                               enzymeWrapper.find('th').simulate('click');
  const actions = store.getActions();
  expect(actions).toEqual([{ type: actionTypes.SORT_TABLE }]);
});
```
