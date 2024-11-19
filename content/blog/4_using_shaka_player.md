---
title: Getting Started with Shaka Player
description: A post about JavaScript Closures
permalink: "/blog/getting-started-with-shaka-player/"
date: 2024-11-19
templateEngineOverride: md
tags:
  - Shaka Player
  - JavaScript
---

## Introduction

I was working with a customer that was facing playback issues when testing video playback via a CloudFront distribution. 
For playback, the customer was using Shaka player, which is an open source JavaScript library for adaptive media [1].

In this post, I walk through how I setup a simple test environment I could use to further troubleshoot the customers 
playback issues.

The source code I use within this post is largely an adaptation of the what is shared in the Basic usage page of 
Shaka Player [2].

Wrote this as a way to help with my own learning, and to in case anyone find this interesting.


## Installation

There is the option of importing shaka player as an npm player, which I shall explore at a later time. In this case,
I build from source.

```sh
git clone https://github.com/shaka-project/shaka-player.git
cd shaka-player
```

Inside the shaka-player directory, run the following:

```sh
python build/all.py
```

Note: I was running the above from MacOS. Thus, I used python3.

Once the build is done, the file we are interested in is `dist/shaka-player.compiled.js`.


## Updating the index.html file

Within in the shaka-player folder, you will find an index.html file. Update the 
contents of the file to the following:

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- Shaka Player compiled library: -->
    <script src="dist/shaka-player.compiled.js"></script>
    <!-- Your application source: -->
    <script src="myapp.js"></script>
  </head>
  <body>
    <video id="video"
           width="1080"
           controls autoplay></video>
           
     <form action="javascript:playVideo(playbackurl.value)">
        <input type="text" id="playbackurl" name="playback-url" value="Input Stream URL"><br><br>
        <input type="submit" value="Play">
    </form>
    
  </body>
</html>
```

In my version of the index.html file, I have an html form that would be used to specify 
the URL to a manifest of your choice.



## Adding some JavaScript

Create a file named "myapp.js". For the contents of that file, I made some modifications 
to the source code seen in [2].


The myapp.js seen [2] includes the variable named "manifestUri", the functions initApp, 
initPlayer, onErrorEvent, onError and a call to add an event listener. initApp, 
onErrorEvent and onError were used as is. 

I made a slight modication to initPlayer. I defined the "player" variable as a variable 
that can called from other functions. This was required as I also added a function that 
would called when specifying the playback URL
in the UI.

Below is the version of myapp.js that I used:

```js
let player;

function initApp() {
    // Install built-in polyfills to patch browser incompatibilities.
    shaka.polyfill.installAll();

    // Check to see if the browser supports the basic APIs Shaka needs.
    if (shaka.Player.isBrowserSupported()) {
        // Everything looks good!
        initPlayer();
    } else {
        // This browser does not have the minimum set of APIs we need.
        console.error('Browser not supported!');
    }
}

async function initPlayer() {
    // Create a Player instance.
    const video = document.getElementById('video');
    player = new shaka.Player();
    await player.attach(video);

    // Attach player to the window to make it easy to access in the JS console.
    window.player = player;

    // Listen for error events.
    player.addEventListener('error', onErrorEvent);

}

async function playVideo(playbackUrl) {
    console.log("submit button was selected");
    console.log(`Playback URL: ${playbackUrl}`);

    // Try to load a manifest.
    // This is an asynchronous process.
    try {
        await player.load(playbackUrl);
        // This runs if the asynchronous load is successful.
        console.log('The video has now been loaded!');
    } catch (e) {
        // onError is executed if the asynchronous load fails.
        onError(e);
    }

    return false;
}


function onErrorEvent(event) {
    // Extract the shaka.util.Error object from the event.
    onError(event.detail);
}

function onError(error) {
    // Log the error.
    console.error('Error code', error.code, 'object', error);
}

document.addEventListener('DOMContentLoaded', initApp);

```


Above, you will see:

- player is a variable defined outside of the initPlayer so that it can called within the playVideo function
- playVideo is function that takes in a string value, which is expected to URL to a manifest 


## References:
[1] https://github.com/shaka-project/shaka-player

[2] https://shaka-player-demo.appspot.com/docs/api/tutorial-basic-usage.html