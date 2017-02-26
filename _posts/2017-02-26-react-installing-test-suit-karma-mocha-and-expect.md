---
date: '2017-02-26 00:07 +0100'
published: true
title: 'React - Test suit Karma, Mocha, and Expect '
category:
  - React
---
```
npm install karma@0.13.22 karma-chrome-launcher@0.2.2 karma-mocha@0.2.2 karma-mocha-reporter@2.0.0 karma-sourcemap-loader@0.3.7 karma-webpack@1.7.0 mocha@2.4.5 expect@1.14.0 react-addons-test-utils@0.14.6 --save-dev
```

**/karma.conf.js**

```js
var webpackConfig = require('./webpack.config.js')

module.exports = function (config) {
    config.set({
        browsers: ['Chrome'],
        singleRun: true,
        frameworks: ['mocha'],
        files: ['app/tests/**/*.test.jsx'],
        preprocessors: {
            'app/tests/**/*.test.jsx': ['webpack','sourcemap']
        },
        reporters: ['mocha'],
        client: {
            mocha: {
                timeout: '5000'
            }
        },
        webpack: webpackConfig,
        webpackServer: {
            noInfo: true
        }
    })
}
```

**/app/tests/app.test.js

```js
var expect = require('expect')


describe('App', () => {
    it('should properly run tests...', () => {
        expect(1).toBe(1)
    })
})
```

**/package.json***

```js
...
  "scripts": {
    "test": "karma start",
    "start": "node server.js"
  },
...
```

[example project](https://github.com/AndreeDeveldoeRay/reacttesting)
