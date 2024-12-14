# Ready Threat Go - Splunk App for Threat Simulation & Detection Validation
![Alt text](Logo.png)

Ready Threat Go (RTG) is a Splunk app developed as an augmentation to the Splunk Common Information Model (CIM) and related datamodels. The app is designed to allow threat researchers and engineers to quickly and routinely test detection logic within Splunk without the technical and financial requirements of a lab or BAS solution. This app aims to accomplish the following goals:

1. Enable security teams to simulate custom threat data within their SIEM to understand where they stand in detection coverage
2. Provide a public repository of observed threat artifacts (sample SIEM events) for immediate use
3. Map individual Threat Events (TE) and Threat Sets (TS) to detection coverage and easily/routinely run the simulations to identify changes or gaps in detection coverage

## How it Works
Threat Events or Threat Sets (simulations involving more than a single event like port scanning) are stored in lookups. There is a lookup for each data type, as shown below:
> 1. Endpoint Processes
> 2. Network Traffic
> 3. Web Proxy

The lookups contain the fields and values for each event. The contents of these lookups are retrieved by hourly searches that then output the events to the "readythreatgo" summary index. Once the events are in the summary index the data will seamlessly filter into the respective datamodels where detections will then fire. 

## Initial Setup
There is just one step to configure the datamodels to use the simulation data:
> * The "readythreatgo" index needs to the added to the datamodels' constraints

This is done either in the CIM Datamodel macros such as `cim_Network_Traffic_indexes` or via the CIM app setup GUI. This should take less than 10 seconds per datamodel and look something like this when complete: `index IN (network,firewall,readythreatgo)`.

That's it for setup! After that you're ready to go. The built-in scheduled searches will simulate the events in the lookups hourly but you can also manually run them if needed.
