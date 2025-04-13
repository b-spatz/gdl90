# gdl90
Cobble together a very basic GDL90 (over WiFi) tester using selected AvareX code (thanks ZK) as a base, with additional decoding and instrumentation.

#### Usage Hints
The "refresh" button at the bottom right flushes the console queue to control scrolling, etc.
That console window is scrollable, but doen't (yet?) update until you click the refresh button.

#### Platform
Use the heavy Flutter framework (as does AvareX), but wanted a stand-alone tool for Android (my use case). 
Perhaps this instrumentation will work back into AvareX at some point, if ZK allows it...

GDL90 spec: https://www.faa.gov/sites/faa.gov/files/air_traffic/technology/adsb/archival/GDL90_Public_ICD_RevA.PDF
