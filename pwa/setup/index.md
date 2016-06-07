## Getting set up

### Download the Code

You can download all of the code for this codelab, by clicking the following
button:

[Download source
code](https://github.com/googlecodelabs/your-first-pwapp/archive/master.zip)

Unpack the downloaded zip file. This will unpack a root folder
(your-first-pwapp-master), which contains one folder for each step of this
codelab, along with all of the resources you will need.

The step-NN folders contain the desired end state of each step of this codelab.
They are there for reference. We'll be doing all our coding work in a directory
called work.

### Install and verify web server

While you're free to use your own web server, this codelab is designed to work
well with the Chrome Web Server. If you don't have that app installed yet, you
can install it from the Chrome Web Store.

[Install Web Server for
Chrome](https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb?hl=en)

After installing the Web Server for Chrome app, click on the Apps shortcut on
the bookmarks bar:

![]()

```
More help: [Add and open Chrome apps](https://support.google.com/chrome_webstore/answer/3060053)
```{:.note}

In the ensuing window, click on the Web Server icon:

![]()

You'll see this dialog next, which allows you to configure your local web
server:

![]()

Click the **choose folder** button, and select the work folder. This will enable
you to serve your work in progress via the URL highlighted in the web server
dialog (in the **Web Server URL(s)** section).

Under Options, check the box next to "Automatically show index.html", as shown
below:

![]()

Then stop and restart the server by sliding the toggle labeled "Web Server:
STARTED" to the left and then back to the right.

![]()


Now visit your work site in your web browser (by clicking on the highlighted Web
Server URL) and you should see a page that looks like this:


![]()


Obviously, this app is not yet doing anything interesting - so far, it's just a
minimal skeleton with a spinner we're using to verify your web server
functionality. We'll add functionality and UI features in subsequent steps.

```
From this point forward, all testing/verification (e.g. the Test It Out sections in subsequent steps) should be performed using this web server setup. You'll usually be able to get away with simply refreshing your test browser tab.
```{:.note}

