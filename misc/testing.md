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