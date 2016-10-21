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