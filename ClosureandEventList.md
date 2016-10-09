# Closure and Event Listeners

### The problem:
Let's say we're making an element for every item in an array. When each is clicked, it should alert its number. The simple approach would be to use a for loop to iterate over the list elements, and when the click happens, alert the value of num as we iterate over each item of the array. Here's an example:

```JavaScript
// clear the screen for testing
document.body.innerHTML = '';
document.body.style.background="white";

var nums = [1,2,3];

// Let's loop over the numbers in our array
for (var i = 0; i < nums.length; i++) {

    // This is the number we're on...
    var num = nums[i];

    // We're creating a DOM element for the number
    var elem = document.createElement('div');
    elem.textContent = num;

    // ... and when we click, alert the value of `num`
    elem.addEventListener('click', function() {
        alert(num);
    });

    // finally, let's add this element to the document
    document.body.appendChild(elem);
};
```

If you run this code on any website, it will clear everything and add a bunch of numbers to the page. Try it! Open a new page, open the console, and run the above code. Then click on the numbers and see what gets alerted. Reading the code, we'd expect the numbers to alert their values when we click on them.

But when we test it, all the elements alert the same thing: the last number. But why?

What's actually happening
Let's cut out the irrelevant code so we can see what's going on. The comments below have changed, and explain what is actually happening.
```JavaScript
var nums = [1,2,3];

for (var i = 0; i < nums.length; i++) {

    // This variable keeps changing every time we iterate!
    //  It's first value is 1, then 2, then finally 3.
    var num = nums[i];

    // On click...
    elem.addEventListener('click', function() {

        // ... alert num's value at the moment of the click!
        alert(num);

        // Specifically, we're alerting the num variable
        // that's defined outside of this inner function.
        // Each of these inner functions are pointing to the
        // same `num` variable... the one that changes on
        // each iteration, and which equals 3 at the end of
        // the for loop.  Whenever the anonymous function is
        // called on the click event, the function will
        //  reference the same `num` (which now equals 3).

    });

};
```

That's why regardless of which number we click on, they all alert the last value of num.

How do we fix it?
The solution involves utilizing closures. We're going to create an inner scope to hold the value of num at the exact moment we add the event listener. There are a number of ways to do this -- here's a good one.

Let's simplify the code to just the lines where we add the event listener.

```JavaScript
var num = nums[i];

elem.addEventListener('click', function() {

    alert(num);

});
```

The num variable changes, so we have to somehow connect it to our event listener function. Here's one way of doing it. First take a look at this code, then I'll explain how it works.

```JavaScript
elem.addEventListener('click', (function(numCopy) {
    return function() {
        alert(numCopy)
    };
})(num));
```

The bolded part is the outer function. We immediately invoke it by wrapping it in parentheses and calling it right away, passing in num. This method of wrapping an anonymous function in parentheses and calling it right away is called an IIFE (Immediately-Invoked Function Expression, pronounced like "iffy"). This is where the "magical" part happens.

We're passing the value of num into our outer function. Inside that outer function, the value is known as numCopy -- aptly named, since it's a copy of num in that instant. Now it doesn't matter that num changes later down the line. We stored the value of num in numCopy inside our outer function.

Lastly, the outer function returns the inner function to the event listener. Because of the way JavaScript scope works, that inner function has access to numCopy. In the near future, num will increment, but that doesn't matter. The inner function has access to numCopy, which will never change.

Now, when someone clicks, it'll execute the returned inner function, alerting numCopy.

**The Final Version**

Here's our original code, but fixed up with our closure trick. Test it out!
```JavaScript
// clear the screen for testing
document.body.innerHTML = '';

var nums = [1,2,3];

// Let's loop over the numbers in our array
for (var i = 0; i < nums.length; i++) {

    // This is the number we're on...
    var num = nums[i];

    // We're creating a DOM element for the number
    var elem = document.createElement('div');
    elem.textContent = num;

    // ... and when we click, alert the value of `num`
    elem.addEventListener('click', (function(numCopy) {
        return function() {
            alert(numCopy);
        };
    })(num));

    document.body.appendChild(elem);
};
```
