I have some questions about WebRTC:

1.  Does WebRTC use TCP or UDP as its peer-to-peer transport? How can I know?
2.  I read that there are reliability mode and DTLS agreement, how does these affect?
3.  Is this transport the same for both Media and DataChannel?
4.  How do I switch between TCP and UDP?

I ask this because I know that browsers have a limit on the number of parallel connections (I think they talk over TCP), and maybe UDP connection is not limited.




1.  It can use either. By default, preference is given to UDP, but depending on the firewall(s) in between the peers connecting it may only be able to connect with TCP. You can use [Wireshark](http://www.wireshark.org/) to capture packets and verify whether TCP or UDP is being used. In Chrome you can also see details on the selected candidate (`googActiveConnection`) by going to `chrome://webrtc-internals`.
    
2.  "Reliability mode" probably refers to the reliability mode of the [DataChannel](http://www.w3.org/TR/2013/WD-webrtc-20130910/#rtcdatachannel), which can be configured to run in reliable or unreliable mode. DTLS refers to the currently optional, but [soon to be default method](http://blog.vline.com/post/57544771647/webrtc-digest-week-of-7-29-ietf-berlin-and-vp8) of exchanging encryption keys (the other deprecated mode is SDES). Firefox only supports DTLS, so for browser interop, you'll currently need to [enable it in Chrome](http://www.webrtc.org/interop).
    
3.  The RTCPeerConnection (media) will use TCP or UDP, while the DataChannel uses SCTP. The SCTP implementation used by Firefox is implemented on top of UDP: [https://code.google.com/p/sctp-refimpl/](https://code.google.com/p/sctp-refimpl/).
    
4.  It's possible to filter out TCP or UDP ICE candidates before adding them with [`addIceCandidate`](http://www.w3.org/TR/2013/WD-webrtc-20130910/#widl-RTCPeerConnection-addIceCandidate-void-RTCIceCandidate-candidate-VoidFunction-successCallback-RTCPeerConnectionErrorCallback-failureCallback). Generally, you should not try to force the transport used since WebRTC will just "do the right thing". The browser does not limit the number of TCP connections used by WebRTC beyond any limit on the RTCPeerConnection or DataChannel (i.e., if you can have 10 PeerConnections, they can each use TCP without any problem).