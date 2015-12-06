# Pirates
---

### Properly hijack require

[//]: # "ProTip(tm): This is how you make a comment in markdown. Anything between the quotes is ignored."

---
[//]: # "I'm on the fence about having this here, as we don't actually use pirates"
[![Beware: Pirates!][pirates-badge]][pirates-link]
[![Version][version-badge]][npm-link]
[![Downloads][downloads-badge]][npm-link]
[![Build Status][build-badge]][build-link]
[![Dependency Status][deps-badge]][deps-link]
[![devDependency Status][devDeps-badge]][devDeps-link]
[![Commitizen friendly][cz-badge]][cz-link]
[![semantic-release][sr-badge]][sr-link]
[![MIT License][license-badge]][license-link]


[version-badge]: 	https://img.shields.io/npm/v/pirates.svg   "npm version"
[downloads-badge]: https://img.shields.io/npm/dm/pirates.svg "npm downloads"
[npm-link]:  http://npm.im/pirates                           "npm"

[license-badge]: https://img.shields.io/npm/l/express.svg    "MIT License"
[license-link]:  http://ariporad.mit-license.org             "MIT License"

[build-badge]: https://travis-ci.org/ariporad/pirates.svg                   "Travis CI Build Status"
[build-link]:  https://travis-ci.org/ariporad/pirates                       "Travis CI Build Status"

[deps-badge]: https://img.shields.io/david/ariporad/pirates.svg             "Dependency Status"
[deps-link]:  https://david-dm.org/ariporad/pirates                         "Dependency Status"

[devDeps-badge]: https://img.shields.io/david/dev/ariporad/pirates.svg      "devDependency Status"
[devDeps-link]:  https://david-dm.org/ariporad/pirates#info=devDependencies "devDependency Status"

[pirates-badge]: http://ariporad.link/pirates-badge "Beware: Pirates!"
[pirates-link]: https://github.com/ariporad/pirates "Beware: Pirates!"

[cz-badge]: https://img.shields.io/badge/commitizen-friendly-brightgreen.svg "Commitizen friendly"
[cz-link]: http://commitizen.github.io/cz-cli/                               "Commitizen friendly"

[sr-badge]: https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg
[sr-link]: https://github.com/semantic-release/semantic-release

[//]: # "This comes last, as it's really long"
[//]: # "These are currently disabled"
[coverage-badge]: https://coveralls.io/repos/ariporad/pirates/badge.svg?branch=master&service=github "Code Coverage"
[coverage-link]: https://coveralls.io/github/ariporad/pirates?branch=master                          "Code Coverage"

---

## Why?

Two reasons:
1. Babel and istanbul were breaking each other.
2. Everyone seemed to re-invent the wheel on this, and everyone wanted a solution that was DRY, simple, easy to use, 
and made everything Just Work™, while allowing multiple require hooks, in a fashion similar to calling `super`.

For some context, see [the Babel issue thread][] which started this all, then [the nyc issue thread][], where 
discussion was moved (as we began to discuss just using the code nyc had developed), and finally to [#1][issue-1] 
where discussion was finally moved.

[the Babel issue thread]: https://github.com/babel/babel/pull/3062 "Babel Issue Thread"
[the nyc issue thread]: https://github.com/bcoe/nyc/issues/70 "NYC Issue Thread"
[issue-1]: https://github.com/ariporad/pirates/issues/1 "Issue #1"

---

## Installation

    npm install --save pirates

---

## Usage

Using pirates is really easy:
```javascript
// my-module/register.js
var pirates = require('pirates');

// Instead of messing with require like this:
var old = require.extensions['.js'];
require.extensions['.js'] = function (mod, filename) {
  var compile = mod._compile;
  
  mod._compile = function (code, filename) {
    code = myLib.compileFile(code, filename);
    compile.call(mod, code, filename);
  }
  
  old(mod, filename);
}

// Now you can just do this!:
var revert = pirates.addHook(['.js'], function matcher(filename) {
  // Here, you can inspect the filename to determine if it should be hooked or 
  // not. Just return a boolean. Files in node_modules are automatically ignored.
  
  // TODO: Implement logic
  return true;
}, function hook(code, filename) {
  return code.replace('@@foo', 'console.log(\'foo\');');
});

// And later, if you want to un-hook require, you can just do:
revert();
```

Then when you add pirates to your module, add this badge to your README.md:

[![Beware: Pirates!](http://ariporad.link/pirates-badge)](https://github.com/ariporad/pirates "Beware: Pirates!")

```markdown
[![Beware: Pirates!](http://ariporad.link/pirates-badge)](https://github.com/ariporad/pirates "Beware: Pirates!")
```

---

## API

### pirates.addHook([exts], [matcher], hook);
Add a require hook. `exts` is optional and defaults to ['.js'], for convenience, if you only want to hook one extension,
you can just pass a string. `matcher` is a function which accepts a filename and returns a boolean indicating if the 
file should be hooked (note: files in node_modules are automatically ignored, regardless of `matcher`). `matcher` 
defaults to always true. `hook` is the actual require hook. It accepts two arguments, the code to 
transpile/instrument/whatever and the filename of the module. It must return the new code of the module. Returns a 
function which un-hooks require.


---

## Projects that use Pirates

See the [wiki page](https://github.com/ariporad/pirates/wiki/Projects-using-Pirates). If you add Pirates to your project,
(And you should! It works best if everyone uses it. Then we can have a happy world full of happy require hooks!), please
add yourself to the wiki.

---

## License

[MIT: ariporad.mit-license.org.](http://ariporad.mit-license.org)
