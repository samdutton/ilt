---
layout: guide
chapter: High performance video for the web
title: Make the most of the video element
index: 5
---

Duration: 5:00

A completed version of this section is in the **step-02** folder.

In this codelab, we'll be using open source video from [The Blender
Foundation](https://www.blender.org/).

The video element is a thing of simple beauty. Download, decode and
play back video with a few lines of code.

Let's get started!

Add a video element to **index.html** in your work folder:

``` html
<video autoplay controls src="video/bunny.webm">
  <p>Sorry! Your browser doesn't support the video element.</p>
</video>
```

Notice that the video element always has a closing `</video>` tag:
it's not 'self-closing'. Any elements inside it (such as the paragraph
in this example) will be displayed if the browser doesn't support
video.

Try it out in the browser: you should see a big white rabbit.

### Time fragments

You can specify a start and end time by adding a fragment to the URL.
This enables multiple views on the same video.

Try it out:

``` html
<video autoplay controls src="video/bunny.webm#t=25,26">
  <p>Sorry! Your browser doesn't support the video element.</p>
</video>
```

### Video formats

What if you want to serve video to Safari users? Safari supports MP4
video but not WebM.

<aside class="note">
  <p><strong>What's WebM?</strong></p>
  <p>MP4 and WebM are container formats. You can think of these a bit like
  .zip files, containing both audio and video components.</p>
  <p>Most commonly, MP4 stores audio using AAC compression and video using
  H.264.</p>
  <p>WebM uses VP8 and Vorbis or, more recently VP9 and Opus.</p>
</aside>

### The source element

Use the `source` element to enable browsers to choose from multiple
formats. MP4 and WebM cover all modern browsers, including all mobile
browsers. Specify video sources in order of preference:

``` html
<video autoplay controls>
  <source src="video/bunny.webm" />
  <source src="video/bunny.mp4" />
  <p>Sorry! Your browser doesn't support the video element.</p>
</video>
```

Adding a `type` attribute to a `source` element enables the browser to
select a video source without having to download part of the video to
check:

``` html
<video autoplay controls>
  <source src="video/bunny.webm" type="video/webm" />
  <source src="video/bunny.mp4" type="video/mp4" />
  <p>Sorry! Your browser doesn't support the video element.</p>
</video>
```

**Try it out!**

The demo at
[simpl.info/video/notype](https://simpl.info/video/notype/index.html)
has `source` elements without `type` attributes. Open the page in Safari
from your own computer or phone and look at network activity from Web
Inspector.

You'll see that Safari has to download a little bit of metadata for the
WebM file to 'sniff' the format. That requires one more resource
request, and additional processing, which is not good anywhere,
particularly on mobile.

<aside class="note">
  <p><strong>Top Tip!</strong></p>
  <p>Make sure your server reports [the correct video MIME
type](https://youtu.be/j5fYOYrsocs?t=3m56s).</p>
</aside>

### Format support

Want to know what formats are supported by your browser?

Try the video `canPlayType()` method.

Add the following code to the **main.js** file in the **js**directory in
your **work**directory:

``` javascript
var videoElement = document.querySelector('video');
console.log('fubar', videoElement.canPlayType('fubar'));
console.log('webm', videoElement.canPlayType('video/webm'));
console.log('webm/vp8', videoElement.canPlayType('video/webm; codecs="vp8"'));
```

Take a look at the output from the DevTools console.

There are many factors that affect whether or not a browser can play a
particular format, so the browser cannot give a definite yes or no
answer to `canPlayType()`. Hence the `maybe` and `probably v`alues! An
empty string means the container and codec combination is definitely not
supported.

### Video src

Want to know which source was actually chosen? Add this to your
JavaScript:

``` javascript
console.log(videoElement.currentSrc);
```

### Video size and display size

While you're there, you can get the actual height and width of the
video. That might be different from the dimensions of the video element
— in the same way that images can be displayed bigger or smaller than
their actual size. For this, you need to wait until the video metadata
is loaded:

``` javascript
videoElement.onloadedmetadata = function() {
  console.log(this.videoWidth);
  console.log(this.videoHeight);
};
```

Thinking about display size and CSS, a simple technique for video is to
specify a `width` and a `max-width`. That way you get a preferred size,
but the video never overflows its container. Don't specify a height: the
browser will work that out automatically. That way you'll keep the right
aspect ratio. Much easier and less error prone than trying to calculate
it manually!

Add the following to **main.css** in the **work/css** folder:

``` css
video {
  width: 640px;
  max-width: 100%;
}
```

### The poster attribute

Last but not least, let's add a poster image: an image displayed before
playback, as soon as a video element is rendered.

There's already a poster image in the **work/images** folder:
**poster.jpg**. Add a `poster` attribute to the opening tag of the
video element:

``` html
<video autoplay controls poster="images/poster.jpg">
```

Including a poster attribute gives viewers a meaningful idea of content
without needing to download video or start playback. The only downside
is that using a poster image incurs an additional file request, which
consumes a little bit of bandwidth and requires rendering.

<aside class="note">
  <p><strong>Choose the right resolution</strong></p>
  <p>Serve video at an appropriate resolution for the target device — not too
  big, not too small. Don't just tweak the video element size! There's no
  point in serving 1080x1920 files to phones with a small viewport when
  360x640px looks just as good.</p>
  <p>You can use media queries to choose a different video depending on the
  viewport dimensions. Try this out at
  [simpl.info/video/mq](https://simpl.info/video/mq): with a bigger
  viewport, you get the bigger video, and with a smaller viewport you get
  the smaller video.</p>
</aside>

### Learn more

-   [Video on mobile](https://www.youtube.com/watch?v=j5fYOYrsocs)
-   [Big Buck Bunny](https://peach.blender.org/): more about the video
    used in this codelab

