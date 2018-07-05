# Using with typescript

For Typescript users, if you are running into issues, such as `Uncaught TypeError: Cannot read property 'off' of undefined`, it's possible that you have not exported your own initialized i18next instance correctly.

Try the following:

```typescript
import * as i18n from 'i18next'
import * as XHR from 'i18next-xhr-backend'
import * as LanguageDetector from 'i18next-browser-languagedetector'
import config from '../src/config/config'

const instance = i18n
  .use(/* your settings */)
  .init({
    // your settings here
  })

export default instance
```

