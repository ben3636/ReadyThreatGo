# Ready Threat Go - Splunk App for Threat Simulation & Detection Validation in the SIEM
![Alt text](Logo.png)

Ready Threat Go (RTG) is a Splunk app developed as an augmentation to the Splunk Common Information Model (CIM) and related datamodels. The app is designed to allow threat researchers and engineers to quickly and routinely test detection logic within Splunk without the technical and financial requirements of a lab or BAS solution. This app aims to accomplish the following goals:

1. Enable security teams to simulate custom threat data within their SIEM to understand where they stand in detection coverage
2. Provide a public repository of observed threat artifacts (sample SIEM events) for immediate use
3. Enable security teams to map individual Threat Events (TE) and Threat Sets (TS) to detection coverage and easily/routinely run the simulations to identify changes or gaps in detection coverage

As a general summary, this app `creates simulated events in Splunk that go into an index and the various datamodels. The events are stored in lookups which already include sample events but can be modified or added to depending on what you want to simulate.`

## How it Works
Threat Events or Threat Sets (simulations involving more than a single event like port scanning) are stored in lookups. There is a lookup for each data type, as shown below:
> 1. Endpoint Processes
> 2. Network Traffic
> 3. Web Proxy
> 4. DNS

The lookups contain the fields and values for each event. The contents of these lookups are retrieved by hourly searches that then output the events to the "readythreatgo" summary index. Once the events are in the summary index the data will seamlessly filter into the respective datamodels where detections will then fire. 

## Initial Setup
There is just one step to configure the datamodels to use the simulation data:
> * The "readythreatgo" index needs to be added to the datamodels' constraints

This is done either in the CIM Datamodel macros such as `cim_Network_Traffic_indexes` or via the CIM app setup GUI. This should take less than 10 seconds per datamodel and look something like this when complete: `index IN (network,firewall,readythreatgo)`.

That's it for setup! After that you're ready to go. The built-in scheduled searches will simulate the events in the lookups hourly but you can also manually run them if needed.

> NOTE: After first installing the app, the hourly searches that simulate the events will not run until the top of the next hour. If you don't want to wait you can click the manual deploy button in the main dashboard

## Demo & Screenshots
[Demo Video](https://drive.google.com/file/d/1rpsDMG0i6JVu6Tnt5a8RBJFCoWSZaYqA/view?usp=share_link)

![Alt text](Main%20Dashboard%20Demo.png)
![Alt text](Main%20Dashboard%20Demo%202.png)


![Alt text](Simulation%20Library%20Demo.png)
![Alt text](Simulation%20Library%20Demo%202.png)


## Adding Custom Simulations
While there are plenty of simulations included, this app was made to customize! If you want to add your own simulations, all you need to do is add them to the appropriate lookup(s) such as `ReadyThreatGo_Datamodel_Endpoint_Process.csv`. You can do this via Splunk's lookup editor app. Note the `enabled` value in each lookup, that needs to be set to `true` for the simulation to run and if you have default simulations you don't want running, you can set that same value to `false`. 

There should be a lookup already for most data types at the datamodel level and there are a few default index-level items such as Wineventlog and XMLWineventlog. Should you need a new data type you will need to make a clone of one of the lookups and its corresponding saved search. From there, you can rename your lookup (be sure to update the new name in the saved search) and add simulations in. Any locally-created lookups and saved searches will remain even when you update the app. 

Adding a new data type for a datamodel requires that you choose the field names for your lookup that will work with the destination datamodel's field calculations. Depending on the tags and constraints required for the datamodel you may have to add a new eventtype in eventtypes.conf and a tag entry in tags.conf where the appropriate tags are added in. It should be easy to copy one of the existing entries in both of those files and change the values you need to. Again, I have already added native support for most common datamodels but this is the process should you need something else. 

Adding a new data type for an index-level simulation requires that you specify the correct sourcetype (and sometimes `source` as well) in the `collect` command in the saved search. This depends on the sourcetype you're using and the TA you'll be passing the simulations to via `collect`. Trial and error is your friend here, check out the default index-level simulation lookups and saved searches such as Wineventlog if you get stuck.

> WARNING: If you make local modifications to any of the default lookups, you need to download/backup your custom copies before updating the app. Updating the app will overwrite any changes you have performed to the default lookups and restore them to the default/updated state. Once you update you can copy your local additions back in via lookup editor.

## Newly Released Features & Future Items
### New Features:
* MITRE mappings & related dashboard panels
* New data types and simulations: `DNS`, `Windows Services`, & `Authentication`
* Simulation ID's and descriptions in the lookups
* Index level simulation support!
   * There are two index-level sourcetypes with simulations out-of-the-box: `wineventlog:security` and `xmlwineventlog:security`. 
   * There is a lookup for each, to add events simply paste the raw event in the "raw" field and be sure to enable it with the "enabled' field.
   * The classic Wineventlog:security sourcetype expects certain fields that a Splunk forwarder would usually add in before sending the event to Splunk. If you have issues with extraction copy the existing entry in the lookup already and change values you need to such as commandline, parent process, etc and you should be all set.
> NOTE: You will need to install the Windows TA on Splunk for these to work correctly. RTG does some compliancy on those events but ultimately passes them to the native TA to get all the calculated fields and extractions real data would. 
* The main dashboard now has a simulation deployment console!
   * This lets you run all or specific simulation searches with a single click, especially if you just installed the app and don't want to wait an hour for the simulations to run.
* New Simulation Library pane to view all the Threat Events in the lookups 

### Future Items:
* `Windows Scheduled Tasks` simulations at index level
* Windows index-level `4624` & `4625` simulations


