---
layout: guide
chapter: Your first Progressive Web App
title: Start with a fast first load
index: 4
---

Progressive Web Apps should start fast and be usable immediately. In its current
state, our Weather App starts quickly, but it's not useable. There's no data. We
could make an AJAX request to get that data, but that results in an extra
request and makes the initial load longer. Instead, provide real data in the
first load.

### Inject the weather forecast data

For this code lab, we'll simulate the server injecting the weather forecast
directly into the JavaScript, but in a production app, the latest weather
forecast data would be injected by the server based on the IP address
geo-location of the user.

Add the following JavaScript code just inside the [immediately-invoked function
expression](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
in scripts/app.js (after the 'use strict'; line near the top):

``` javascript
  var initialWeatherForecast = {
    key: 'newyork',
    label: 'New York, NY',
    currently: {
      time: 1453489481,
      summary: 'Clear',
      icon: 'partly-cloudy-day',
      temperature: 52.74,
      apparentTemperature: 74.34,
      precipProbability: 0.20,
      humidity: 0.77,
      windBearing: 125,
      windSpeed: 1.52
    },
    daily: {
      data: [
        {icon: 'clear-day', temperatureMax: 55, temperatureMin: 34},
        {icon: 'rain', temperatureMax: 55, temperatureMin: 34},
        {icon: 'snow', temperatureMax: 55, temperatureMin: 34},
        {icon: 'sleet', temperatureMax: 55, temperatureMin: 34},
        {icon: 'fog', temperatureMax: 55, temperatureMin: 34},
        {icon: 'wind', temperatureMax: 55, temperatureMin: 34},
        {icon: 'partly-cloudy-day', temperatureMax: 55, temperatureMin: 34}
      ]
    }
  };
```

### Differentiating the first run

But, how do we know when to display this information, which may not be relevant
on future loads when the weather app is pulled from the cache? When the user
loads the app on subsequent visits, they may have changed cities, so we need to
load the information for those cities, not necessarily the first city they ever
looked up.

User preferences, like the list of cities a user has subscribed to, should be
stored locally using IndexedDB or another fast storage mechanism. To simplify
this code lab as much as possible, we've used
[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage),
which is not ideal for production apps because it is a blocking, synchronous
storage mechanism that is potentially very slow on some devices.

{:.note}
Extra Credit: Replace `localStorage` implementation with [idb](https://www.npmjs.com/package/idb), check out [localForage](http://mozilla.github.io/localForage/) as a simple wrapper to idb.

First, let's add the code required to save user preferences at the bottom of the
immediately-invoked function expression in **scripts/app.js** before the `})();` line
at the bottom of the file:

``` javascript
  // Save list of cities to localStorage.
  app.saveSelectedCities = function() {
    var selectedCities = JSON.stringify(app.selectedCities);
    localStorage.selectedCities = selectedCities;
  };
```

Next, let's add the startup code to check if the user has any saved cities and
render those, or use the injected data. Add the following code to your
scripts/app.js file (after the code you just added):

``` javascript
  /\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
   \*
   \* Code required to start the app
   \*
   \* NOTE: To simplify this codelab, we've used localStorage.
   \*   localStorage is a synchronous API and has serious performance
   \*   implications. It should not be used in production applications!
   \*   Instead, check out IDB (https://www.npmjs.com/package/idb) or
   \*   SimpleDB (https://gist.github.com/inexorabletash/c8069c042b734519680c)
   \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/
  app.selectedCities = localStorage.selectedCities;
  if (app.selectedCities) {
    app.selectedCities = JSON.parse(app.selectedCities);
    app.selectedCities.forEach(function(city) {
      app.getForecast(city.key, city.label);
    });
  } else {
    app.updateForecastCard(initialWeatherForecast);
    app.selectedCities = [
      {key: initialWeatherForecast.key, label: initialWeatherForecast.label}
    ];
    app.saveSelectedCities();
  }
```

### Save the selected cities

Finally, don't forget to save the list of cities when the user adds a new one by
adding: `app.saveSelectedCities();` to the `butAddCity` button event handler (right
before the `app.toggleAddDialog(false);` line).

### Test it out

* When first run, your app should immediately show the user the forecast from
  `initialWeatherForecast`.
* Add a new city (by clicking the **+** icon on the upper right) and verify that two
  cards are shown.
* Refresh the browser and verify that the app loads both forecasts and shows the
  latest information.

[TRY IT](https://weather-pwa-sample.firebaseapp.com/step-05/)
