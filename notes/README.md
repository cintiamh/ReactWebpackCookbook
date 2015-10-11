# Webpack

Webpack takes your dependencies, puts them through loaders and outputs browser
compatible static assets.

It won't solve everything. It does solve the difficult problem of bundling,
however, and that's one worry less during development.

```javascript
module.exports = {
    entry: "./entry.js",
    output: {
        path: __dirname,
        filename: "bundle.js"
    },
    module: {
        loaders: [
            { test: /\.css$/, loader: "style!css" }
        ]
    }
};
```

## Getting Started

Create `package.json` file using `npm init`.

Install webpack as a dev dependency.

```
$ npm i webpack --save-dev
// And run with
$ node_modules/.bin/webpack
```

Directory Structure:

* /app
  * main.js
  * component.js
* /build
  * bundle.js (automatically created)
  * index.html
* package.json
* webpack.config.js

Basic webpack.config.js:

```javascript
var path = require('path');

module.exports = {
    entry: path.resolve(__dirname, 'app/main.js'),
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js',
    },
};
```

### Running your first build

app/component.js

```javascript
'use strict';

module.exports = function () {
    var element = document.createElement('h1');
    element.innerHTML = 'Hello world';
    return element;
};
```

app/main.js

```javascript
'use strict';

var component = require('./component.js');
document.body.appendChild(component());
```

Now if you run `webpack` in your terminal, bundle.js will be created inside build.

build/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
  </head>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

You can also use `html-webpack-plugin` to generate the html file.

package.json

```javascript
  "scripts": {
    "build": "webpack"
  }
```

Now you can run build with `npm run build`.

## Running a Workflow

### Setting up webpack-dev-server

```
$ npm i webpack-dev-server --save-dev
```

package.json

```javascript
{
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build"
  }
}
```

`webpack-dev-server` - Starts a web service on localhost:8080
`--devtool eval` - Creates source urls for your code. Making you able to pinpoint by filename and line number where any errors are thrown
`--progress` - Will show progress of bundling your application
`--colors` - Yay, colors in the terminal!
`--content-base build` - Points to the output directory configured

Go to http://localhost:8080 and you should see something.

When webpack-dev-server is running it will watch your files for changes. When it
happens it rebundles your project and notifies browsers listening to refresh.

webpack.config.js file:

```javascript
var path = require('path');

module.exports = {
  entry: [
    'webpack/hot/dev-server',
    'webpack-dev-server/client?http://localhost:8080',
    path.resolve(__dirname, 'app/main.js')
  ],
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'bundle.js'
  }
};
```

With this the browser will automatically refresh on change.

It's also possible to run the application from http://localhost:8080/webpack-dev-server/bundle
This will fire up a default index.html file that you do not control. It also
fires this file up in an iFrame allowing for a status bar to indicate the status
of the rebundling process.

### Requiring files

#### Modules

Webpack allows you to use different module patterns, but "under the hood" they
all work the same way. All of them also works straight out of the box.

##### ES6 Modules

```
import MyModule from './MyModule.js';
```

##### CommonJS

```
var MyModule = require('./MyModule.js');
```

##### AMD

```
define(['./MyModule.js'], function (MyModule) {
});
```

#### Understanding paths

Consider the following file structure:

* /app
  * /modules
    * MyModule.js
  * main.js (entry point)
  * utils.js

Requiring utils.js file from app/modules/MyModule.js:

```javascript
// ES6 relative path
import utils from './../utils.js';

// ES6 absolute path
import utils from '/utils.js';

// CommonJS relative path
var utils = require('./../utils.js');

// CommonJS absolute path
var utils = require('/utils.js');
```

The relative path is relative to the current file. The absolute path is relative
to the entry file (main.js).
