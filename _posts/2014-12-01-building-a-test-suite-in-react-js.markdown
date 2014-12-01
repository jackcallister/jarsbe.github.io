---
layout: post
title: Building a test suite in React JS
---

 
I'm primarily a Rails developer but everyday I seem to be writing more and more front end JavaScript. While writing Ruby, tests are a given part of the process. This is not the case with JavaScript. I've always found that odd and I've also written enough JavaScript to intimately know how frustrating it can be. 

It's a delight writing Ruby with a test suite so why is it acceptable that most JavaScript goes into production without automated tests? I believe difficulty and fear to be the culprit here. It's simply hard to get started; the tooling, workflow and even what to test are foreign. Rather than continue to deal with difficult JavaScript applications I decided to learn how to develop a test suite.

I've been writing a lot of JavaScript using React, my test suite reflects this. React is also conducive to testing, hopefully this becomes more apparent with my explanations. 

## Application Setup

If you would like to follow along by writing code (I strongly suggest you do) you need to clone this boilerplate.

`git clone https://github.com/jarsbe/react-webpack-boilerplate test-suite`

`cd test-suite`

`npm install`

Firstly we need something to test. The application we'll create has two components; an App component which contains a list of clickable items and a Menu component which contains a list of all selected items. The menu component also has a counter for the total number of selected items. An item can be selected by clicking on it. 

If you are coding along replace the `component.js` file with the following code.

{% highlight js %}
var React = require('react'),
    App = require('./app');

React.render(<App />, document.body);
{% endhighlight %}

Then create the app.js and menu.js files.

{% highlight js %}
var React = require('react'),
    Menu = require('./menu');

var App = React.createClass({

  onSelectItem: function(index) {
    var item = this.props.items[index];
    
    this.setState({
      selectedItems: this.state.selectedItems.concat(item)
    });
  },

  getInitialState: function() {
    return {
      selectedItems: [] 
    };
  },

  getDefaultProps: function() {
    return {
      items: [{ title: 'Bread' }, { title: 'Milk' }, { title: 'Cheese' }]  
    };
  },

  render: function() {
    var listItems = this.props.items.map(function(item, i) {
      return <li key={"item" + i} onClick={t.onSelectItem.bind(null, i)}>{item.title}</li>
    }.bind(this));

    return (
      <div> 
        <ul>{listItems}</ul>

        <Menu items={this.state.selectedItems} />
      </div>
    );
  }
});

module.exports = App;
{% endhighlight %}

{% highlight js %}
var React = require('react');

var Menu = React.createClass({

  render: function() {
    var listItems = this.props.items.map(function(item, i) {
      return <li key={"selectedItem" + i}>{item.title}</li>
    });
    var count = listItems.length;
    return (
      <div>
        <ul>
          {listItems}
        </ul>
        <span>{count}</span>
      </div>
    );
  }
});

module.exports = Menu;
{% endhighlight %}

Take 5 minutes to read how the application works. 

## Testing Setup

To build a test suite we need tools for testing. Facebook uses Jest, which is a layer upon the Jasmine test framework. It offers automatic mocking and uses JSDom which allows for running tests in the command line (rather than browser). Along with Jest we need React Tools to transform any JSX during testing.

Fun fact, automocking in Jest is very helpful. You'll be testing components in isolation and Jest helps by mocking all dependencies. Take a look [here](https://facebook.github.io/jest/docs/automatic-mocking.html) for more information.

`npm install jest-cli react-tools --save-dev`

To transform any JSX a helper function is required. In a support folder create a `preprocessor.js` file to do the work.

{% highlight js %}
var ReactTools = require('react-tools');

module.exports = {
  process: function(src) {
    return ReactTools.transform(src);
  }
};
{% endhighlight %}

To use the preprocessor add some configuration inside the `package.json` file. It adds test script and informs Jest of the preprocessor function.

{% highlight js %}
"scripts": {
  "test": "jest"
},
"jest": {
  "scriptPreprocessor": "<rootDir>/support/preprocessor.js",
  "unmockedModulePathPatterns": [
    "<rootDir>/node_modules/react"
  ]
}
{% endhighlight %}

Next add a folder called `__tests__` to the root directory. Jest is magical enough to automatically run any test in any files sitting in this directory.

Just for sanity run `npm test`. This should run Jest and everything should pass with flying colours!

