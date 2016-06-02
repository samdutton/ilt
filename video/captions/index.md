---
layout: guide
chapter: High performance video for the web
section: Add subtitles, captions & descriptions
section-number: 6
---

Duration: 2:00

A completed version of this section is in the **step-03** folder.

It's easy to make audio and video more accessible to a wider audience by
adding subtitles, titles or descriptions.

This can be done by adding a `track` element as a child of a `video` or
`audio` element. The `track` element's `src` attribute points to a file
that gives information about text (such as subtitles) to be displayed by
the browser during playback. A track file consists of timed ‘cues' in a
plain text file.

Create a directory named **track** in your **work**folder and add a new
file named **track.vtt**. Add the following text to that file:

``` html
WEBVTT FILE

00:00:00.000 --> 00:00:02.000
Opening titles

00:00:03.500 --> 00:00:05.000
A rabbit hole at the base of a tree

00:00:09.000 --> 00:00:11.000
An enormous white rabbit in a field

00:00:13.000 --> 00:00:14.500
Three rodents, one throws an acorn at the rabbit

00:00:15.500 --> 00:00:16.750
The rabbit looks shocked
```

This format is called WebVTT.

Add the following `track` element as a child of your video element:

``` html
<track label="Descriptions" src="tracks/track.vtt" />
```

Your video element code should now look like this:

``` html
<video autoplay controls  poster="images/poster.jpg">
  <source src="video/bunny.webm" type="video/webm" />
  <source src="video/bunny.mp4" type="video/mp4" />
  <track label="Descriptions" src="tracks/track.vtt" />
  <p>Sorry! Your browser doesn't support the video element.</p>
</video>
```

Open your page in the browser, click on the captions button at the right
of the video controls and select Descriptions:

![]()

The video should now display the descriptions you added — feel free to
embellish them.

![]()

The track element is supported by [all modern
browsers](http://caniuse.com/#feat=webvtt) on desktop and mobile — so
make the most of it!

You can also get and set text tracks in JavaScript, and listen out for
cue changes. Take a look at this example from
[simpl.info/track](https://simpl.info/track/):

``` javascript
'use strict';
var videoElement = document.querySelector('video');
var textTrack = videoElement.textTracks[0]; // there's only one!
var data = document.getElementById('data');
textTrack.oncuechange = function() {
  // 'this' is a textTrack
  var cue = this.activeCues[0]; // assuming there is only one active cue
  if (cue) {
    data.innerHTML = cue.startTime + '-' + cue.endTime + ': ' + cue.text +
        '<br />' + data.innerHTML;
    //  var obj = JSON.parse(cue.text); // cues can be data too :)
  }
};
```

<aside class="note">
  <p><strong>Using tracks for synchronised metadata</strong></p>
  <p>The track element can be used to synchronise any metadata with media
  playback — not just subtitles.</p>
  <p>You can add data (such as json) to cues, then parse the value of the
  cues when the track `cuechange` event is fired.</p>
  <p>[This demo](http://samdutton.com/map) shows how the track element can be
  used to synchronise video playback with the position of a map marker,
  and make synchronised changes to DOM elements. The position of the map
  marker changes corresponding to the current time of the video. Time of
  day is overlaid on the video.</p>
</aside>

<aside class="warning">
<p><strong>Tracks and tracks</strong></p>
<p>Don't confuse the track element and the [TextTrack
API](http://www.html5rocks.com/en/tutorials/track/basics/#toc-cues-js)
with
[MediaStreamTrack](https://developer.mozilla.org/docs/Web/API/MediaStream):
they are related but entirely different!</p>
</aside>

> **Using tracks for synchronised metadata**
>
> The track element can be used to synchronise any metadata with media
> playback — not just subtitles.
>
> You can add data (such as json) to cues, then parse the value of the
> cues when the track `cuechange` event is fired.
>
> [This demo](http://samdutton.com/map) shows how the track element can be
> used to synchronise video playback with the position of a map marker,
> and make synchronised changes to DOM elements. The position of the map
> marker changes corresponding to the current time of the video. Time of
> day is overlaid on the video.

> **Tracks and tracks**
>
> Don't confuse the track element and the [TextTrack
> API](http://www.html5rocks.com/en/tutorials/track/basics/#toc-cues-js)
> with
> [MediaStreamTrack](https://developer.mozilla.org/docs/Web/API/MediaStream):
> they are related but entirely different!
{:.warning}

### Learn more

-   [Getting started with the track
    element](http://www.html5rocks.com/en/tutorials/track/basics/)
-   [An introduction to WebVTT
    and](https://dev.opera.com/articles/an-introduction-to-webvtt-and-track/)
-   [Mozilla Developer
    Network:](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/track)

