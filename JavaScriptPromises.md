# JavaScript Promises

## Creating Promises
1. Native JavaScript offers promises which are recommended options to handle asynchronous work because it offers flexibility, intuitive syntax and easy error handling.
1. Asynchronous work happens at an unknown or unpredictable time as opposed to normal code which is synchrnous.
1. Network, Events, Threads and setTimeout() are asynchronous.
1. Callbacks are default JavaScript technique for asynchronous work - pass a function to another function and then call the callback function at some later time when some conditions have been met.
1. It's best practice to assume any operation could fail at any time especially with network requests.
1. Avoid callbacks within callbacks within callbacks - that's why you use `.then()`.
1. Course structure:
	1. Wrapping - syntax of constructing promises
	1. Thening - react to resolution of the promise if all goes well
	1. Catching - if something breaks
	1. Chaining - create long sequences of asynchronous work 
1. Further [notes](https://developers.google.com/web/fundamentals/getting-started/primers/promises) from Jake Archibald.
1. Four states a promise can have:
	1. **Fulfilled** (Resolved): It worked.
	1. **Rejected**: It didn't work.
	1. **Pending**: Still waiting..
	1. **Settled**: Something happened - fulfilled or rejected
1. Example promise constructor:
```JavaScript
	new Promise(function(resolve, reject) {
		resolve('hi');	// works
		resolve('bye');	// can't happen a second time as a promise can only settle once

		// events can fire many times though (when coupled with event listeners)
	});
```
1. Promises executes in the main thread which means that they are still potentially blocking - which could affect frame rate and result in jank.
1. Consider using promises when:
	1. doing asynchronous requests
	1. posting messages back and forth Web Worker - web workers run on separate threads and post data to the main thread which means they are asynchrounous
1. Wrapping - A promise is a try-catch wrapper around code that will finish at an unpredictable time.
```JavaScript
	var promise = new Promise(function(resolve[,reject]){
		var value = doSomething();
		if(thingWorked) {
			resolve(value);
		} else if (somethingWentWrong) {
			reject();
		}
	}).then(function(value) {
		return nextThing(value);
	}).catch(rejectFunction);

	// e.g.
	new Promise(function(resolve, reject) {
		var img = document.createElement('img');
		img.src = 'image.jpg';
		img.onload = resolve; 'to specify success, onload calls resolve which queues up the function passed to then to execute after the appendChild function below finishes executing'
		img.onerror = reject;
		document.body.appendChild(img); 'this function gets executed despite calling resolve first'
	})
	.then(finishLoading)
	.catch(showAlternateImage);
```
1. A Promise is a constructor - normally not stored as a variable.
1. `resolve` and `reject` are two callbacks then are used to specify when a promise has either fulfilled (because thingWorked) or rejected (because something went wrong).
1. When either resolve or reject has been called, the promise has been settled.
1. Any value passed to `resolve` or `reject` will be received as an argument by this subsequent `.then()` or `.catch()`.
1. If no argument is passed, then it simply receives `undefined`.
1. If a `Promise` is passed, the `Promise` executes first then whatever value it resolves to will be passed to the next link in the chain (`.then()` or `.catch()`).
1. `resolve` and `reject` have the same syntax.
```HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Promisify setTimeout</title>
</head>
<body>
	<h1>Did the promise finish?</h1>
	<div class="completion">Not yet</div>
	<script>
		function wait(ms) {
			/*
			Your code goes here!

			Instructions:
			(1) Wrap this setTimeout in a Promise. resolve() in setTimeout's callback.
			(2) console.log(this) inside the Promise and observe the results.
			(3) Make sure wait returns the Promise too!
			 */
			 return new Promise(function(resolve, reject) {
				console.log(this);
				window.setTimeout(function() {resolve()}, ms);
			 });
			
		};

		/*
		Uncomment these lines when you want to test!
		You'll know you've done it right when the message on the page changes.
		 */
		var milliseconds = 2000;
		wait(milliseconds).then(finish);


		// This is just here to help you test.
		function finish() {
			var completion = document.querySelector('.completion');
			completion.innerHTML = "Complete after " + milliseconds + "ms.";
		};
	</script>
</body>
</html>
```
1. JQuery's `document.readyState` has three possible states: `loading`, `interactive`(document loaded and been parsed, but subresources like images and stylesheets have yet to load i.e. DOM content loaded event), and `complete`. 
```HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Recreating .ready()</title>
	<style>
		body {
			margin: 0px;
		}
		.responsive {
			width: 100%;
			height: 100%;
		}
	</style>
</head>
<body>
	<h1>Are you doing work when the document is ready?</h1>
	<h3>Your wrapper should resolve before the huge image finishes loading. Turn on <a href="https://developers.google.com/web/tools/chrome-devtools/profile/network-performance/network-conditions">network throttling</a> to test.</h3>
	<div class="completion">Wrapper hasn't resolved yet!</div>
	<img class="responsive" src="San_Francisco_Market_Street_between_4th_and_5th_St.jpg" height="2448" width="3264" alt="SF Market Street">
	<p><a href="https://commons.wikimedia.org/wiki/File:San_Francisco_Market_Street_between_4th_and_5th_St.jpg">Photo: Andreas Praefcke (Own work) [GFDL (http://www.gnu.org/copyleft/fdl.html) or CC BY 3.0 (http://creativecommons.org/licenses/by/3.0)], via Wikimedia Commons</a></p>
	<script>
		function ready() {
			/*
			Your code goes here!

			Instructions:
			(1) Set network throttling.
			(2) Wrap an event listener for readystatechange in a Promise.
			(3) If document.readyState is not 'loading', resolve().
			(4) Test by chaining wrapperResolved(). If all goes well, you should see "Resolved" on the page!

			Make sure you return the Promise!
			 */
			 return new Promise(function(resolve, reject) {
				document.addEventListener('readystatechange', function() {
					/*
					Your code here too!
					 */
					if (document.readyState !== 'loading') {
						resolve();
					}
				}); 	
			 });
			
		};

		/*
		Don't forget to test your code!
		Call wrapperResolved when the DOM is ready.
		 */
		ready().then(wrapperResolved);

		// Just here for your testing
		function wrapperResolved() {
			var completion = document.querySelector('.completion');
			completion.innerHTML = "Resolved!";
		};
	</script>
</body>
</html>
```
1. Polyfills for promises:
	1. JQuery - [now satisfy Promises/A+ compliance](https://blog.jquery.com/2016/06/09/jquery-3-0-final-released/)
	1. Angular - uses Q style promises, mostly the same as native promises. 
	1. Angular 2 - takes advantage of native JS promises.
1. As of Dec 2015, native promises are safe to use with every major browser except for Opera mini and Internet Explorer. Include a polyfill or some other kind of fall-back on your production sites.
1. New API taking advantage of promises e.g. recommended strategry to interacting with the new Service Worker API. 
1. Service Workers allows you to add a powerful layer of control between your app and the network, and that means that you can actually create apps that work offline. More info [here](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers). 
1. [Fetch API](https://davidwalsh.name/fetch) simplifies XHR - natively supported by Chrome, Firefox Opera and Android beowsers and growing and it's built on native promises.
1. `fetch` by itself is a promise - you can return it the way you normally would with a promise.
1. You can chain promises e.g. `.then()`s together i.e. you can `.then()` initial promises, you can also `.then()` off of `.then()`s because they are also promises.
1. Developer commonly use the term "**thenable**" to describe promise in `.thens()`.
1. MDN Promise [documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#Methods).

## Chaining Promises
1. You can chain `.then`s.
1. Error handling strategies:
```JavaScript
	get('example.json')
	.then(resolveFunc)
	.catch(rejectFunc);

	// is equivalent to

	get('example.json')
	.then(resolveFunc)
	.then(undefined, rejectFunc);

	// full function signature
	get('example.json').then(resolveFunc, rejectFunc); // if any previous promise is rejected, the rejectFunc gets called. If they resolve then resolveFunc gets called.

	// if there is no resolve func and the promise before this then resolves, this then gets skipped over and the next then is called
	get('example.json').then(undefined,rejectFunc).then(...);
```
```JavaScript
	// can call both resolveFunc and rejectFunc
	get('example.json')
	.then(resolveFunc)
	.catch(rejectFunc);

	// can't call both if in the same .then()
	get('example.json').then(resolve, rejectFunc);
```
1. resolve doesn't always mean promise succeeded - [see more](https://jakearchibald.com/2014/resolve-not-opposite-of-reject/)
1. Problem with code below is that async request like `getJSON` can finish at different time so there is no guarantee the order that these thumbnails would get created.
```JavaScript
	getJSON(url)
	.then(function(response) {
		response.results.forEach(function(url) {
			getJSON(url)
			.then(createPlanetThumb);
		})
	});
```
1. Control the order of promises resolve - sequence
	1. Promises with `forEach()`
```JavaScript
	// problem with this approach is that it executes in series not parallel - sloooooooowww
	getJSON('../data/earth-like-results.json')
	    .then(function(response) {
	      console.log(response.results);

	      var sequence = Promise.resolve();

	      response.results.forEach(function(url) {
	        sequence = sequence.then(function() {
	          return getJSON(url);
	        })
	        .then(createPlanetThumb);
	      });
    })
```
	1. Promises with `.map()` - an array method that accepts a function, and returns an array - which is the result of executing this function against every element in this array. -- **apparently there's no guarantee to order when using `.map` then why teach this as one of the strategy???**
	```JavaScript
		getJSON('../data/earth-like-results.json')
	    .then(function(response) {
	      addSearchHeader(response.query);
	      var sequence = response.results;
	      sequence.map(function(url) {
	        getJSON(url).then(createPlanetThumb);
	      });
	    })
	```
	1. `Promise.all()` takes an array of promises, executes them, and return array of values in the same order as the original promises. `.all()` fails fast in that it will reject as soon as the first promise rejects, without waiting for the rest of the promises to settle. This means even if one of the promises rejects, the whole `.all()` rejects. But once every promises has resolved, then the next `then` in the chain gets the array of values.
	```JavaScript
	 getJSON('../data/earth-like-results.json')
    .then(function(response) {
      addSearchHeader(response.query);
      var arrayOfPromises = []
      response.results.map(function(url) {
        arrayOfPromises.push(getJSON(url));
      });
      return Promise.all(arrayOfPromises);
    })
    .then(function(planetObjects){
      planetObjects.forEach(function(data) {
        createPlanetThumb(data);
        });
    });
	```
	```JavaScript
	// this works too!
	getJSON('../data/earth-like-results.json')
	.then(function(response) {
      return Promise.all(response.results.map(getJSON));
    })
    .then(function(planetObjects){
      planetObjects.forEach(function(data) {
        createPlanetThumb(data);
        });
    });
    ```
1. There's a bonus section in this course where the course developer shows how to execute once promises before them have <settled class=""></settled>