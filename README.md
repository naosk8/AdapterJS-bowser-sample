# AdapterJS-bowser-sample
sample patch to enable Temasys/AdapterJS to work on bowser. (too simple patch. at your own risk)  

I'd like to share my idea, but it's too cheap to make pull-request. So just try it.  
Be careful to modify codes because of its original license.
  
  
## Original Repository
- https://github.com/Temasys/AdapterJS (Great!)
  
  
## Diff between mine and original
```
$ diff ./my/adapter.debug.js ./original/adapter.debug.js
3,7d2
< var originalPeerConnection = window.RTCPeerConnection || window.mozRTCPeerConnection ||
<     window.webkitRTCPeerConnection || window.msRTCPeerConnection || null;
< var originalSessionDescription = window.RTCSessionDescription || window.mozRTCSessionDescription ||
<     window.webkitRTCSessionDescription || window.msRTCSessionDescription || null;
<
644,645c639
< if (navigator.userAgent.match(/Bowser\/[0-9.]*/g) ||
<     ((navigator.mozGetUserMedia ||
---
> if ( (navigator.mozGetUserMedia ||
650c644
<           (navigator.userAgent.match(/chrome/ig) || []).length === 0 && navigator.userAgent.indexOf('Safari/') > 0))) {
---
>           (navigator.userAgent.match(/chrome/ig) || []).length === 0 && navigator.userAgent.indexOf('Safari/') > 0)) {
1185,1188d1178
<       case 'bowser':
<         window.RTCPeerConnection = originalPeerConnection;
<         window.RTCSessionDescription = originalSessionDescription;
<         break;
1426d1415
<
3213,3220c3202,3204
<
<       if (navigator.userAgent.match(/Bowser\/[0-9.]*/g)) {
<         // Bowser
<         result.browser = 'bowser';
<         result.version = this.extractVersion(navigator.userAgent,
<             /Bowser\/([0-9]+)\./, 1);
<       } else if (navigator.mozGetUserMedia) {
<         // Firefox.
---
>
>       // Firefox.
>       if (navigator.mozGetUserMedia) {
```
  
  
## Other work around (but result in failure)
- Bowser is already made to fit to WebRTC interface, so just skip AdapterJS.  
  AdapterJS make Bowser's RTC~ Classes **null** by their current implementation.
- I tried to do it, but some error occured in AdapterJS. It doesn't work.

```
  // in your js

  // Store browser(bowser)'s original RTCPeerConnection, first.
  // As I checked, bowser(ver 0.6.1) used only RTCPeerConnection and webkitRTCPeerConnection.
  // So, moz and ms are not necessary, maybe.
  var origRTCPeerConnection = window.RTCPeerConnection || window.mozRTCPeerConnection ||
    window.webkitRTCPeerConnection || window.msRTCPeerConnection || null;
  var origRTCSessionDescription = window.RTCSessionDescription || window.mozRTCSessionDescription ||
    window.webkitRTCSessionDescription || window.msRTCSessionDescription || null;

  AdapterJS.webRTCReady(function(isUsingPlugin) {
    // window.webrtcDetectedBrowser is set in initial processing of AdapterJS
    if (window.webrtcDetectedBrowser === 'bowser') {
      window.RTCPeerConnection = origRTCPeerConnection;
      window.RTCSessionDescription = origRTCSessionDescription;
    }
    if ((typeof RTCPeerConnection !== 'undefined') && (RTCPeerConnection != null)) {
      pc = new RTCPeerConnection(cfg, con);
    }
  });
```
