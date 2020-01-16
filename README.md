# bindings-loader [![npm version](https://badge.fury.io/js/bindings-loader.svg)](https://badge.fury.io/js/bindings-loader)

A webpack loader for node addon modules that handles the recommended `require("bindings")("myaddon.node")` pattern. This borrows heavily from `awesome-node-loader` (huge credit to its contributors!) with how it handles node addons in your code, but extends the usability to include all dependencies in node_modules.

## Installation

Add the package to the `package.json` file:

```bash
$ npm install bindings-loader --save-dev
$ yarn add --dev bindings-loader
```

## Usage + Options

Always emits all `.node` files directly in the bundle root.

### `useDirname`

This option chooses in between `__dirname` and `path.dirname(process.execPath)`. (Default is `false` -> `__dirname`)

## Sample Config

```js
// webpack.config.js
module.exports = {
  externals: [
    // Don't try to pack referenced .node files
    function(context, request, callback) {
      if (/\.node$/.test(request)) {
        return callback(null, `commonjs ${request}`);
      }
      return callback();
    },
  ],
  module: {
    rules: [
    {
      test: /\.js$/,
      loader: "bindings-loader",
      options: {
        useDirname: !isElectronApp,
      }
    },
    ],
  },
};
```

## Behavior

Recognizes `require('bindings')('foo.node')`, uses (roughly) `require('bindings')({ path: true, bindings: 'foo.node' })` to get the built addon module, re-writes the require to `require("path").join(__dirname, 'foo.node')` when `useDirname = true`, and emits the referenced file at that bundle destination.

## Future Options Improvements
PRs welcome!

### `name`

This option would allow changing the file name in the output directory and allow usage of all placeholders defined in the [loader-utils](https://github.com/webpack/loader-utils/tree/v1.1.0#interpolatename) package.

### `rewritePath`

This option would need to remain `undefined` if you are building a package with embedded files, such as with Electron.
