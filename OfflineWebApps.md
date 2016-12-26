# Offline Web Applications
## Benefits of Offline First
1. Internet isn't always with us.
1. Progressive web app - works like native apps but it's actually a web page.
1. When you open the app - if there's no connection you will see connection error message.
1. But worse - when internet connection is poor, i.e. Lie-Fi - it's takes forever to load and user sees nothing.
1. Sometimes, your phone thiks it's acing it in the connection department but nothing is getting through - the problem is further down the line: somewhere between you and the server, where the app and data lives.
1. There's a lot between your phone and the server - phone sends request to the Wifi router or cell tower, then onto the ISP, through intermediate proxies and potentially across to the other side of the world, and eventually the request reaches the destination server and go back to your phone through the same route again.
1. If something along the way fails, the whole thing fails, if something along the way runs slowly, the whole thing runs slow - therein lies Lie-Fi.
1. Loads of stuff can go wrong and we can't predict them.
1. Offline first - getting as many things as possible using stuff already on the user's device in caches and such. We might still go to the network, but we're not going to wait for it as we'll get stuff from the cache as much as we can and then update the page if we finally get content from the network.
1. When we get that fresh data from the network we can update what the user is looking at and also save that new data into the cache for next time.
1. If we can't get fresh data - we stick with what we've got - as it's better than nothing.
1. Things that slow down or prevent users receiving data from your website:
	1. Poor signal
	1. A misconfigured proxy
	1. Fault in the mobile network
	1. Busy network
	1. Server being DDOSed
	1. Bug in server code
	1. Wifi captive portal
	1. The moon's gravitational pull
1. Online first works well good connectivity and offline, but bad during poor connectivity / Lie-Fi.
1. Wittr - starter code works like this:
	1. When navigate to Wittr, the browser makes a request for some HTML
	1. Like all web requests, this goes via the browser's HTTP cache, if there's no match there, it continues on to the internet.
	1. The response makes it's way back to the browser, the HTML the browser receives tells it needs some CSS so that's fetched.
	1. Once that arrives, we get our first render, a page full of content.
	1. At the same time the browser downloaded the CSS and also requesting some JavaScript and when that arrives it opens a websocket, a persistent connection that let's the server continually stream newer posts as they arrive - live update!
1. Service worker comes to the rescue!

## Introducing Service Work
1. Service worker is basically a simple JavaScript file that sits between you and network requests. It's a type of web worker meaning it runs separately from your page. It isn't visible to the user. It can't access the DOM but it does control pages and by intercepting requests as the browser makes them. From there you can do whatever you want.
1. You can send request off to the network, as per usual or you can skip the network and go to some kind of cache or create a custom random response, or any combination of those things.
```JavaScript
	// this gives location of your service worker script
	
	navigator.serviceWorker.register('/sw.js')
	.then(function(reg) {console.log('Yay!');}) // it returns a promise so you get callbacks for success 
	.catch(function(err) {console.log('Boo!');}); // and failure...
```
1. If you call register when the service worker is already registered, that's fine as the browser won't re-register it and return a promise for the existing registration.
```JavaScript
	// You can also provide a scope - it will control any page whose URL begins with the scope and ignore any that don't
	navigator.serviceWorker.register('/sw.js', {
		scope: '/my-app/'
	})
	// it will control
	// /my-app/
	// /my-app/hello/world/

	// it won't control
	// /
	// /another-app/
	// /my-app    ...Yes! Even this as it doesn't have the trailing slash so it counts as a shaloow URL
```
1. You can have multiple service workers with different scopes which is really handy for something like GitHub Pages when multiple projects share the same origin - scopes let you have a different service worker for each project.
1. The default scope is determined by the location of the service worker script. It's basically the path that the scripts sits in. Usually you don't need to set the scope and just put the service worker script in the right place. E.g. SW URL ---> Default Scope:
	1. /foo/sw,js ---> /foo/
	1. /foo/bar/sw.js ---> /foo/bar/
	1. /sw.js ---> /
