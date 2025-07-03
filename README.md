# gdl90
Cobble together a very basic GDL90 (over WiFi) tester using selected AvareX code (thanks ZK) as a base, with additional decoding and instrumentation.
I use on Android as a simple stand-alone app to assert GDL90 compliance, help out Avare* forum users, diagnose my non-compliant new Dynon HDX &nbsp; :-( &nbsp; , etc.

#### Usage Hints
The "refresh" button at the bottom right flushes the console queue to control scrolling, etc.
That console window is scrollable, but doesn't (yet?) update until you click the refresh button (do it often).

Example screenshot shows packet counts at the top, plus more detail packet info in the Console by port (4000 in this case):  
* Heartbeat shows 36 packets of standard GDL90 Heartbeat and 33 of the [stratux-identifying Heatbeat](https://github.com/cyoung/stratux/blob/master/notes/app-vendor-integration.md) plus some bit decoding)  
* Ownship shows 33 packets, decoded as N342ME/mode-S plus lat/long and some bit decoding
* 90 Traffic packets, latest is SWA2276 with pressure altitude
* Unknown packet counts by msg type and count, e.g. 33 packets of msg type 0x53
   
<img width="600" src="gdl90-tester-0.0.8.jpg">

#### AvareX Decoding
message_factory.dart message types defined and processed (after reception, or ignored):  
`
  Hex  Message                  Process?
  0x00 heartBeat                no
  0xCC stratux Heartbeat        no, but PR submitted for PFD roll-orientation reversal
  0x07 uplink                   yes
  0x0A ownShip                  yes
  0x0B ownShipGeometricAltitude yes
  0x14 trafficReport            yes
  0x1E basicReport              no
  0x1F longReport               no
  0x4C ahrsReport               yes
  0x7A deviceReport             no
  `

#### Platform
Use the heavy Flutter framework (as does AvareX), but wanted a stand-alone tool for Android (my use case). 
Perhaps this instrumentation will work back into AvareX at some point, if ZK allows it...

GDL90 spec: https://www.faa.gov/sites/faa.gov/files/air_traffic/technology/adsb/archival/GDL90_Public_ICD_RevA.PDF  
ForeFlight extensions: https://www.foreflight.com/connect/spec/  
stratux identifying Heartbeat, etc.: https://github.com/cyoung/stratux/blob/master/notes/app-vendor-integration.md  
stratux code: https://github.com/cyoung/stratux/blob/master/main/gen_gdl90.go#L629  
Dynon issues: https://forum.flydynon.com/threads/ads-b-over-wifi.15650/page-2#post-92735  
