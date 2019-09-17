# Patch for 'dom-compare-temp' on Sage + Vue configurations

The default dev config of Sage's wordpress theme uses dom-compare-temp as a transitive dependency to live-update the page while you develop.
Using Vue, the node process crashes when this dependency encounters document fragments.

This patch allows to use Vue a bit better, at least without crashes. See here :

https://discourse.roots.io/t/sage-9-vue-js-no-jquery/14162/3

## Usage

Add dom-compare-temp as a dev dependency to be able to require it directly without ESLint complaining :

```yarn -D add dom-compare-temp```


### sage/resources/assets/build/config.js before

```js
const path = require('path');
const { argv } = require('yargs');
const merge = require('webpack-merge');
const desire = require('./util/desire');
// etc..
```

### sage/resources/assets/build/config.js after

```js
const path = require('path');
const { argv } = require('yargs');
const merge = require('webpack-merge');
const patch = require('./dom-compare-patch');
const domCompare = require('dom-compare-temp');
patch(domCompare);
const desire = require('./util/desire');
```

## Method

Since the code of `dom-compare-temp/compare` resides in an IIFE we're duplicating it here to be able to replace it. 

Crashes are then "resolved".
