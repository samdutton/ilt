## Implement your App Shell

There are multiple ways to get started with any project, and we generally
recommend using Web Starter Kit. But, in this case, to keep our project as
simple as possible and concentrate on Progressive Web Apps, we've provided you
with all of the resources you'll need.

```
**Learn more** about [Web Starter Kit](https://developers.google.com/web/tools/starter-kit/).
```{:.note}

### Create the HTML for the App Shell

Now we'll add the core components we discussed in [Architect the App
Shell](https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/step-01).

Remember, the key components will consist of:

* Header with a title, and add/refresh buttons
* Container for forecast cards
* A forecast card template
* A dialog for adding new cities
* A loading indicator

Your index.html file in your work directory should look something like this now
(this is a subset of the actual contents, don't copy this code into your file):

``` html
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
  &lt;meta charset="utf-8"&gt;
  &lt;meta http-equiv="X-UA-Compatible" content="IE=edge"&gt;
  &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
  &lt;title&gt;Weather PWA&lt;/title&gt;
  &lt;link rel="stylesheet" type="text/css" href="styles/inline.css"&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;header class="header"&gt;
    &lt;h1 class="header\_\_title"&gt;Weather PWA&lt;/h1&gt;
    &lt;button id="butRefresh" class="headerButton"&gt;&lt;/button&gt;
    &lt;button id="butAdd" class="headerButton"&gt;&lt;/button&gt;
  &lt;/header&gt;
  &lt;main class="main"&gt;
    &lt;div class="card cardTemplate weather-forecast" hidden&gt;
    . . .
    &lt;/div&gt;
  &lt;/main&gt;
  &lt;div class="dialog-container"&gt;
  . . .
  &lt;/div&gt;
  &lt;div class="loader"&gt;
    &lt;svg viewBox="0 0 32 32" width="32" height="32"&gt;
      &lt;circle id="spinner" cx="16" cy="16" r="14" fill="none"&gt;&lt;/circle&gt;
    &lt;/svg&gt;
  &lt;/div&gt;
  &lt;!-- Insert link to app.js here --&gt;
&lt;/body&gt;
&lt;/html&gt;
```

Notice the loader is visible by default. This ensures that the user sees the
loader immediately as the page loads, giving them a clear indication that the
content is loading.

To save time, we've already created the
[stylesheet](https://weather-pwa-sample.firebaseapp.com/styles/inline.css) for
you to use. Take a few minutes to review it and customize it to make it more
your own.

```
We've given you the markup and styles to save you some time and make sure you're starting on a solid foundation. In the next section, you'll have an opportunity to write your own code.
```{:.note}

### Add the key JavaScript bootstrap code

Now that we have most of the UI ready, it's time to start hooking up the code to
make everything work. Like the rest of the app shell, be conscious about what
code is necessary as part of the key experience and what can be loaded later.

In our bootstrap code (app.js), we've included:

* An app object that contains some of the key information necessary for the app.
* The event listeners for all of the buttons in the header (add/refresh) and in
  the add city dialog (add/cancel).
* A method to add or update forecast cards (app.updateForecastCard).
* A method to get the latest weather forecast data from the Firebase Public
  Weather API (app.getForecast).
* A method to iterate the current cards and call app.getForecast to get the
  latest forecast data (app.updateForecasts).
* Some fake data (fakeForecast) you can use to quickly test how things render.

Add the JavaScript code

1. Make a scripts folder in your work directory.
2. Copy app.js from the resources/step4 directory to your scripts folder.
3. In the index.html file, add a link to the newly created app.js by replacing
   &lt;!-- Insert link to app.js here --&gt;, with:

``` javascript
&lt;script src="scripts/app.js" async&gt;&lt;/script&gt;
```

### Test it out

Now that you've got the core HTML, styles and JavaScript, it's time to test the
app. While it may not do much yet, make sure it doesn't write errors to the
console.

To see how the fake weather data is rendered, uncomment the line below at the
bottom of your scripts/app.js file:

``` javascript
// app.updateForecastCard(fakeForecast);
```

The result should be a nicely formatted (though fake) forecast card with the
spinner disabled, like this:

![]()

[TRY IT](https://weather-pwa-sample.firebaseapp.com/step-04/)

Once you've tried it and verified it works as expected, remove the fakeForecast
data and the call to app.updateForecastCard(fakeForecast); . We needed it only
to ensure that everything worked as expected. In the next step, we'll start
using real data.
