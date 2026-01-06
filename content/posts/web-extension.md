+++
date = '2026-01-04T19:44:32-03:00'
draft = false
title = 'This week I built my first Firefox Extension'
+++

For a very long time I have used a Youtube extension for Firefox called BetterYT, or something like that. Unfortunately for some reason this 
extension cannot be found anymore in the addons store. One of the features that I used the most was a controller for the speed of the video.
I could easily scroll with the mouse and it would fine adjust the speed of the video, without me having to click in the settings of the video.

So I decided to build it my own, how hard could it be?

## Getting started on Firefox Extensions 

First I accessed [Mozilla's documentation](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Your_first_WebExtension). Basically you create a `manifest.json` file with a script that will run on specific pages. Mine ended up like this:

```json
{
  "browser_specific_settings": {
    "gecko": {
      "id": "speedify@yourdomain.com",
      "data_collection_permissions": {
        "required": [
          "none"
        ]
      }
    }
  },
  "manifest_version": 2,
  "name": "Scrolling speed for youtube",
  "version": "1.0",
  "description": "Adds a scrolling speed option",
  "content_scripts": [
    {
      "matches": [
        "*://*.youtube.com/watch*"
      ],
      "js": [
        "speedify.js"
      ]
    }
  ]
}
```
In order to upload your addon to the Mozilla store, you need the `browser_specific_settings` property. More can be found in the docs.
Then we have some basic information about our extension, such name, version and description. Finally we have the `content_scripts`, which
is where you tell the extension in which sites to run and what script. 

## Script 
Our script is very simple: get the video and the controllers elements, and create a `span` element that changes the video playback speed when scrolling.
Lets first get our elements. If we inspect a youtube video page we can easily find our `video` element, as well as the controllers. More specifically,
we want the right-left controllers, which is on the right side of the video bar and on the left side of those controllers (I'm not sure why they
separate them like this but oh well).

```js
  const video = document.querySelector('video')
  const controls = document.querySelector('.ytp-right-controls-left')
```

Then, we create our span and set its content to the playback rate of the video:

```js 
  const speedDisplay = document.createElement('span')
  speedDisplay.textContent = video.playbackRate.toFixed(2) + ' x'
```

Finally, we add the wheel functionality. I actually found out that the wheel and scroll actions are different for the API. In this case we use the wheel 
to get the `deltaY` from the event, which gives us the direction of the wheel as a positive or negative number. Based on that we change the video playback
rate accordingly. I also set an option to click it to reset the speed as well:

```js 
  speedDisplay.addEventListener('wheel', (event) => {
    event.preventDefault()
    speedDirection = event.deltaY
    if (speedDirection < 0 && video.playbackRate > 0) {
      video.playbackRate += 0.05
    } else {
      video.playbackRate -= 0.05
    }
    speedDisplay.textContent = video.playbackRate.toFixed(2) + ' x'
  })

  speedDisplay.addEventListener('click', () => {
    video.playbackRate = 1
    speedDisplay.textContent = video.playbackRate.toFixed(2) + ' x'
  })
```

That's it! I added an extra style to match youtube's and there we have it.

![alt](/media/extension.gif)

Check it out if you want to try yourself on [GitHub](https://github.com/viniciusbregoli/youtube-speed-controller-extension) 
