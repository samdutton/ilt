## Use service workers to pre-cache the App Shell

Progressive Web Apps have to be fast, and installable, which means that they
work online, offline, and on intermittent, slow connections. To achieve this, we
need to cache our app shell using service worker, so that it's always available
quickly and reliably.

If you're unfamiliar with service workers, you can get a basic understanding by
reading [Introduction To Service
Workers](https://developers.google.com/web/fundamentals/primers/service-worker/)
about what they can do, how their lifecycle works and more.

```
**TIP:** Use a new Incognito window for testing and debugging service workers. When the Incognito window is closed, Chrome will remove any cached data or installed service worker, ensuring that you always start from a clean state.
```{:.note}

Features provided via service workers should be considered a progressive
enhancement, and added only if supported by the browser. For example, with
service workers you can cache the app shell and data for your app, so that it's
available even when the network isn't. When service workers aren't supported,
the offline code isn't called, and the user gets a basic experience. Using
feature detection to provide progressive enhancement has little overhead and it
won't break in older browsers that don't support that feature.

```
**Remember:** Service worker functionality is only available on pages that are accessed via HTTPS (https://localhost and equivalents will also work, to facilitate testing). To learn about the rationale behind this restriction check out Prefer Secure Origins For Powerful New Features from the Chromium team.
```{:.note}

### Register the service worker if it's available

The first step to making the app work offline is to register a service worker, a
script that allows background functionality without the need for an open web
page or user interaction.

This takes two simple steps:

1. Tell the browser to register the JavaScript file as the service worker.
1. Create a JavaScript file containing the service worker

First, we need to check if the browser supports service workers, and if it does,
register the service worker. Add the following code to the scripts/app.js file
(before the })(); line at the bottom of the file):

``` javascript
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker
             .register('./service-worker.js')
             .then(function() { console.log('Service Worker Registered'); });
  }
```

### Cache the site assets

When the service worker is registered, an install event is triggered the first
time the user visits the page. In this event handler, we will cache all the
assets that are needed for the application.

```
The code below must NOT be used in production, it covers only the most basic use cases and it's easy to get yourself into a state where your app shell will never update. Be sure to review the section below that discusses the pitfalls of this implementation and how to avoid them.
```{:.warning}

