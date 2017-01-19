# Orbiwise connector

## About Orbiwise
Orbiwise is an IoT company that offers a full network software solution for LoRaWAN-based networks, which can scale to support millions of devices. Orbiwise's network server connects loRaWAN devices to the outside word, either through API exposed by the server or through packet propagation (invocation of callbacks registered at the server).

## Purpose of the connector
The connector wraps the APIs that are exposed by the Orbiwise server and allow you to seamlessly communicate with your loRaWAN devices through the Orbiwiser server, as if they were objects of your scriptr.io applications.   

## Components
- /orbiwiseserver: defines the OrbiwiseServer class that exposes all the methods you need to intereact with your devices
- /client: a generic HTTP client that handles the communication with the Orbiwise server
- /config: the configuration script, where to specify the URL of your Orbiwise server, your Orbiwise credentials and your callbacks.

## Configuring the connector

You can configure your application in the connector using the "/config" file:
- Specfify the URL of the Orbiwiser server using the "orbiwiseUrl" variable,
- Specify your Orbiwise credentials using the "username" and "password" variables. These will be used by default if no credentials ae passed to the OrbiwiseServer constructor (see below). 
- You can use the default value for the callback URL. If you have subscribed to the Orbiwise automatic packet forwading option (see below), this URL is automatically invoked by the Orbiwise server whenever updated data is available. Not that this value will also be used by default if no alternative configuration is passed to the OrbiwiseSerer constructor.
```
var orbiwiseUrl = "The URL of the Orbiwise server";

var username = "your_username";
var password = "your_password";

var callback = {
  
  host: "https://electronica.scriptrapps.io", 
  port: 443,
  path_prefix: "/api/lora/update/rest/callback/payloads/ul",
  auth_string: "Bearer A_VALID_SCRIPTRIO_AUTH_TOKEN",
  retry_policy: 1
};
```

## Using the connector

### Instantiating the connector

The first thing to do is to create an instance of the Orbiwise Server. From a script, require the orbiwiseserver script then create an instance of the aforementioned class:
```
var orbiwise = require("../modules/orbiwise/orbiwiseserver");
// option 1: use the default configuration
var defaultOrbiwiseServer = new orbiwise.OrbiwiseServer(); 
// option 2: use the a custom configuration
var customOrbiswiserServer = new orbiwise.OrbiwiseServer({username:"xxx", password:"yyyy", callback: {// some object}});
```

### List all available devices (nodes)
```
var nodes = customOrbiswiserServer.listNodes();
```
#### Get latest payload for a given node
```
var deviceId = nodes[0].deveui; // deveui is the property that identies a node
var payload = customOrbiswiserServer.getLatestPayload(deviceId);
```
#### List all available payloads for a given device
```  
var payloadList = customOrbiswiserServer.listPayloads(deviceId);
```
#### Register callback to receive data updates
The preceding methods allow you to explicitely ask for a device's latest payload. You can also register a callback to the Orbiwise server that will automatically forward updates to the given callback URL. The registerCallback method can be invoked without passing any parameter, which will make it use the value of the "callback" variable of the "./config" script. You can also choose to overwrite the default callback by providing a specific configuration and thus, register multiple different callbacks.
```

