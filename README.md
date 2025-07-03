# gdl90
A minimal GDL90 (over WiFi) tester using selected AvareX code (thanks ZK) as a base, with additional decoding and instrumentation.
I use on Android (mostly) as a simple, portable, stand-alone app to assert GDL90 compliance,
help out Avare* forum users, diagnose my non-compliant new Dynon HDX &nbsp; :-( &nbsp; , etc.

#### Usage Hints
The "refresh" button at the bottom right flushes the console queue to control scrolling, etc.
That console window is scrollable, but doesn't (yet?) update until you click the refresh button (do it often).

Example screenshot against stratux v1.6r1-eu027 shows packet counts at the top, plus more detail packet info in the Console by port (4000 in this case):  
* Heartbeat shows 36 packets of standard GDL90 Heartbeat and 33 of the [stratux-identifying Heatbeat](https://github.com/cyoung/stratux/blob/master/notes/app-vendor-integration.md) plus some bit decoding)  
* Ownship shows 33 packets, decoded as N342ME/mode-S plus lat/long and some bit decoding
* 90 Traffic packets, latest is SWA2276 with pressure altitude
* "Unknown" packet counts by msg type and count, e.g. 33 packets of msg type 0x53, but not decoding specifically, but may be "known" e.g.
   * 0x53 is "second stratux status" msg (SX message, from Hilton software)
   * 0x65 is stratux ForeFlight "ID Message" (see stratux GDL packet code, ForeFlight links below)
* Counts by port: AvareX 4000, 43211, 49002, 5557 (as of 0.55) plus 8384 for proprietary Dynon msgs (in-progress alpha)
   
<img width="600" src="gdl90-tester-0.0.8.jpg">

#### AvareX Decoding
storage.dart listens on ports 4000, 43211, 49002, 5557  
message_factory.dart message types defined and processed (after reception, or ignored):  
```
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
```
#### stratux and GPS fix scenarios
stratux encodes the GPS fix state but only in the stratux-specific HeartBeat (0xCC) in byte1 bit 1
(along with AHRS enabled bit 0) -- but NOT in the GDL90 Heartbeat message status byte 1 bit
[per the spec](https://www.faa.gov/sites/faa.gov/files/air_traffic/technology/adsb/archival/GDL90_Public_ICD_RevA.PDF)! (see p. 10).
If stratux has no GPS fix, only the 0xCC msg will reflect this, along with no Ownship msg with position every second (1 Hz).

The GDL90 spec allows a GPS fix of 0,0 to be sent (see section 3.4 p. 16) and this along with NIC=0 is supposed to signify an invalid fix, although at
least AvareX ignores this scenario and assumes 0,0 is valid without checking NIC or HeartBeats(s), etc.  stratux is also non-compliant and does
not use the 0x00 msg bit and just stops sending Ownship (spec says send ownship but 0,0 and NIC=0 means invalid).  Either I misunderstand the spec
or many implementors are out of spec.

#### Platform
Use the heavy Flutter framework (as does AvareX), but wanted a stand-alone tool for Android (my use case). 
Perhaps this instrumentation will work back into AvareX at some point, if ZK allows it...

GDL90 spec: https://www.faa.gov/sites/faa.gov/files/air_traffic/technology/adsb/archival/GDL90_Public_ICD_RevA.PDF  
ForeFlight extensions: https://www.foreflight.com/connect/spec/  
stratux identifying Heartbeat, etc.: https://github.com/cyoung/stratux/blob/master/notes/app-vendor-integration.md  
stratux GDL90-ish packet code: https://github.com/cyoung/stratux/blob/master/main/gen_gdl90.go#L629  
Dynon issues reported: https://forum.flydynon.com/threads/ads-b-over-wifi.15650/page-2#post-92735  