When the service worker is fired, it should open the
[caches](https://developer.mozilla.org/en-US/docs/Web/API/Cache) object and
populate it with the assets necessary to load the App Shell. Create a file
called service-worker.js in your application root folder (which should be
your-first-pwapp-master/work). This file must live in the application root
because the scope for service workers is defined by the directory in which the
file resides. Add this code to your new service-worker.js file:

``` javascript
var cacheName = 'weatherPWA-step-6-1';
var filesToCache = [];
self.addEventListener('install', function(e) {
  console.log('[ServiceWorker] Install');
  e.waitUntil(
    caches.open(cacheName).then(function(cache) {
      console.log('[ServiceWorker] Caching app shell');
      return cache.addAll(filesToCache);
    })
  );
});
```

First, we need to open the cache with caches.open() and provide a cache name.
Providing a cache name allows us to version files, or separate data from the app
shell so that we can easily update one but not affect the other.

Once the cache is open, we can then call cache.addAll(), which takes a list of
URLs, then fetches them from the server and adds the response to the cache.
Unfortunately, cache.addAll() is atomic, if any of the files fail, the entire
cache step will also fail!

```
Sanity Check: You can make sure your service worker has been registered properly and that the install and activate events are being fired by reloading the page and checking the Console in the DevTools.
```{:.note}

**Be sure to change the ****cacheName**** variable each time you make changes to
your service worker** to ensure you're always getting the latest version of the
files from the cache. It's important to periodically purge the cache of unused
content and data. Add an event listener to the activate event that gets all of
the cache keys and deletes the unused ones by adding the following code to the
bottom of your service-worker.js file:

``` javascript
self.addEventListener('activate', function(e) {
  console.log('[ServiceWorker] Activate');
  e.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (key !== cacheName) {
          console.log('[ServiceWorker] Removing old cache', key);
          return caches.delete(key);
        }
      }));
    })
  );
});
```

Finally, let's update the list of files required for the app shell. In the
array, we need to include all of the files our app needs, including images,
JavaScript, stylesheets, etc. Near the top of your service-worker.js file,
replace var filesToCache = []; with the code below:

``` javascript
var filesToCache = [
  '/',
  '/index.html',
  '/scripts/app.js',
  '/styles/inline.css',
  '/images/clear.png',
  '/images/cloudy-scattered-showers.png',
  '/images/cloudy.png',
  '/images/fog.png',
  '/images/ic\_add\_white\_24px.svg',
  '/images/ic\_refresh\_white\_24px.svg',
  '/images/partly-cloudy.png',
  '/images/rain.png',
  '/images/scattered-showers.png',
  '/images/sleet.png',
  '/images/snow.png',
  '/images/thunderstorm.png',
  '/images/wind.png'
];
```

```
Be sure to include all permutations of file names, for example our app is served from index.html, but it may also be requested as / since the server sends index.html when a root folder is requested. You could deal with this in the fetch method, but it would require special casing which may become complex.
```{:.note}

Our app doesn't work offline quite yet. We've cached the app shell components,
but we still need to load them from the local cache.

### Serve the app shell from the cache

Service workers provide the ability to intercept requests made from our
Progressive Web App and handle them within the service worker. That means we can
determine how we want to handle the request and potentially serve our own cached
response.

For example:

``` javascript
self.addEventListener('fetch', function(event) {
  // Do something interesting with the fetch here
});
```

Let's now serve the app shell from the cache. Add the following code to the
bottom of your service-worker.js file:

``` javascript
self.addEventListener('fetch', function(e) {
  console.log('[ServiceWorker] Fetch', e.request.url);
  e.respondWith(
    caches.match(e.request).then(function(response) {
      return response || fetch(e.request);
    })
  );
});
```

Stepping from inside, out, `caches.match()` evaluates the web request that
triggered the
[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) event, and
checks to see if it's available in the cache. It then either responds with the
cached version, or uses fetch to get a copy from the network. The response is
passed back to the web page with e.respondWith().

```
If you're not seeing the [ServiceWorker] logging in the console, be sure you've changed the cacheName variable and reload the page. If that doesn't work, see the section on Tips for testing live service workers.
```{:.warning}

### Beware of the edge cases

As previously mentioned, this code **must not be used in production** because of
the many unhandled edge cases.

#### Cache depends on updating the cache key for every change

For example this caching method requires you to update the cache key every time
content is changed, otherwise, the cache will not be updated, and the old
content will be served. So be sure to change the cache key with every change as
you're working on your project!

#### Requires everything to be redownloaded for every change

Another downside is that the entire cache is invalidated and needs to be
re-downloaded every time a file changes. That means fixing a simple single
character spelling mistake will invalidate the cache and require everything to
be downloaded again. Not exactly efficient.

#### Browser cache may prevent the service worker cache from updating

There's another important caveat here. It's crucial that the HTTPS request made
during the install handler goes directly to the network and doesn't return a
response from the browser's cache. Otherwise the browser may return the old,
cached version, resulting in the service worker cache never actually updating!

#### Beware of cache-first strategies in production

Our app uses a cache-first strategy, which results in a copy of any cached
content being returned without consulting the network. While a cache-first
strategy is easy to implement, it can cause challenges in the future. Once the
copy of the host page and service worker registration is cached, it can be
extremely difficult to change the configuration of the service worker (since the
configuration depends on where it was defined), and you could find yourself
deploying sites that are extremely difficult to update!

#### How do I avoid these edge cases?

So how do we avoid these edge cases? Use a library like
[sw-precache](https://github.com/GoogleChrome/sw-precache), which provides fine
control over what gets expired, ensures requests go directly to the network and
handles all of the hard work for you.

### Tips for testing live service workers

Debugging service workers can be a challenge, and when it involves caching,
things can become even more of a nightmare if the cache isn't updated when you
expect it. Between the typical service worker life cycle and bug in your code,
you may become quickly frustrated. But don't. There are some tools you can use
to make your life easier.

Some tips:

* Once a service worker has been unregistered, it may remain listed until its
  containing browser window is closed.
* Chrome occasionally displays a console error when trying to retrieve the
  service worker, this is safe to ignore.
* If multiple windows to your app are open, the new service worker will not take
  effect until they've all been reloaded and updated to the latest service
  worker.
* Unregistering a service worker does not clear the cache, so it may be possible
  you'll still get old data if the cache name hasn't changed.
* If a service worker exists and a new service worker is registered, the new
  service worker won't take control until the page is reloaded, unless you take
  [immediate
  control](https://github.com/GoogleChrome/samples/tree/gh-pages/service-worker/immediate-control).

#### Your new best friend: chrome://serviceworker-internals

Chrome's Service Worker Internals page (chrome://serviceworker-internals) is a
life saver, easily allowing you to stop and un-register existing service workers
and start fresh. You can also use this page to launch the Developer Tools on the
service worker, giving you access to the console of the service worker.

### Test it out

* Open the Chrome DevTools or open a new tab and check
  chrome://serviceworker-internals to make sure the service worker is properly
  registered and the right resources are cached.
* Try changing the cacheName and make sure that the cache is properly updated.

[TRY IT](https://weather-pwa-sample.firebaseapp.com/step-06/)
