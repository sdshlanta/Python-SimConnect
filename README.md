[![PyPI version](https://badge.fury.io/py/SimConnect.svg)](https://badge.fury.io/py/SimConnect)
# Python-SimConnect

Python interface for Microsoft Flight Simulator 2020 (MSFS2020) using SimConnect

This library allows Python scripts to read and set variables within MSFS2020 and trigger events within the simulation.

It also includes, as an example, "Cockpit Companion", a flask mini http server which runs locally. It provides a web UI with a moving map and simulation variables. It also provides simulation data in JSON format in response to REST API requests.

Full documentation for this example can be found at [https://msfs2020.cc](https://msfs2020.cc) and it is included in a standalone repo here on Github as [MSFS2020-cockpit-companion](https://github.com/hankhank10/MSFS2020-cockpit-companion).

## Python interface example

````
from SimConnect import *

# Create SimConnect link
sm = SimConnect()
# Note the default _time is 2000 as to refreshed at 2s
aq = AircraftRequests(sm, _time=2000)
# Use _time=ms where ms is the millsec to refresh data to cash.
# setting ms to 0 will disable data cashing and allwas pull new data form sim.
# There is still a timeout of 4 trys with a 10ms delay between checks. 
# If no data is received in 40ms the value will be set to -999999
# Each Requests can be fine tuned by seting the time pram.
# To find and set time out of cashed data to 200ms

altitude = aq.find("PLANE_ALTITUDE")
altitude.time = 200

# Get the aircraft's current altitude
altitude = aq.get("PLANE_ALTITUDE")
altitude = altitude + 1000

# Set the aircraft's current altitude
aq.set("PLANE_ALTITUDE", altitude)

ae = AircraftEvents(sm)
# Trigger a simple event
event_to_trigger = ae.find("AP_MASTER")  # Toggles autopilot on or off
event_to_trigger()

# Trigger an event while passing a variable
target_altitude = 15000
event_to_trigger = ae.find("AP_ALT_VAR_SET_ENGLISH")  # Sets AP autopilot hold level
event_to_trigger(target_altitude)
sm.quit()
exit()
````

## HTTP interface example

Run `glass_server.py` using Python 3.

#### `http://localhost:5000`
Method: GET

Variables: None

Output: Web interface with moving map and aircraft information

#### `http://localhost:5000/dataset/<dataset_name>`
Method: GET

Arguments to pass:

|Argument|Location|Description|
|---|---|---|
|dataset_name|in path|can be navigation, airspeed compass, vertical_speed, fuel, flaps, throttle, gear, trim, autopilot, cabin|

Description: Returns set of variables from simulator in JSON format


#### `http://localhost:5000/datapoint/<datapoint_name>/get`
Method: GET

Arguments to pass:

|Argument|Location|Description|
|---|---|---|
|datapoint_name|in path|any variable name from MS SimConnect documentation|

Description: Returns individual variable from simulator in JSON format


#### `http://localhost:5000/datapoint/<datapoint_name>/set`
Method: POST

Arguments to pass:

|Argument|Location|Description|
|---|---|---|
|datapoint_name|in path|any variable name from MS SimConnect documentation|
|index (optional)|form or json|the relevant index if required (eg engine number) - if not passed defaults to None|
|value_to_use (optional)|value to set variable to - if not passed defaults to 0|

Description: Sets datapoint in the simulator

#### `http://localhost:5000/event/<event_name>/trigger`
Method: POST

Arguments to pass:

|Argument|Location|Description|
|---|---|---|
|event_name|in path|any event name from MS SimConnect documentation|
|value_to_use (optional)|value to pass to the event|

Description: Triggers an event in the simulator

## Runing SimConnect on other system.

#### Note: At this time SimConnect can only run on Windows hosts.

Creat a file called SimConnect.cfg in the same folder as your script.
#### Sample SimConnect.cfg:
```
; Example SimConnect client configurations
[SimConnect]
Protocol=IPv4
Address=<ip of server>
Port=500
```
To enable the host running the sim to share over network, 

add \<Address\>0.0.0.0\</Address\>

under the \<Port\>500\</Port\> in SimConnect.xml

SimConnect.xml can be located at
#### `%AppData%\Microsoft Flight Simulator\SimConnect.xml`

#### Sample SimConnect.xml:
```
<?xml version="1.0" encoding="Windows-1252"?>

<SimBase.Document Type="SimConnect" version="1,0">
    <Descr>SimConnect Server Configuration</Descr>
    <Filename>SimConnect.xml</Filename>
    <SimConnect.Comm>
        <Descr>Static IP4 port</Descr>
        <Protocol>IPv4</Protocol>
        <Scope>local</Scope>
        <Port>500</Port>
        <Address>0.0.0.0</Address>
        <MaxClients>64</MaxClients>
        <MaxRecvSize>41088</MaxRecvSize>
    </SimConnect.Comm>
...
```

## Events and Variables

Below are links to the Microsoft documentation 

[Function](https://docs.microsoft.com/en-us/previous-versions/microsoft-esp/cc526983(v=msdn.10))

[Event IDs](https://docs.microsoft.com/en-us/previous-versions/microsoft-esp/cc526980(v=msdn.10))

[Simulation Variables](https://docs.microsoft.com/en-us/previous-versions/microsoft-esp/cc526981(v=msdn.10))


