---
layout: guide
chapter: High performance video for the web
title: Build a video stream in JavaScript
index: 6
---

Duration: 2:00

A completed version of this section is in the **step-04** folder.

The source element makes it possible for the browser to choose between
multiple formats,

Modern browsers make 'partial requests' for video rather than waiting
for a whole video to download.

To see this in action, open
[simpl.info/longvideo](https://simpl.info/longvideo):

![]()

Open the Network panel of the Chrome DevTools, select Media, move around
between different times in the video, and you'll see lots of requests
for the same video. What's going on?

![]()

Click on one of the network responses, view the Request headers, and
you'll see something like this:

![]()

The browser has added a `range` header to the request, specifying a
start point in the video in bytes. As long as the server supports range
requests, it will return video data starting from that point.

Getting smaller segments is a better approach than waiting for
monolithic video downloads, but the decisions are all made by the
browser.

What if you wanted to choose between *different* video sources —
adapting to bandwidth, for example?

The [MediaSource
Extensions](https://developer.mozilla.org/en-US/docs/Web/API/MediaSource)
API (MSE) gives you more control by making it possible to construct
video streams in JavaScript. You can request segments of video using the
Fetch API or XHR, then stitch the segments together and buffer them for
a video element using MSE.

You can see MSE in action at [simpl.info/mse](https://simpl.info/mse):

![]()

In this example, video is retrieved via XHR, the video is divided up and
stored locally using the File APIs, then MSE creates a buffer to feed
the video element.

### Learn more

-   [W3C MSE specification](http://www.w3.org/TR/media-source/)
-   [Stream video using the Media Source
    API](http://updates.html5rocks.com/2011/11/Stream-video-using-the-MediaSource-API)
-   [Demo using MSE and the File
    APIs](http://html5-demos.appspot.com/static/media-source.html)

