# Problem

Many Node.js modules include test and example code in their repos. This code needs to require the module itself.

But whereas modules depending on `foobar` can write `require('foobar')`, the test and example code within `foobar` cannot reliably require itself that way. It must use a relative path like `var foobar = require('../..')` instead.

This difference is minor but annoying in some scenarios. Example code copied from `foobar` into another module won't work until all the relative paths are updated to `'foobar'`. And if `foobar` is authored in TypeScript, the line `var foobar = require('../..')` loses all ambient type information about the module.

# Solutions
### Solution A: Do development work inside a `node_modules` folder
If you ensure your `foobar` module directory is a direct child of a directory called `node_modules`, then it can require itself using `require('foobar')` and Node's module resolution logic will successfully resolve such a call.

However, if you wish to treat your `node_modules` directories like an artefact that is always safe to clean/delete, this approach is not going to fly. Development work and git repos under `node_modules` may be inadvertently deleted.

### Solution B: Add a self-referencing module to the `node_modules` folder
A straight-forward solution is to add a file called `foobar.js` inside `foobar`'s `node_modules` subdirectory. This file simply contains `module.exports = require('..');`.

You could automate this step using a simple `echo ... ` command in an npm script, but that won't work cross-platform. Another way is to call a node module from an npm script, that let that do the job reliably.

Solution B is precisely what `require-self` provides.

# Usage

1. Add `require-self` as a devDependency to your module.
2. Call the `require-self` bin command in your module's `prepublish` npm script.

For example:
```
{
    "name": "foobar",
	...
    "scripts": {
        "build": "...",
        "prepublish": "npm run build && require-self"
    },
    "devDependencies": {
		...
        "require-self": "^0.x"
    }

```

Now all test and example code in the `foobar` module can use `var foobar = require('foobar');`.


## License

The MIT License (MIT)

Copyright (c) 2015 Troy Gerwien

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


