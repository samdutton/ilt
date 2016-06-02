---
layout: guide
chapter: High performance video for the web
section: Implement adaptive streaming
section-number: 8
---

Duration: 2:00

A completed version of this section is in the **step-05** folder.

It's useful to be able to build video streams in JavaScript, but how can
a video player adapt dynamically to device capabilities and current
network conditions?

### DASH: Dynamic Adaptive Streaming over HTTPS

This is where DASH comes in.

With DASH, you deliver video in segments over a normal HTTP connection.
Each segment is made available at a variety of bitrates, resolutions and
formats.The client chooses the best possible version, depending on
bandwidth and device capabilities. Using HTTP enables content delivery
with all the advantages of HTTP — and without the disadvantages of a
traditional streaming server. No proprietary protocols or hardware are
required.

However, rolling your own DASH solution can be complex.

Shaka Player is a simple-to-use DASH JavaScript library from Google
built on MSE and other Web standards. Shaka also supports multilingual
content for audio tracks and subtitles.

Shaka Player also enables delivery of protected content, using the EME
APIs to get licences and do decryption. To find out more take a look at
the [Shaka DRM
documentation](http://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

Let's get started.

Replace everything in **main.js** with the following:

``` javascript
 'use strict';
 var manifestUri =
   'https://storage.googleapis.com/shaka-demo-assets/sintel/dash.mpd';
 // Install built-in polyfills to patch browser incompatibilities.
 shaka.polyfill.installAll();
 // Check to see if the browser supports the basic APIs Shaka needs.
 // This is an asynchronous check.
 shaka.Player.support().then(function(support) {
   // This executes when the asynchronous check is complete.
   if (support.supported) {
   // Everything looks good!
   initPlayer();
} else {
  // This browser does not have the minimum set of APIs you need.
  console.error('Browser not supported!');
}
});

function initPlayer() {
  // Create a Player instance.
  var video = document.querySelector('video');
  var player = new shaka.Player(video);
  player.configure({
  preferredTextLanguage: 'en'
});
// Add player to global scope so it's visible from the browser console
window.player = player;
// Listen for error events.
player.addEventListener('error', onErrorEvent);
// Try to load a manifest.
// This is an asynchronous process.
player.load(manifestUri).then(function() {
  // This runs if the asynchronous load is successful.
  console.log('The video has now been loaded!');
}).catch(onError); // onError is executed if the asynchronous load fails.
}

function onErrorEvent(event) {
  // Extract the shaka.util.Error object from the event.
  onError(event.detail);
}

function onError(error) {
  console.error('Error code', error.code, 'object', error);
}
```

In **index.html** replace the video element and the script elements:

``` html
<video controls autoplay></video>

<script src="js/dist/shaka-player.compiled.js"></script>
<script src="js/main.js"></script>
```

That's it!

Open **index.html** in your browser, and you should see
[Sintel](https://durian.blender.org/), an open source film from the
[Blender Institute](https://www.blender.org/institute/).

### How it works

The code in **main.js** uses Shaka player to implement DASH. Code for
Shaka is in **js/dist/shaka-player.compiled.js**.

The JavaScript for Shaka is compiled using
[Closure](https://developers.google.com/closure/compiler/), which makes
it efficient to use, but hard to read.

If you'd like to learn more about how Shaka is architected and coded,
the [API docs](http://shaka-player-demo.appspot.com/docs/api/index.html)
are a good place to start.

The JavaScript in **main.js** does the following:

1.  Install polyfills.
2.  Check for browser support.
3.  Create a Player object to wrap the video element.
4.  Listen for errors.
5.  Get and parse the DASH manifest.
6.  Get media segments via XHR and create a stream using MSE.

### What's a manifest?

A DASH manifest is an XML file that describes alternative sources for
each segment of video and audio — and also any text tracks for
subtitles, captions and descriptions.

The manifest is created programmatically when a media file is encoded
and/or segmented for DASH delivery.

Shaka estimates bandwidth and checks device capability using
`canPlayType()`. (Remember that from the previous step?) Shaka can then
fetch appropriate segments of audio and video via XHR, using the
information from the manifest. Once the segments are received, they can
be stitched together to form a stream using MSE.

Subtitles, captions and descriptions are also retrieved (if available)
and set for the video using the TextTrack API.

Take a look at the manifest used in this codelab,
[dash.mpd](http://storage.googleapis.com/shaka-demo-assets/sintel/dash.mpd).
Part of this is shown below:

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<!--Generated with <a href="https://github.com/google/edash-packager">https://github.com/google/edash-packager</a> version fe6775a-release-->

<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xlink="http://www.w3.org/1999/xlink" xsi:schemaLocation="urn:mpeg:dash:schema:mpd:2011 DASH-MPD.xsd" xmlns:cenc="urn:mpeg:cenc:2013" minBufferTime="PT2S" type="static" profiles="urn:mpeg:dash:profile:isoff-on-demand:2011" mediaPresentationDuration="PT888.0533447265625S">

  <Period id="0">
    <AdaptationSet id="0" contentType="text" lang="nl">
      <Representation id="0" bandwidth="256" mimeType="text/vtt">
        <BaseURL>s-dut.webvtt</BaseURL>
      </Representation>
    </AdaptationSet>
    <AdaptationSet id="1" contentType="text" lang="en">
      <Representation id="1" bandwidth="256" mimeType="text/vtt">
        <BaseURL>s-eng.webvtt</BaseURL>
      </Representation>
    </AdaptationSet>
    ...
    <AdaptationSet id="10" contentType="video" maxWidth="3840" maxHeight="1636" frameRate="1000000/42000" par="40:17">
      <Representation id="10" bandwidth="216910" codecs="vp9" mimeType="video/webm" sar="427:426" width="426" height="182">
        <BaseURL>v-0240p-0300k-vp9.webm</BaseURL>
        <SegmentBase indexRange="24075612-24076886" timescale="1000000">
          <Initialization range="0-282"/>
        </SegmentBase>
      </Representation>
      <Representation id="11" bandwidth="393793" codecs="vp9" mimeType="video/webm" sar="639:640" width="640" height="272">
        <BaseURL>v-0360p-0550k-vp9.webm</BaseURL>
        <SegmentBase indexRange="43709632-43710931" timescale="1000000">
          <Initialization range="0-284"/>
        </SegmentBase>
      </Representation>
      ...
      <Representation id="14" bandwidth="132318" codecs="mp4a.40.2" mimeType="audio/mp4" audioSamplingRate="48000">
        <AudioChannelConfiguration schemeIdUri="urn:mpeg:dash:23003:3:
          audio_channel_configuration:2011" value="2"/>
        <BaseURL>a-eng-0128k-aac.mp4</BaseURL>
        <SegmentBase indexRange="745-1844" timescale="48000">
          <Initialization range="0-744"/>
        </SegmentBase>
      </Representation>
    </AdaptationSet>
    <AdaptationSet id="12" contentType="audio" lang="en">
      <Representation id="16" bandwidth="110385" codecs="vorbis" mimeType="audio/webm" audioSamplingRate="48000">
        <AudioChannelConfiguration schemeIdUri="urn:mpeg:dash:23003:3:
          audio_channel_configuration:2011" value="2"/>
        <BaseURL>a-eng-0128k-libvorbis.webm</BaseURL>
        <SegmentBase indexRange="12251633-12253142" timescale="1000000">
          <Initialization range="0-4550"/>
        </SegmentBase>
      </Representation>
    </AdaptationSet>
    <AdaptationSet id="13" contentType="video" maxWidth="3840" maxHeight="1636" frameRate="12288/512" subsegmentAlignment="true" par="40:17">
      <Representation id="17" bandwidth="100729" codecs="avc1.42c01e" mimeType="video/mp4" sar="110:109" width="256" height="110">
        <BaseURL>v-0144p-0100k-libx264.mp4</BaseURL>
        <SegmentBase indexRange="828-1747" timescale="12288">
          <Initialization range="0-827"/>
        </SegmentBase>
      </Representation>
      ...
      <Representation id="28" bandwidth="9270693" codecs="avc1.640028" mimeType="video/mp4" sar="1:1" width="2560" height="1090">
        <BaseURL>v-1440p-9000k-libx264.mp4</BaseURL>
        <SegmentBase indexRange="812-1731" timescale="12288">
          <Initialization range="0-811"/>
        </SegmentBase>
      </Representation>
      <Representation id="29" bandwidth="17674702" codecs="avc1.640028" mimeType="video/mp4" sar="1636:1635" width="3840" height="1636">
        <BaseURL>v-2160p-17000k-libx264.mp4</BaseURL>
        <SegmentBase indexRange="831-1750" timescale="12288">
          <Initialization range="0-830"/>
        </SegmentBase>
      </Representation>
    </AdaptationSet>
  </Period>

</MPD>
```

This is not meant to be human readable! The main thing to understand is
that the manifest enables the player to choose alternative formats for
the same content and bitrate – in this example, WebM as well as MP4.

Great for media delivery to a range of devices and connectivity!

Shaka implements [its own bandwidth estimator](https://shaka-player-demo.appspot.com/docs/api/shakaExtern.AbrManager.html),
which enables it to choose different resolutions and bitrates
accordingly, but Shaka can also use a plugin instead.

### How do DASH players get audio and video?

Take a look at the Chrome DevTools Network panel to see what's going on.

Note that you need to select **XHR** or **All**, not **Media**:

![]()

You'll see that Shaka has retrieved a number of files:

-   The manifest, dash.mpd.
-   Multiple .mp4 audio and .webm video segments.
-   Several WebVTT subtitle files.

Let's take a closer look:

![]()

From this you can see that different resolutions and bitrates have been
retrieved as Shaka adjusts to changing bandwidth. You can also check the
Headers for each request:

![]()

Note the Range header (remember that from the previous step?) and how
the Request URL relates to the URL from the manifest.

### Subtitles, captions and descriptions

It's possible to add text tracks to HTML5 video either declaratively by
adding a `<track>` element pointing to a WebVTT file, or via JavaScript
using [TextTrack
API](http://www.html5rocks.com/en/tutorials/track/basics/#toc-cues-js) —
which is what Shaka does.

You can turn on subtitles by clicking on the CC button near the bottom
right of the player:

![]()

(This shows a still from the open source film Sintel, © copyright
Blender Foundation | [sintel.org](http://www.sintel.org).)

From the DevTools console, you can set the subtitle language:

``` javascript
player.configure({preferredTextLanguage: 'fr'});
```

From the DevTools, also take a look at the WebVTT files and the text
track options in dash.mpd.

If a video has audio tracks for different languages, you can set the
language from the player.

For example:

``` javascript
player.configure({preferredAudioLanguage: 'fr'});
```

The `player.getConfiguration()` and `player.getStats()` methods provide
more information about video playback and the player environment:

![]()

### Learn more

-   [Shaka Player repo, tutorials and
    documentation](https://github.com/google/shaka-player)
-   [High performance video for the
    web](https://www.youtube.com/watch?v=Fm3Bagcf9Oo): video about DASH
    and Shaka Player
-   [Building a simple MPEG-DASH streaming
    player](https://msdn.microsoft.com/en-us/library/dn551368(v=vs.85).aspx):
    detailed article explaining how to build your own DASH player
-   [Sintel](https://durian.blender.org/): more about the movie used for
    this codelab
