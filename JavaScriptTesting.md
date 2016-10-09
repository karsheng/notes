## JavaScript Testing

1. Use libraries like [Jasmine](http://jasmine.github.io/) to write testing suite.
1. Test [Example Code](../Dropbox/udacity/ud549) by Udacity
### Jasmine
1. `it` is used to identify a specification (spec). Spec returns `true`, it passes and vice versa.
1.  `describe` is used to identify a suite, which is a group of related specs - an organizational tool for indentation.
1. Writing a test:
  1. `expect(aFunction(var A, var B)).toBe(return)`;
  1. `expect(aFunction(var A, var B)).not.toBe(return)`;
1. Red-Green-Refactor cycle: write your test first, and they all fail since there's no code to make them pass. Then write the code required to make test pass. Once that's complete you can safely refactor your code as continue to add new features.
1. Usually separate the `src` files and `spec` files in separate folders and give the spec file `*Spec.js`. E.g. AddressBook.js in `src` and AddressBookSpec.js in `spec`.
1. use `beforeEach` to refactor code - runs a function defined within it before each test (`it`);
1. Testing asynchronous function is a bit different - need some way to inform the test framework that our asynchronous function has completed.
1. To fake asynchronous function for a test, use `setTimeout()`:
  ```JavaScript
  AddressBook.prototype.getInitialContacts = function(cb) {
    var self = this;

    setTimeout(function() {
      self.initialComplete = true;
      if (cb) {
        return cb();
      }
    }, 3)
  }
  ```
1. For asynchronous function testing, add a `beforeEach` function and pass `done` in the function, and within it's callback function call `done()`, this will signal to the framework that it's done and continue testing. Also need to signal to framework which test rely upon that asynchronous execution. To do this, use `done` to signal this.
E.g.
  ```JavaScript
    describe('Async Address Book', function() {
    var addressBook = new AddressBook();

    beforeEach(function(done){
      addressBook.getInitialContacts(function() {
        done();
      });
    });

    it('should grab initial contacts', function(done) {
      expect(addressBook.initialComplete).toBe(true);
      done();
    });
  });
  ```
