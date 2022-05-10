
# Changelog

## 0.2.0 (20/APR/2022)

* Published the binaries to maven
* Added the tracker.reportCustomEvent() for sending raw data
* Changed the default post path to ctp
* Added feature to auto track the Fragment and its load time
* Added feature to auto track user clicks inside Activity and Fragment
* Added feature to enable or disable activityTrackingPeriodicHB() set to true by default, with default interval of 40 seconds and delay of 2 seconds

## 0.1.2 (01/APR/2022)

* Simplified the integration instructions by absorbing internally the:
    * default gateway endpoint to "https://appgw.conviva.com"
    * HTTPMethod POST
    * default namespace "CAT"
* Added feature of measuring Application and Activity Load time
* Added feature of auto collecting Bundle Info from the getIntent()
* Fixed the duplicate entries in the heartbeats while navigating through the screens

## 0.1.1 (30/MAR/2022)

* Modified the min sdk version supported to 15 for supporting older versions

## 0.1.0 (10/FEB/2022)

* Published the first version of the Conviva App Sensor for the App Analytics
* Add the customerKey("ck") to createrTracker()
* Add Conviva specific parameters ck, clid and iid under clid_schema schema
* Rename the default package to "com.conviva.apptracker"
* Rename the default class to ConvivaAppAnalytics
