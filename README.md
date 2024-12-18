# Ready Threat Go - Splunk App for Threat Simulation & Detection Validation
![Alt text](Logo.png)

Ready Threat Go (RTG) is a Splunk app developed as an augmentation to the Splunk Common Information Model (CIM) and related datamodels. The app is designed to allow threat researchers and engineers to quickly and routinely test detection logic within Splunk without the technical and financial requirements of a lab or BAS solution. This app aims to accomplish the following goals:

1. Enable security teams to simulate custom threat data within their SIEM to understand where they stand in detection coverage
2. Provide a public repository of observed threat artifacts (sample SIEM events) for immediate use
3. Map individual Threat Events (TE) and Threat Sets (TS) to detection coverage and easily/routinely run the simulations to identify changes or gaps in detection coverage

As a general summary, this app `creates simulated events in Splunk that go into an index and the various datamodels. The events are stored in lookups which already include sample events but can be modified or added to depending on what you want to simulate.`

## How it Works
Threat Events or Threat Sets (simulations involving more than a single event like port scanning) are stored in lookups. There is a lookup for each data type, as shown below:
> 1. Endpoint Processes
> 2. Network Traffic
> 3. Web Proxy

The lookups contain the fields and values for each event. The contents of these lookups are retrieved by hourly searches that then output the events to the "readythreatgo" summary index. Once the events are in the summary index the data will seamlessly filter into the respective datamodels where detections will then fire. 

## Initial Setup
There is just one step to configure the datamodels to use the simulation data:
> * The "readythreatgo" index needs to be added to the datamodels' constraints

This is done either in the CIM Datamodel macros such as `cim_Network_Traffic_indexes` or via the CIM app setup GUI. This should take less than 10 seconds per datamodel and look something like this when complete: `index IN (network,firewall,readythreatgo)`.

That's it for setup! After that you're ready to go. The built-in scheduled searches will simulate the events in the lookups hourly but you can also manually run them if needed.

> NOTE: After first installing the app, the hourly searches that simulate the events will not run for a full hour. If you don't want to wait you can click run for each one in the native scheduled searches pane

![Alt text](Dashboard%20Demo.png)

## Newly Released Features & Future Items
### New Features:
* New data type and simulations: `DNS`
* Index level simulation support!
   * There are two index-level sourcetypes with simulations out-of-the-box: `wineventlog:security` and `xmlwineventlog:security`. 
   * There is a lookup for each, to add events simply paste the raw event in the "raw" field and be sure to enable it with the "enabled' field.
   * The classic Wineventlog:security sourcetype expects certain fields that a Splunk forwarder would usually add in before sending the event to Splunk. If you have issues with extraction copy the existing entry in the lookup already and change values you need to such as commandline, parent process, etc and you should be all set.
> NOTE: You will need to install the Windows TA on Splunk for these to work correctly. RTG does some compliancy on those events but ultimately passes them to the native TA to get all the calculated fields and extractions real data would. 
* Dashboard now has a manual deploy button!
   * This lets you run all the out-of-the-box simulation searches with a single click, especially if you just installed the app and don't want to wait an hour for the simulations to run. If you add your own saved searches be sure to add them to that drilldown.




