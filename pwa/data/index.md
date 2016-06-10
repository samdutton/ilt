---
layout: guide
chapter: Your first Progressive Web App
title: Use service workers to cache the forecast data
index: 6
---

Choosing the right [caching
strategy](https://jakearchibald.com/2014/offline-cookbook/) for your data is
vital and depends on the type of data your app presents. For example,
time-sensitive data like weather or stock quotes should be as fresh as possible,
while avatar images or article content can be updated less frequently.

The
[cache-first-then-network](https://jakearchibald.com/2014/offline-cookbook/#cache-network-race)
strategy is ideal for our app. It gets data on screen as quickly as possible,
then updates that once the network has returned the latest data. In comparison
to network-first-then-cache, the user does not have to wait until the
[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) times out to
get the cached data.

Cache-first-then-network means we need to kick off two asynchronous requests,
one to the cache and one to the network. Our network request with the app
doesn't need to change much, but we need to modify the service worker to cache
the response before returning it.

Under normal circumstances, the cached data will be returned almost immediately
providing the app with recent data it can use. Then, when the network request
returns, the app will be updated using the latest data from the network.

### Intercept the network request and cache the response

We need to modify the service worker to intercept requests to the weather API
and store their responses in the cache, so we can easily access them later. In
the cache-then-network strategy, we expect the network response to be the
'source of truth', always providing us with the most recent information. If it
can't, it's OK to fail because we've already retrieved the latest cached data in
our app.

In the service worker, let's add a `dataCacheName` so that we can separate our
applications data from the app shell. When the app shell is updated and older
caches are purged, our data will remain untouched, ready for a super fast load.
Keep in mind, if your data format changes in the future, you'll need a way to
handle that and ensure the app shell and content stay in sync.

Add the following line to the top of your **service-worker.js** file:

``` javascript
var dataCacheName = 'weatherData-v1';
```

Next, we need to modify the `fetch` event handler to handle requests to the data
API separately from other requests.

``` javascript
self.addEventListener('fetch', function(e) {
  console.log('[ServiceWorker] Fetch', e.request.url);
  var dataUrl = 'https://publicdata-weather.firebaseio.com/';
  if (e.request.url.indexOf(dataUrl) === 0) {
    // Put data handler code here
  } else {
    e.respondWith(
      caches.match(e.request).then(function(response) {
        return response || fetch(e.request);
      })
    );
  }
});
```

The code intercepts the request and checks if the URL starts with the address of
the weather API. If it does we'll use
[`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) to make the
request. Once the response is returned, our code opens the cache, clones the
response, stores it in the cache, and finally returns the response to the
original requestor.

Next, replace `// Put data handler code here` with the code below:

``` javascript
e.respondWith(
  fetch(e.request)
    .then(function(response) {
      return caches.open(dataCacheName).then(function(cache) {
        cache.put(e.request.url, response.clone());
        console.log('[ServiceWorker] Fetched&Cached Data');
        return response;
      });
    })
);
```

Our app won't work offline quite yet. We've implemented caching and retrieval
for the app shell, but even though we're caching the data, we're still dependant
on the network.

### Making the requests

As mentioned previously, the app needs to kick off two asynchronous requests,
one to the cache and one to the network. The app uses the `caches` object
available in `window` to access the cache and retrieve the latest data. This is an
excellent example of progressive enhancement as the `caches` object may not be
available in all browsers, and if it's not the network request should still
work.

To do this, we need to:

1. Check if the `caches` object is available in the global `window` object.
1. Request data from the cache.
    * If the server request is still outstanding, update the app with the cached
      data.
1. Request data from the server.
    * Save the data for quick access later.
    * Update the app with the fresh data from the server.

#### Track pending requests

First, let's add the flag we'll use to prevent the cache from updating the app
in the rare case that the XHR responds before the cache. Add `hasRequestPending:
false,` (make sure to include the trailing comma) to the top of the app object
definition in the **scripts/app.js file**.

#### Get data from the cache

Next, we need to check if the `caches` object exists and request the latest data
from it. Add the following code to `app.getForecast`, before the `// Make the XHR
to get the data, then update the card` comment:

``` javascript
if ('caches' in window) {
  caches.match(url).then(function(response) {
    if (response) {
      response.json().then(function(json) {
        // Only update if the XHR is still pending, otherwise the XHR
        // has already returned and provided the latest data.
        if (app.hasRequestPending) {
          console.log('updated from cache');
          json.key = key;
          json.label = label;
          app.updateForecastCard(json);
        }
      });
    }
  });
}
```

#### Update hasRequestPending flag

Finally, we need to update the `app.hasRequestPending` flag. Just after the
comment about making the XHR, add `app.hasRequestPending = true;`

Then, in the o`nreadystatechange` XHR response handler, just before
`app.updateForecastCard(response)`, set `app.hasRequestPending = false;`

Our weather app now makes two asynchronous requests for data, one from the cache
and one via an XHR. If there's data in the cache, it'll be returned and rendered
extremely quickly (tens of milliseconds) and update the card only if the XHR is
still outstanding. Then, when the XHR responds, the card will be updated with
the freshest data direct from the weather API.

If for some reason, the XHR responds faster than the cache, the
`hasRequestPending` flag will prevent the cache from over writing the latest data
from the network.

### Test it out

* In the console, you should see two events each time you refresh, one
  indicating data was retrieved from the cache, and one that it was retrieved
  from the network.
* The app should work completely offline now. Try stopping your development
  server and disconnecting your network and running the app. The app shell and
  data should both be served from the cache.

[TRY IT](https://weather-pwa-sample.firebaseapp.com/step-07/)
