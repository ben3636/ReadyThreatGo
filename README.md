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
This app has come a long way already. The most recent update adds native eventtypes and tagging for the simulations in the `readythreatgo` index so the events are compatible with the datamodels out of the box with no extra setup. This reduced the onboarding to just the single step of adding the index to the datamodels' macros. This one step will remain manual because it serves as the consent/acknowledgment that RTG will begin firing alerts.

Additionally I will shortly be adding support for `index-level event simulation`. This will allow you to place raw events in their entirety such as Wineventlog in a lookup where they will be output to the readythreatgo index for simulation. This has been tested with XMLWineventlog already and can successfully pass the simulated event off to the Windows TA (if installed) so the event lands in the summary index with all the calculated fields and tagging required to be indistinguishable from real telemetry. As of now, it seems that there will be one lookup per sourcetype as well as a corresponding scheduled search to handle the actual simulation. I will update the documentation to cover the process of adding custom index-level simulations which should entail creating a lookup/scheduled search with the appropriate lookup name and sourcetype specified. More to come :)
