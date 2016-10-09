# JavaScript Design Patterns

## Changing Expectations
1. Udacity notes on [Closure and Event Listeners](ClosureandEventList.md).
1. Spaghetti code: code gets messy when connected together without planning.
1. **Model Octopus View (MVO)**
  View: Stuff that users see and interact with that includes DOM elements, input elements, buttons, and images. Basically this is the user's interface to your application.
  Model: Where the data is stored, which includes data from server and users.
  Octopus: Sometimes called the controller, view model, presenter, and whatever..
1. `Window.localStorage` allows you to access a local storage object. Similar to sessionStorage, but it has no expiration time and gets cleared when the browsing session ends - that is when the browser is closed.
1. `JSON.stringify()` method converts a JavaScript value to a JSON string, optionally replacing values if a replacer function is specified, or optionally including only the specified properties if a replacer array is specified.
1. Model should be where you store the raw data. View should only used to present ready-data. And Octopus should be used to handle the data.

## Refractoring with Separations of Concerns
1. Separate views functionally whenever possible.
1. A good course practicing code refractoring.

## Using an Organization LIbrary
1. Library vs Framework:
  1. **Library**  - JS that's been packaged up e.g. **JQuery**. AJAX request, DOM manipulation and more are frequently used methods hence it's good to package these methods into a public library.
  1. **Organization Library** - Organization library is like JQuery, but instead of focusing on AJAX and DOM manipulation, they focus on application organization: MVC or MVVM and others.
  1. **Framework** - Not properly defined as every community has different definition. Some people say it's a collection of libraries, some say frameworks call view render methods, while the libraries require you to call them, there's a wide range of definitions.
  1. **They solve the same problem** : Organization library and Framework.
1. Example good libraries and frameworks: **Underscore**, **Angular JS** to do automatic updating of page when modifying data model and other advanced features.
1. Universal Organizational Concepts (concept remains the same for different framework):
  1. Models: representing your data, can be stupid or intelligent.
  1. Collections: smart arrays that are filled with models.
  1. View Model or Controller : the octopus.
  1. Views: Draw the interface. There's also Routers: Routers keep track of the state of the URL, which in a way is a view-like thing.
1. [Knockout.JS](http://www.knockoutjs.com):
  1. An organization library
  1. Model View ViewModel(Octopus)
  1. Declarative Bindings: bindings allow you to connect the View and Model in a direct and simple way.
    1. A lot of times when writing octopus we noticed it's doing the same thing which is passing the information from Model to View and vice versa. Using bindings in Knockout we can connect them. It is still going through the viewModel(octopus) to get the data, we are not connecting things directly. Instead go Viewmodel going to the View to grab stuff, we are putting the logic in the DOM in the form of bindings.
    1. Automatic UI refresh - Knockout's will update the View when the Model changes. And with the right declarative bindings, Knockout can update the Model when elements in the View change (such as input elements, checkboxes, etc).
    1. Dependency Tracking - Knockout allows you to create a relationship between parts of the Model, and will automatically update Model data that depends on other Model data when that other Model data changes.
  1. Knockout object i.e. observable: it's an object with special functions.  
  1. There isn't a great cross-browser way to run some code when a value updates normally. If a variable is changed, the View has no way of knowing, i.e. we need to run `view.render()` everytime. The way Knockout get around it, is by making it to run a function in order to change any values. E.g. if a number of a variable is changed, the function is run and update the model and also notify the users of the variable in the view that the number has changed.
  1. Smart array in knockout is known as observable arrays.
  1. Knockout will update the changed data in the view, rather than re-rendering everything.
  1. **Observables** and **observable arrays** are actually functions (which is also an object) with a bunch of keys and methods.
  1. Computed observable creates a value when they're accessed, rather than just retrieve it from somewhere.
  1. **Models** - Observables, Computed Observables, Observable Arrays
  1. **ViewModel
  1. **View** - Bindings
  1. **Observables, Computed Observables, foreach, Observables array, binding context**.
  1. With the addition of [the "with" binding](http://knockoutjs.com/documentation/with-binding.html), when accessing an element within "with" (clicking an image or something), this JavaScript the use of `this` would refer to the "with" binding, not the parent(i.e. usually the ViewModel).
  Hence in the **Cat Clicker** example, the HTML part looks like this(notice incrementCounter):
  ```html
  <div id="cat" data-bind="with: currentCat">
      <h2 id="cat-name" data-bind="text: name"></h2>
      <h3 id="cat-level" data-bind="text: level"></h3>
      <div id="cat-count" data-bind="text: clickCount"></div>
      <img id="cat-img" data-bind="click: $parent.incrementCounter, attr: {src: imgSrc}" src="" alt="cute cat">
      <h3>Nicknames:</h3>
      <ul data-bind="foreach: nicknames">
        <li data-bind="text: $data"></li>
      </ul>
  </div>
  ```
  whereas JS part which looks like this (use `this.clickCount()` rather than `this.currentCat().clickCount`):
  ```js
  this.currentCat = ko.observable(new Cat());
  this.incrementCounter = function() {
    this.clickCount(this.clickCount() + 1);
  };
  ```
