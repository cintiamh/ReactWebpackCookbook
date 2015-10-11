* [1. Webpack](README.md)
* [2. React](REACT.md)

# React

## Basic Features

The greatest and worst feature of frameworks is that they sort of cage you in.
As long as you are doing what they expect you to do within their boundaries,
everything is fine. It is only after you start to reach beyond those boundaries
that problems begin to appear. In a library driven approach you aren't as bound.
Initially you might not be as fast or efficient but over time as problems become harder, you will have more choices available.

### Basics of JSX

React provides a component centric approach to frontend development. You will design your application as smaller components, each of which has it own purpose. Taken to the extreme a component may contain its logic, layout and basic styling.

We can mix normal JavaScript code within {}.

```javascript
<ul>{todoItems.map((todoItem, i) =>
    <li key={'todoitem' + i}><TodoItem owner={todoItem.owner} task={todoItem.task} /></li>
)}</ul>
```

The key property tells React the exact ordering of your items. If you don't provide unique keys for list items, React will warn you as it won't be able to guarantee the correct ordering otherwise.

This has to do with the fact that React actually implements Virtual DOM on top of actual DOM.

```javascript
var React = require('react');
var TodoItem = require('./TodoItem.jsx');

module.exports = React.createClass({
    getInitialState() {
        return {
            todoItems: [
                {task: 'Learn React',},
                {task: 'Learn Webpack',},
                {task: 'Conquer World',}
            ],
            owner: 'John Doe',
        };
    },

    render() {
        var todoItems = this.state.todoItems;
        var owner = this.state.owner;

        return <div>
            <div className='ChangeOwner'>
                <input type='text' defaultValue={owner} onChange={this.updateOwner} />
            </div>

            <div className='TodoItems'>
                <ul>{todoItems.map((todoItem, i) =>
                    <li key={'todoitem' + i}>
                        <TodoItem owner={owner} task={todoItem.task} />
                    </li>
                )}</ul>
            </div>
        </div>;
    },

    updateOwner() {
        this.setState({
            owner: e.target.value,
        });
    },
});
```

### Using a Mixin

Mixins provide us one way to encapsulate shared concerns into something which can be reused easily.

```javascript
// ReactLink is an addon so we have to load addons
var React = require('react/addons');
...
module.exports = React.createClass({
    mixins: [React.addons.LinkedStateMixin],
    ...
    render() {
        var todoItems = this.state.todoItems;

        return <div>
            <div className='ChangeOwner'>
                // no need to keep track with onChange
                <input type='text' valueLink={this.linkState('owner')} />
            </div>

            <div className='TodoItems'>
                <ul>{todoItems.map((todoItem, i) =>
                    <li key={'todoitem' + i}>
                        <TodoItem owner={owner} task={todoItem.task} />
                    </li>
                )}</ul>
            </div>
        </div>;
    },
});
```

### Testing

Try out Jest.

### Flux Architecture and Variants

In addition to View Components which we just discussed, Reflux introduces the concepts of Actions and Stores. The flow goes like this:

Components => Actions => Stores => Components

### Isomorphic Rendering

React allows you to prerender HTML at backend side. You can also hydrate some stores with pre-existing data making it possible to skip certain data queries altogether initially. Even web crawlers will be happy as they get some HTML to scrape.

## Configuring React JS

### Installing React JS

```
$ npm install react --save
$ npm install react-dom --save
```

### Using React JS in the code

component.js

```javascript
import React from 'react';

export default class Hello extends React.Component {
  render() {
    return <h1>Hello world</h1>;
  }
}
```

main.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import Hello from './component.jsx';

main();

function main() {
  ReactDOM.render(<Hello />, document.getElementById('app'));
}
```

build/index.html

```javascript
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
  </head>
  <body>
    <div id="app"></div>
    <script src="http://localhost:8080/webpack-dev-server.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

### Converting JSX

To use the JSX syntax you will need webpack to transform your JavaScript. We'll use Babel as it's nice and has plenty of features.

```
$ npm install babel-loader --save-dev
```

webpack.config.js

```javascript
var path = require('path');

var config = {
  entry: [
    'webpack/hot/dev-server',
    'webpack-dev-server/client?http://localhost:8080',
    path.resolve(__dirname, 'app/main.js')
  ],
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/, // A regexp to test the require path. accepts either js or jsx
        loader: 'babel' // The module to load. "babel" is short for "babel-loader"
      }
    ]
  }
};

module.exports = config;
```