1. What happens within the service worker	? - you listen to particular JS events. Just like other JS events, you can react to them or even prevent the default and do your own thing.
```JavaScript
	self.addEventListener('install', function(event) {
		// ...
	});
```
1. Browser support -- check [Is Service Worker Ready?](https://jakearchibald.github.io/isserviceworkerready/)
1. Wrap code in a simple feature detect to make sure it's safe progressive enhancing way;
```JavaScript
	if (navigator.serviceWorker) {
		navigator.serviceWorker.register('/sw.js');
	}
```
1. Babel lets you use some of the newer JavaScript features.
1. JS doesn't have private methods, but it's common practice to start methods with an underscore if they'll only ever be called by other methods of this object.
```JavaScript
	register service worker at the start 
	if(!navigator.serviceWorker) return;

	navigator.serviceWorker.register('/sw.js')
	.then(function() {
		console.log('Registration worked!');
	})
	.catch(function() {
		console.log('Registration failed!')
	});
```
1. At the serviceWorker script:
```JavaScript
	// log any fetch requests (HTML, CSS and stuff)
	self.addEventListener('fetch', function(event) {
	  console.log(event.request);
	});
```
1. The scope of the service worker restricts the oages it controls, but it will intercept pretty much any requests made by controlled pages regardless of URL. 
1. You can also messued around with these requests: changing headers or responding with something entirely different. Because of this service worker is limited to only HTTPS as its dangerous if the data is not encrypted - localhost is exempt from this rule.
1. Service worker has a different life cycles to pages:
	1. Add code to register service worker then hit refresh
	1. Hitting refresh spawned a new window client, then the request went off to the network, we got a response back, and the old window client went away
	1. There is a overlap between the old page and the new page when you hit refresh e.g. if the response came back indicating that the browser should save the resource to disk via download dialog, then the old window would have stayed around. But if the response was a page then we got rid of the old one.
	1. From the page the requests went out for CSS, images, and also the JS which registered the service worker etc. You won't see request log from this page because the service worker only takes control of the pages when they're loaded, and this page was loaded before the service worker existed. That means any additional requests this page makes will bypass the service worker.
	1. Then we refresh the page, creating a new window client, and because service worker was up and running it took control of it. Therefore the request went to the service worker as did all of the subresources. So that explains why it took two refresh to see logged requests.
	1. But then we changed the log to below and still see the requests logged rather than 'Hello World' :
```JavaScript
	// log any fetch requests (HTML, CSS and stuff)
	self.addEventListener('fetch', function(event) {
	  console.log(Hello World!);
	});
```
	1. This is because while the new service worker is registered, it's not taking control because the old service worker still associates with the old page (much like the download example above), hence the new service worker won't take over until all pages using the current version are gone. 
	1. This ensure only one version of your site running a given time like native apps.
	1. To make the current version no longer in use, you either close the page using it or navigate to a different page. When it does that the new service worker takes over and future page loads will go through the new one.
	1. This process is actually the same as Chrome uses. Chrome downloads the update in the background but it won't take over until the browser closes and opens again. It notifies the user that there's an update ready by changing the color of the hamburger tool icon.
	1. When the browser refetches a service worker looking for updates, it will go through the browser cache as pretty much all requests do. Hence it is recommended keeping a cache time on your service workers short - in fact cache time zero.
	1. As a safety precaution, if you set your service worker script to cache for more than a day, the browser will ignore that and just set the cache to 24 hours. That doesn't mean that service worker will stop working after 24 hours, it just means that update checks will bypass the browser cache if the service worker it has is over a day old.
1. Google Chrome Canary is a browser that includes tools under development by Google.
1. Resources tab is now Application in the new Chrome.
1. You can use the Application tab to check the status of service worker e.g. if there's any waiting.
1. To make it easier during development for updating service worker (without having to navigate to a different site or close and reopen the page)
	1. Shift + Reload - this loads the page but bypasses the service worker, this behavior is part of the service worker spec, so it should work in any browser that supports service workers. This is handy because:
		1. It's a quick way to test changes that are unrelated to the service worker such as minor CSS changes.
		1. Because this tab is no longer controlled by the service worker, it lets the waiting service worker takes over.
		1. If you then refreshes normally the new service worker will be in controlled.
	1. An even easier way: there's supposed to be checkbox under service worker that allows you to force update on page load, but I can't find it.
1. Hijacking requests - catch a request as it hits the service worker and respond ourselves so nothing goes to the network -- this is important for offline first:
```JavaScript
	self.addEventListener('fetch', function(event) {
		// this tells the browser we are going to handle the response ourselves
		event.respondWith(
			// this takes a response object or a promise that resolves with a response
			// you can use new Response() to create a response
			// the first parameter is the body of the response which can be a blob, a buffer etc but at its simplest it can take a string
			// the second parameter is an object and the header's property takes an object of headers and values
			event.respondWith(
				new Response('<span class="a-winner-is-me">Hello World!</span>', {
				headers: {'Content-Type': 'text/html'}
				})
			);
		);
	});
```
1.  XMLHttpRequest - XHR old. Use `fetch()`
1. As event.respondWith also takes a promise that resolves to a response, you can also do this:
```JavaScript
	self.addEventListener('fetch', function(event) {
		event.respondWith(
			fetch('/imgs/dr-evil.gif')
		);
	});
```
1. Intercepting .jpg with .gif
```JavaScript
self.addEventListener('fetch', function(event) {
  // TODO: only respond to requests with a
  // url ending in ".jpg"

  var requestURL = event.request.url;

  if (requestURL.endsWith('.jpg')) {
  	event.respondWith(
    	fetch('/imgs/dr-evil.gif')
  	);
  }
});
```
1. Handle page not found and error:
```JavaScript
	self.addEventListener('fetch', function(event) {
		event.respondWith(
			fetch(event.request).then(function(response) {
				if (response.status == 404) {
				 return new Response("Whoops, not found");
				}
				return response;
			})
			.catch(function() {
				return new Response("Uh oh, that totally failed!");
			})
		);
	});
```
1. To load an app without using the network, we need somewhere to store the HTML, CSS, JS, images, web font etc by using cache API.
1. The cache API gives us this caches object on the global:
```JavaScript
	// caches.open returns a promise for a cache of that name
	// if the cache of that name was not opened before, it creates one and returns it
	caches.open('my-stuff').then(function(cache) {
		//...
	});
```
1. A cache box contains request and response pairs from any secure origin. We can use it to store fonts, scripts, images, or anything from our origin or elsewhere on the web.
1. To add cache items, use:
```JavaScript
	cache.put(request, response);
	
	// this takes an array of requests or URLs, fetches them and puts the request-response pairs into the cache.
	// this operation is atomic - if any of these fail to cache, none of them are added
	cache.addAll([
		'/foo',
		'/bar'
	]);
	
	// to get something out of the cache, call cache.match
	// this will return a promise for a matching response if one is found, or null otherwise. 
	cache.match(request); // can parse a request or URL
	caches.match(request); // does the same but tries to find a match in any cache, starting with the oldest
```
1. `.addAll()` uses `fetch()` under the hood, so bear in mind that requets will go via the browser cache.
1. When a browser runs a service worker for the first time, an event is fired within it, the install event.
1. The browser won't let this new service worker take control of pages until it's install phase is completed, and we're in control of what that involves. We use this opportunity to get everything we need from the network and create cache for them.
```JavaScript
	self.addEventListener('install', function(event) {
	  var urlsToCache = [
	    '/',
	    'js/main.js',
	    'css/main.css',
	    'imgs/icon.png',
	    'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
	    'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
	  ];

	  event.waitUntil(
	    // TODO: open a cache named 'wittr-static-v1'
	    // Add cache the urls from urlsToCache
	    caches.open('wittr-static-v1').then(function(cache) {
			return cache.addAll(urlsToCache); // returning the promise addAll returned for further action
		})
	  );
	});
```
1. The following fetches the request from cache first if there's any matches, if they aren't, it will fetch from network. First step to offline first!
```JavaScript
self.addEventListener('fetch', function(event) {
  // TODO: respond with an entry from the cache if there is one.
  // If there isn't, fetch from the network.
    event.respondWith(
      caches.match(event.request).then(function(response){
        if (response) return response;
        return fetch(event.request);
      })
    );  
});
```
1. Updating the static cache - the problem with the approach above is that when the files are updated but because there's new service worker to install and hence the cache you see won't be updated. So we need to capture these changes:
	1. Say a CSS is updated, make a change to the service worker
	1. Browser treats this updated worker as a new version and it gets it's own install event where it'll fetch the files including the updated CSS and put them in a new cache - with a new name for the cache
	1. Once old service worker release, we delete the old cache so the next page load gets resources from the cache it will get the updated CSS
```JavaScript
	// fires when this service worker becomes active, when it's ready to 
	// control pages and the previous service worker is gone
	self.addEventListener('activate', function(event) {
		// ...
	});

	// use event.waitUntil to signal the length of the process

	// while activating the browser will queue other service worker events such as fetch so by the time your service worker receives it's first fetch you know you have the caches how you want them.

	// delete caches using caches.delete(cacheName);

	// get names of all caches using caches.keys();

	// both methods above return promises
```
```JavaScript
	self.addEventListener('activate', function(event) {
	  event.waitUntil(
	    // TODO: remove the old cache
	    caches.keys().then(function(cacheNames) {
	      return Promise.all(
	        cachesNames.filter(function(cacheName) {
	          return cacheName.startsWith('wittr-') &&
	                cacheName != staticCacheName;
	        }).map(function(cacheName) {
	          return cache.delete(cacheName); 
	        })        
	      );
	    }) 
	  );
	});
```
1. Code above:
	1. `activate` event fires when it's ready to take over the old service worker
	1. `event.waitUntil()` takes a promise as parameter. In service workers, extending the life of an event prevents the browser from terminating the service worker before asynchronous operations within the event have completed.
	1. `caches.keys()` returns a Promise that resolves to an array of Cache keys in this case `cacheNames`
	1. `Promise.all()` takes an array of promises and execute them and return an array of values. 
	1. `cacheNames.filter()` here returns an array of cacheName with the specified criteria.
	1. `.map()` takes the function and execute them for each value in the array (which was returned from `cacheNames.filter()`), in this case it returns promise as the value for the array. This is then parsed to `Promise.all()`
1. If one of these resources had a cache time of say a year, the update woud just be fetched from the HTTP cache, which means you won't get any changes you made. In the development server, all the resources are set to have a cache age of zero i.e. they don't cache
1. In production, it is recommended versioning as part of your resource names like this:
```JavaScript
	'/',
	'js/main-a494ce.js',
	'css/main-2fee7ea.css',
```
where the version number is generated from the content of the file. Then you can give these resources a cache time of a year or more - so that when you update your CSS a build script could automatically update your service worker changing the URL to this CSS. 
1. Version number could also be all generated based on the things that caches. Versioning resources like this and giving them a long cache time isn't advice specific to service workers. It's just general good caching practice. 
1. Reminder: when new worker is discovered it waits until all pages using current version go away before it can take over - and that could be a long time.
1. Our goal is to tell user once an update has been found, and give them a button to ignore it or refresh the page to get the new version
1. When you register a service worker it returns a promise, that promise fulfills with a service worker registration object - this object has properties and methods relating to the service worker registration.
```JavaScript
	navigator.serviceWorker.register('/sw.js').then(function(reg) {
		reg.unregister();
		reg.update();
		// below points to service worker object or be null
		reg.installing;
		reg.waiting;
		reg.active;
		
		// the registration object will emit an event when a new update is found
		reg.addEventListener('updatefound', function() {
			// reg.installing has changed (to new worker)
		});

		var sw = reg.installing;
		console.log(sw.state);
		// could log:
		// 'installing'
		// 'activating'
		// 'activated'
		// 'redundant' - thrown away because superseded by newer worker or fails to install

		sw.addEventListener('statechange', function() {
			// sw.state has changed 
		});

		if (!navigator.serviceWorker.controller {
			// page didn't load using a service worker
		}

		if (reg.waiting) {
			// there's an update ready and waiting!
		}

		if (reg.installing) {
			// there's an update in progress
			reg.installing.addEventListener('statechange', function() {
				if (this.state == 'installed') {
					// there's an update ready!
				}
			});
		}
		
		// listen to if there's any update found
		reg.addEventListener('updatefound', function() {
			reg.installing.addEventListener('statechange', function() {
				if (this.state == 'installed') {
					// there's an update ready
				}
			})
		})
	})
```
1. Call `self.skipWaiting()` to not queue behind another service worker and just take over. Call this when the user hits the reset button.
1. To send the signal from the page to the waiting service worker using `.postMessage()`
```JavaScript
	// from a page:
	reg.installing.postMesssage({
		foo: 'bar' 
	});

	// in the service worker:
	self.addEventListener('message', function(event) {
		event.data; // {foo: 'bar'}
	});

	navigator.serviceWorker.addEventListner('controllerchange', function() {
		// navigator.serviceWorker.controller has changed
		// you can use this to signal that we should reload the page
	});
```
1. E.g. If the update lands before the user interacted with the page, tells service worker to take over and refreshes instantly - if the user hasn't interacted, refreshing instantly isn't disruptive in this case
1. Ask server for change log between new version and the current version. If you update contains only minor things maybe don't bother user at all and let them get the update naturally or if update contains urgent security fix then you might consider reload the page automatically.
1. A way to cache skeleton - check caching the skeleton video:
```JavaScript
self.addEventListener('fetch', function(event) {
  // TODO: respond to requests for the root page with
  // the page skeleton from the cache
  var requestUrl = new URL(event.request.url);

  if (requestUrl.origin === location.origin) {
    if (requestUrl.pathname === '/') {
      event.respondWith(caches.match('/skeleton'));
      return; // no need to go to network for fallback as skeleton is part of the install set
    }
  }
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
```
## IndexedDB and Caching
1. Use database to retrieve new data (e.g. posts), retain existing data (current posts) and remove old data (older posts).
1. Web platform has a database called IndexDB - apparently with bad reputation
1. Generally you only have one database per app - which contains multiple objects stores, generally one for each kind of thing you want to store (posts, preferences, users etc.)
1. Assign propertu of the values to be the key and the key must be unique within an object store:
```JavaScript
{name: 'Jane', age: 41}
{name: 'Sam', age: 41}
{name: 'Julian', age: 11}
```
1. All read or write operations in IndexDB must be part of a transaction
1. Later you can get, set, add, remove, iterate over items in the object stores as part of a transaction. 
1. All read or write operations in IndexDB must be part of a transaction. This means that if you create a transaction for a series of steps and one of the actions fail, none of them are applied. The state of the databsed would be as if none of the steps happened.
1. You can also create indexes within an object store, which provides a different view of the same data ordered by particular properties. The model here is similar to a lot of databases which makes a lot of sense.
1. The IndexDB API is a little horrid and often creates spaghetti code as it's asynchronous but predates promises.
1. Use [IndexedDB Promised](https://github.com/jakearchibald/idb) - a small library that mirror the IndexDB API but uses promises rather than events. But other than that it's the same as IndexedDB.
1. A simple way to create and store stuff in a IndexedDB using the promised library by Jake Archibald:
```JavaScript
	import idb from 'idb';
	// this function (idb.open) will be called if the browser hasn't heard of this db before
	// or the version it knows about is less than the number here
	// the upgradeDb parameter which we used to define the database
	// to ensure the database integrity, this is the only place you can create
	// and remove objects stores and indexes
	// refer to the full IndexedDB API for more info
	// mamy of the methods in the promised library behaves like the native IDB except they returns a promise - which make it way more useable
	// refer to the indexDB promised documentation
	// so far in the first two lessons, you need to run /idb-test/ in order to have this executed
	var dbPromise = idb.open('test-db', 1, function(upgradeDb) {
	  var keyValStore = upgradeDb.createObjectStore('keyval');
	  keyValStore.put("world", "hello"); // note: key is 'hello' and value is 'world'
	});
	// read "hello" in "keyval"
	dbPromise.then(function(db) {
	  var tx = db.transaction('keyval');
	  var keyValStore = tx.objectStore('keyval');
	  return keyValStore.get('hello');
	}).then(function(val) {
	  console.log('The value of "hello" is:', val);
	});
	// set "foo" to be "bar" in "keyval"
	dbPromise.then(function(db) {
	  var tx = db.transaction('keyval', 'readwrite');
	  var keyValStore = tx.objectStore('keyval');
	  keyValStore.put('bar', 'foo');
	  return tx.complete; // is a promise that fulfills if and when the transaction completes, and it rejects if it fails
	}).then(function() {
	  console.log('Added foo:bar to keyval');
	});
	dbPromise.then(function(db) {
	  // TODO: in the keyval store, set
	  // "favoriteAnimal" to your favourite animal
	  // eg "cat" or "dog"
	  var tx = db.transaction('keyval', 'readwrite');
	  var keyValStore = tx.objectStore('keyval');
	  keyValStore.put('cat', 'favoriteAnimal');
	  return tx.complete;
	}).then(function() {
	  console.log('Added!');
	});
```
1. Database with indices and using cursor to loop through index
```JavaScript
	import idb from 'idb';

	var dbPromise = idb.open('test-db', 4, function(upgradeDb) {
	  // normally there's a break per case but we specially want to continue execute
	  // e.g. if oldVersion is 1, and the specified version is 4, it will continue executing
	  // all the way up to case 3
	  switch(upgradeDb.oldVersion) {
	    case 0:
	      var keyValStore = upgradeDb.createObjectStore('keyval');
	      keyValStore.put("world", "hello");
	    case 1:
	      upgradeDb.createObjectStore('people', { keyPath: 'name' }); 
	    case 2:
	      var peopleStore = upgradeDb.transaction.objectStore('people');
	      peopleStore.createIndex('animal', 'favoriteAnimal'); // this creates a animal index which stores the `people`'s `favoriteAnimal` and sorted them alphabetically
	    case 3:
	      peopleStore = upgradeDb.transaction.objectStore('people');
	      peopleStore.createIndex('age', 'age');  // this creates an 'age' index which stores the `people`'s `age` and sorted them in ascending order
	  }
	});

	// read "hello" in "keyval"
	dbPromise.then(function(db) {
	  var tx = db.transaction('keyval');
	  var keyValStore = tx.objectStore('keyval');
	  return keyValStore.get('hello');
	}).then(function(val) {
	  console.log('The value of "hello" is:', val);
	});

	// set "foo" to be "bar" in "keyval"
	dbPromise.then(function(db) {
	  var tx = db.transaction('keyval', 'readwrite');
	  var keyValStore = tx.objectStore('keyval');
	  keyValStore.put('bar', 'foo');
	  return tx.complete;
	}).then(function() {
	  console.log('Added foo:bar to keyval');
	});

	dbPromise.then(function(db) {
	  var tx = db.transaction('keyval', 'readwrite');
	  var keyValStore = tx.objectStore('keyval');
	  keyValStore.put('cat', 'favoriteAnimal');
	  return tx.complete;
	}).then(function() {
	  console.log('Added favoriteAnimal:cat to keyval');
	});

	// add people to "people"
	dbPromise.then(function(db) {
	  var tx = db.transaction('people', 'readwrite');
	  var peopleStore = tx.objectStore('people');

	  peopleStore.put({
	    name: 'Sam Munoz',
	    age: 25,
	    favoriteAnimal: 'dog'
	  });

	  peopleStore.put({
	    name: 'Susan Keller',
	    age: 34,
	    favoriteAnimal: 'cat'
	  });

	  peopleStore.put({
	    name: 'Lillie Wolfe',
	    age: 28,
	    favoriteAnimal: 'dog'
	  });

	  peopleStore.put({
	    name: 'Marc Stone',
	    age: 39,
	    favoriteAnimal: 'cat'
	  });

	  return tx.complete;
	}).then(function() {
	  console.log('People added');
	});

	// list all cat people
	dbPromise.then(function(db) {
	  var tx = db.transaction('people');
	  var peopleStore = tx.objectStore('people');
	  var animalIndex = peopleStore.index('animal');

	  return animalIndex.getAll('cat');
	}).then(function(people) {
	  console.log('Cat people:', people);
	});

	// people by age
	dbPromise.then(function(db) {
	  var tx = db.transaction('people');
	  var peopleStore = tx.objectStore('people');
	  var ageIndex = peopleStore.index('age');

	  return ageIndex.getAll();
	}).then(function(people) {
	  console.log('People by age:', people);
	});

	// Using cursors
	dbPromise.then(function(db) {
	  var tx = db.transaction('people');
	  var peopleStore = tx.objectStore('people');
	  var ageIndex = peopleStore.index('age');

	  return ageIndex.openCursor();
	}).then(function(cursor) {
	  if (!cursor) return;
	  return cursor.advance(2);
	}).then(function logPerson(cursor) {
	  if (!cursor) return;
	  console.log("Cursored at:", cursor.value.name);
	  // I could also do things like:
	  // cursor.update(newValue) to change the value, or
	  // cursor.delete() to delete this entry
	  return cursor.continue().then(logPerson);
	}).then(function() {
	  console.log('Done cursoring');
	});
```
### The strategy
1. In Wittr's case, it loads via a service worker, it does so without going to the network - fetches the skeleton and assets straight from the cache
1. Getting posts from the database and display them hence showing the content before we go to the network - then connect the web socket to get updated posts - web sockets bypass both the service worker and the HTTP cache. As the new posts arrive we'll add them to our database for next time.
1. You can use `.openCursor(null, 'prev')` to open a cursor that goes through an index/store backwards.
### Caching Images
1. Photos appear over the lifetime of the app - cache photo as they appear
1. Put in IDB - read pixel data and convert it into a blob - that's kinda complicated. This also loses streaming, which has a performance impact.
1. When we get an item from a database, we have to take the whole thing out in one lump, then convert it into image data, then add it to the page.
1. Whereas if we get the image from a cache, it will stream the data so we don't need to wait for the whole thing before we display anything. This is more memory efficient and leads to faster renders, even if the data is coming from the disk. Hence **cache API** is a much better fit.
1. Responsive image: `srcset` informs browser to load optimized images for different screen size.
1. When post arrives through the web socket, which version do we cache? We wait until the browser makes the request, then we hear about it in the service worker, we go to the network for image and once we get a response we put it in the cache and also send it to the page (image cache is separated from the static content cache).
1. We reset the content of our static cache whenever we update our JavaScript or CSS, but we want these photos to live between versions of our app. Next time we get a request for an image that we already have cached, we simply return it. But the trick is returning image from the cache even if the browser requests a different size of the same image.
1. When posts are short lived, if the browser requests a bigger version of the same image returning one from the cache isn't really a problem. Even returning a big one is fine since not bandwidth is wasted. In fact getting smaller version of something we already have cached would be a waste of bandwidth. 
1. Resizng back and forth is only something web developers do.
1. You can only use the body of response once `response.json()` as in if you read the response as json, you can't ready it as blob (`response.blob()`), this is because the original data has gone, keeping it in memory would be a waste. Also `respondWith(response)` uses the body of `response` as well so you cannot later read it again.
1. In most cases, this is great because if the response was like a large gigabyte video going to a video element on the page, the browser doesn't need to keep the all gigabytes in memory. It only needs to keep the bit currently playing plus a bit of extra for buffering.
1. This is a problem for our photos, we want to open the cache, fetch from the network, and send the response to both the cache and back to the browser. We can solve this by cloning the response we send to the cache.
```JavaScript
	event.respondWith(
		caches.open('wittr-content-imgs').then(function(cache) {
			return fetch(request).then(function(response) {
				cache.put(request, response.clone()); // cloning the response as you can only use it once
				return response; 
			});
		})
	);
````
```JavaScript
function servePhotos(request) {	
	// Photo urls look like:
  	// /photos/9-8028-7527734776-e1d2bda28e-800px.jpg
  	// But storageUrl has the -800px.jpg bit missing.
  	// Use this url to store & match the image in the cache.
  	// This means you only store one copy of each photo.
	var storageUrl = request.url.replace(/-\d+px\.jpg$/, '');

	// checks if the image is in the cache
	// if yes, return the image
	// if not, get from network and cache the image

  return caches.open(contentImgsCache).then(function(cache) {
    return caches.match(storageUrl).then(function(response) {
      if (response) return response;
      return fetch(request).then(function(networkResponse) {
        cache.put(storageUrl, networkResponse.clone());
        return networkResponse;
      });
    });  
  });
}
```
1. To remove specific entries in cache, use:
```JavaScript
	cache.delete(request);

	// or

	cache.keys().then(function(requests) {
		// ...
	});
```
1. A method copied from wittr which deletes unnecessary cache that's not loaded in the current page
```JavaScript
IndexController.prototype._cleanImageCache = function() {
  return this._dbPromise.then(function(db) {
    if (!db) return;

    // TODO: open the 'wittr' object store, get all the messages,
    // gather all the photo urls.
    //
    // Open the 'wittr-content-imgs' cache, and delete any entry
    // that you no longer need.
    var store = db.transaction('wittrs').objectStore('wittrs');
    var photos = []; 
    return store.getAll().then(function(messages) {
      messages.forEach(function(message) {
        if (message.photo) photos.push(message.photo);
      });
      return caches.open('wittr-content-imgs');
    }).then(function(cache) {
      return cache.keys().then(function(requests) {
        requests.forEach(function(request) {
          var url = new URL(request.url);
           if (!photos.includes(url.pathname)) cache.delete(request);
        });
      })
    });
  });
};
```