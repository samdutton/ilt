---
layout: guide
chapter: Your first Progressive Web App
title: Architect your App Shell
index: 2
---

### What is the app shell?

The app's shell is the minimal HTML, CSS, and JavaScript that is required to
power the user interface of a progressive web app and is one of the components
that ensures reliably good performance. Its first load should be extremely quick
and immediately cached. "Cached" means that the shell files are loaded once over
the network and then saved to the local device. Every subsequent time that the
user opens the app, the shell files are loaded from the local device's cache,
which results in blazing-fast startup times.

App shell architecture separates the core application infrastructure and UI from
the data. All of the UI and infrastructure is cached locally using a service
worker so that on subsequent loads, the Progressive Web App only needs to
retrieve the necessary data, instead of having to load everything.

![]()

Put another way, the app shell is similar to the bundle of code that you'd
publish to an app store when building a native app. It is the core components
necessary to get your app off the ground, but likely does not contain the data.

### Why use the App Shell architecture?

Using the app shell architecture allows you to focus on speed, giving your
Progressive Web App similar properties to native apps: instant loading and
regular updates, all without the need of an app store.

### Design the App Shell

The first step is to break the design down into its core components.

Ask yourself:

* What needs to be on screen immediately?
* What other UI components are key to our app?
* What supporting resources are needed for the app shell? For example images,
  JavaScript, styles, etc.

We're going to create a Weather app as our first Progressive Web App. The key
components will consist of:

* Header with a title, and add/refresh buttons
* Container for forecast cards
* A forecast card template
* A dialog box for adding new cities
* A loading indicator

When designing a more complex app, content that isn't needed for the initial
load can be requested later and then cached for future use. For example, we
could defer the loading of the New City dialog until after we've rendered the
first run experience and have some idle cycles available.