## Testing

Now the moment we've been waiting for writing actual test. Here's a spoiler - all the hard work has been done. The original goal of this post was to learn how to setup a JavaScript test suite. With that out of the way everything else is an implementation detail. Ending it here wouldn't be much fun though so on to the tests.

The simplest component is the Menu. It accepts only one property `items`. It generates a list from those `items`. The Menu also calculates a total `items` count.

The first order of business to get this component tested is setup a `menu-test.js` file inside the `__tests__` directory. It also needs some boilerplate code like so.

{% highlight js %}
jest.dontMock('../components/menu.js');

var React = require('react/addons'),
    Menu = require('../components/menu.js'),
    TestUtils = React.addons.TestUtils;

describe('Menu', function() {

  it('renders each item as a li', function() {
  
  });

  it('displays the items count', function(){
  
  });
});
{% endhighlight %}

Firstly Jest is told not to mock the Menu component. Then require all the necessary dependencies. Finally add two empty tests one to check each item is rendered as an array, the next test is to make sure the item count is correct.

To get these tests running you need to create an instance of the component, give it some items to render and finally select the DOM nodes to test.

{% highlight js %}
describe('Menu', function() {

  var items = [{ title: 'test' }, { title: 'test' }];

  var MenuElement = TestUtils.renderIntoDocument(
    <Menu items={items} />
  );

  var MenuElementDOM = TestUtils.findRenderedDOMComponentWithTag(MenuElement, 'div');
  var items = TestUtils.scryRenderedDOMComponentsWithTag(MenuElement, 'li');
  var count = TestUtils.findRenderedDOMComponentWithTag(MenuElement, 'span');  
...
{% endhighlight %}

The final piece of the puzzle is to add the expectations to each test. 

{% highlight js %}
it('renders each item as a li', function() {
  expect(items.length).toEqual(2);
});

it('displays the items count', function(){
  expect(count.getDOMNode().textContent).toEqual(2);
});
{% endhighlight %}

Nice and simple. Make sure there are two `li` nodes and that the items count is correct. The nice thing about React is that it's simple to test. The menu component is given data and the tests make sure it renders as expected. You can see this after testing the App component. 

{% highlight js %}
jest.dontMock('../components/app.js');

var React = require('react/addons'),
    App = require('../components/app.js'),
    TestUtils = React.addons.TestUtils;

describe('App', function() {
  var AppElement = TestUtils.renderIntoDocument(<App/>);

  var list = TestUtils.scryRenderedDOMComponentsWithTag(AppElement, 'ul')[0];
  var items = TestUtils.scryRenderedDOMComponentsWithTag(AppElement, 'li');

  it('has 3 default items', function() {
    expect(list.props.children.length).toEqual(3);
  });

  it('has no selected items', function() {
    expect(AppElement.state.selectedItems.length).toEqual(0);
  });

  describe('clicking an item', function() {
    it('adds it to the selected items', function() {
      TestUtils.Simulate.click(list[0]);
      expect(AppElement.state.selectedItems.length).toEqual(1);
    });
  });
});
{% endhighlight %}

The same pattern of create component, give data, expect output is followed above. There's also the added complexity of component state and function calls. Clicking an item in the App list should add that item to the Menu list. This happens via a state change in App. The only thing that needs to be expected is that clicking on an item adds it to the state's selected items array.

## Conclusion

Hopefully this has helped banish some of the fear behind testing JavaScript, especially so with the React library. Give an input, expect an output. 

A few things that helped while learning these tools was the documentation. I got started by looking at [Jest](https://facebook.github.io/jest/docs/tutorial-react.html#content) specifically the React section and then reading the API (which is very concise). Next I had a look through both the [Jasmine Guide](http://jasmine.github.io/2.0/introduction.html) and the [React Test Utils](http://facebook.github.io/react/docs/test-utils.html). It's a bit strange looking through 3 different sets of documentation, but they work surprisingly well together. 

Finally, you may have noticed everything here is using CommonJS modules. As a Rails developer this is very foreign! Don't fret, you can have your cake and eat it. I strongly suggest reading James McCann's post on incorporating [Webpack into Rails](http://www.jamesmccann.nz/2014/11/27/bundling-npm-modules-through-webpack-and-rails-asset-pipeline.html). 
