
I have some questions about WebRTC:
웹 RTC에 대한 질문
1.  Does WebRTC use TCP or UDP as its peer-to-peer transport? How can I know?
2.  I read that there are reliability mode and DTLS agreement, how does these affect?
3.  Is this transport the same for both Media and DataChannel?
4.  How do I switch between TCP and UDP?

1. 웹 RTC는 TCP를 쓰냐 UDP를 쓰냐? P2P 전송을 위해서 내가 어떻게 그걸 알 수 있지? 
2. 나는 DTLS 동의와 신뢰모드가 있다는 것을 읽었다. 어떤 영향을 미치나?
3. 미디어와 데이터 채널을 같이 전송하나?
4. 어떻게 내가 TCP와 UDP를 바꿔가면서 쓸 수 있나?

I ask this because I know that browsers have a limit on the number of parallel connections (I think they talk over TCP), and maybe UDP connection is not limited.




1.  It can use either. By default, preference is given to UDP, but depending on the firewall(s) in between the peers connecting it may only be able to connect with TCP. You can use [Wireshark](http://www.wireshark.org/) to capture packets and verify whether TCP or UDP is being used. In Chrome you can also see details on the selected candidate (`googActiveConnection`) by going to `chrome://webrtc-internals`.
    둘다 쓸 수 있다. 기본적으로는 UDP를 제공한다. 그러나 피어커넥팅에 방화벽이 TCP로만 연결할 수 있게 설정되어 있다면 TCP로 연결하는 것이 가능하다. 너는 와이어샤크를 써서 패킷을 볼 수 있다. 그리고 TCP든 UDP든 뭐가 사용되는지 알 수 있다. 크롬에서는 후보자를 선택하는데 자세한 정보를 알 수 있다. 다음의 웹사이트로 감으로써.

2.  "Reliability mode" probably refers to the reliability mode of the [DataChannel](http://www.w3.org/TR/2013/WD-webrtc-20130910/#rtcdatachannel), which can be configured to run in reliable or unreliable mode. DTLS refers to the currently optional, but [soon to be default method](http://blog.vline.com/post/57544771647/webrtc-digest-week-of-7-29-ietf-berlin-and-vp8) of exchanging encryption keys (the other deprecated mode is SDES). Firefox only supports DTLS, so for browser interop, you'll currently need to [enable it in Chrome](http://www.webrtc.org/interop).
    신뢰모드는 아마도 신뢰모드를 주지말지 선택할 수 있는 데이터채널에 신뢰모드를 준다.  DTLS는 기본적인 방법의 암호키 교환을 사용할 수 있다. 파이어폭스는 DTLS만을 지원한다. 브라우저 interop를 위해, 너는 크롬에서 이걸 사용할 수 있도록 해야한다.

3.  The RTCPeerConnection (media) will use TCP or UDP, while the DataChannel uses SCTP. The SCTP implementation used by Firefox is implemented on top of UDP: [https://code.google.com/p/sctp-refimpl/](https://code.google.com/p/sctp-refimpl/).
    RTC 피어 커넥션은 TCP 혹은 UDP를 사용할 것이다. DataChannel이 SCTP를 사용하는 동안. SCTP 함축은 파이어폭스에서 사용된다. SCTP함축은 UDP위에 구현된다. 
    
4.  It's possible to filter out TCP or UDP ICE candidates before adding them with [`addIceCandidate`](http://www.w3.org/TR/2013/WD-webrtc-20130910/#widl-RTCPeerConnection-addIceCandidate-void-RTCIceCandidate-candidate-VoidFunction-successCallback-RTCPeerConnectionErrorCallback-failureCallback). Generally, you should not try to force the transport used since WebRTC will just "do the right thing". The browser does not limit the number of TCP connections used by WebRTC beyond any limit on the RTCPeerConnection or DataChannel (i.e., if you can have 10 PeerConnections, they can each use TCP without any problem).
'add IceCandidate'를 사용하여 TCP 또는 UDP ICE 후보를 추가하기 전에 필터링할 수 있습니다. 일반적으로 WebRTC는 "올바른 일을 할 것"이기 때문에 사용된 전송을 강제로 시도해서는 안 됩니다. 브라우저는 RTCPeerConnection 또는 DataChannel의 제한을 초과하여 WebRTC가 사용하는 TCP 연결 수를 제한하지 않습니다(즉, 10개의 PeerConnection을 가질 수 있는 경우, 각각 문제 없이 TCP를 사용할 수 있습니다).