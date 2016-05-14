Chromecast plugin for Cordova
==================

I have forked this repo out of Exozet's repo, because I wanted to bugfix or rather fix plugin's communication with the Chromecast.
You can find a working demo at the bottom of this readme.


##Original repo

```
https://github.com/GetVideostream/cordova-chromecast.git
```

##Exozet's repo
Exozet created fixes required for the plugin to run on the latest cordova versions

```
https://github.com/exozet/cordova-chromecast.git
```


##Installation
For now, add the plugin from this repository.

```
cordova plugin add https://github.com/nbabanov/cordova-chromecast.git
```

If you have NodeJS installed, the dependencies should be automatically copied.

- `http://nodejs.org/`

If not you will need to import the following projects as Library Projects in order for this plugin to work:

- `adt-bundle\sdk\extras\google\google_play_services\libproject\google-play-services_lib`
- `adt-bundle\sdk\extras\android\support\v7\appcompat`
- `adt-bundle\sdk\extras\android\support\v7\mediarouter`

##Usage

This project attempts to implement the official Google Cast SDK for Chrome... in Cordova. We've made a lot of progress in making this possible, check out the offical docs for examples: https://developers.google.com/cast/docs/chrome_sender

When you call `chrome.cast.requestSession()` an ugly popup will be displayed to select a Chromecast. If you're not cool with this - you can call: `chrome.cast.getRouteListElement()` which will return a `<ul>` tag that contains the Chromecasts in a list. All you have to do is style that bad boy and you're off to the races!


##Demo

```
if (!chrome.cast || !chrome.cast.isAvailable) {
    setTimeout(initializeCastApi, 1000);
}

var initializeCastApi = function () {
    var sessionRequest = new chrome.cast.SessionRequest(chrome.cast.media.DEFAULT_MEDIA_RECEIVER_APP_ID);
    var apiConfig = new chrome.cast.ApiConfig(sessionRequest,
        sessionListener,
        receiverListener);
    chrome.cast.initialize(apiConfig, onInitSuccess, onError);
};


function receiverListener(e) {
    if (e === chrome.cast.ReceiverAvailability.AVAILABLE) {
        console.log(e);
    }
}
var castSession = null;
var currentMedia = null;

/**
*	Calling the castConnect function, a popup will show to choose from available devices
*/
function castConnect() {
    castSession = null;
    chrome.cast.requestSession(onRequestSessionSuccess, onLaunchError);
}

/**
*	|	Event listeners
*	V
*/

function onRequestSessionSuccess(e) {
    castSession = e;
}

function onLaunchError(e) {
    console.error(e);
}

function sessionListener(e) {
    console.log('sessionListener');
    console.log(e);
}

function receiverListener(e) {
    console.log('receiverListener');
    console.log(e);
}

function onInitSuccess(e) {
    console.log('onInitSuccess');
    console.log(e);
}

function onError(e) {
    console.error('onError');
    console.error(e);
}

function onMediaDiscovered(how, media) {
    console.log(how);
    currentMedia = media;
}

function onMediaError(e) {
    console.error('onMediaError');
    console.error(e);
}


/**
*	Sends media to the chromecast
*	NOTE: Default receiver only accepts media files (videos, audio and/or subtitles)	
*
*	@param url - url
*	@param type - mime type 
*/

function sendMedia(url, type) {
    var mediaInfo = new chrome.cast.media.MediaInfo(url, type);

    var request = new chrome.cast.media.LoadRequest(mediaInfo);
    castSession.loadMedia(request,
        onMediaDiscovered.bind(this, 'loadMedia'),
        onMediaError);
}
```