---
layout: guide
chapter: Your first Progressive Web App
title: Introduction
index: 0
---

[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps)
are experiences that combine the best of the web and the best of apps. They are
useful to users from the very first visit in a browser tab, no install required.
As the user progressively builds a relationship with the app over time, it
becomes more and more powerful. It loads quickly, even on flaky networks, sends
relevant push notifications, has an icon on the home screen, and loads as a
top-level, full screen experience.

### What is a Progressive Web App?

A Progressive Web Apps is:

* **Progressive**: Works for every user, regardless of browser choice because
  it's built with progressive enhancement as a core tenet.
* **Responsive**: Fits any form factor: desktop, mobile, tablet, or whatever is
  next.
* **Connectivity independent**: Enhanced with service workers to work offline
  or on low-quality networks.
* **App-like**: Feels like an app to the user with app-style interactions and
  navigation because it's built on the app shell model.
* **Fresh**: Always up-to-date thanks to the service worker update process.
* **Safe**: Served via HTTPS to prevent snooping and to ensure content hasn't
  been tampered with.
* **Discoverable**: Is identifiable as an "application" thanks to W3C manifest
  and service worker registration scope, allowing search engines to find it.
* **Re-engageable**: Makes re-engagement easy through features like push
  notifications.
* **Installable**: Allows users to "keep" apps they find most useful on their
  home screen without the hassle of an app store.
* **Linkable**: Easily share via URL, does not require complex installation.

This codelab will walk you through creating your own Progressive Web App,
including the design considerations, as well as implementation details to ensure
that your app meets the key principles of a Progressive Web App.

> Looking for more? Watch Alex Russell's talk about [Progressive Web Apps](https://www.youtube.com/watch?v=MyQ8mtR9WxI) from the 2015 Chrome Dev Summit.
{:.note}

### What are we going to be building?

In this codelab, you're going to build a Weather web app using Progressive Web App techniques. Let's consider the properties of a Progressive Web App:

* **Progressive**: we'll use progressive enhancement throughout.
* **Responsive**: we'll ensure it fits any form factor.
* **Connectivity independent**: we'll cache the app shell with service workers.
* **App-like**: we'll use app-style interactions to add cities and refresh the data.
* **Fresh**: we'll cache the latest data with service workers.
* **Safe**: we'll deploy the app to a host that support HTTPS.
* **Discoverable and installable**: we'll include a manifest making it easy for search engines to find our app.
* **Linkable**: it's the web!

### What you'll learn

* How to design and construct an app using the "app shell" method
* How to make your app work offline
* How to store data for use offline later

### What you'll need

* Chrome 47 or above
* Install [Web Server for
  Chrome](https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb),
  or use your own web server of choice.
* The sample code
* A text editor
* Basic knowledge of HTML, CSS and JavaScript

This codelab is focused on Progressive Web Apps. Some concepts are glossed over
or code blocks (for example styles or non-relevant JavaScript) are provided for
you to simply copy and paste.
