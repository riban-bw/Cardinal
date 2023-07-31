# OSC Remote Control

OSC (Open Sound Control) is a protocol commonly used for remote control over the network.  
Starting with version 23.08, Cardinal allows remote control of the entire patch/project and individual parameters through OSC.

## Activating remote control

Make sure you are using version 23.08 or later of Cardinal, start up the standalone (both Native and JACK variants will work) and under "Engine" menu click on "Enable OSC remote control".

NOTE: This option is not available when using Cardinal as a plugin, only for standalones.

![screenshot](Docs_Remote-Control-1.png "Screenshot")

This will ask you for which network port to use, Cardinal will default to 2228.  
Valid range is typically between 1025 and 32767.  
If unsure just stick the default value.

Depending on the OS security features you might be asked to allow network usage at this point.

![screenshot](Docs_Remote-Control-2.png "Screenshot")

If all went well opening the "Engine" menu again should show a checkmark, indicating that OSC remote control is enabled.

For the moment there is no error dialog or information in case things go wrong.  
If you are unable to connect, make sure your OS network firewall settings allows opening port 2228.

## Available messages

The following OSC messages are available:

#### /hello

Sending a `/hello` message will make Cardinal reply back with another hello, using `/resp` path and "hello" message.  
Useful when testing if the connection works.

#### /host-param i:port f:value

Sending a `/host-param` message will set a port value of the "Host Params" module.  
The port index starts from 0.

There is no reply back from Cardinal.

#### /param h:moduleId i:paramId f:value

Sending a `/param` message will change the parameter value of any loaded module.  
(TODO: describe a way to find the module and param id)

There is no reply back from Cardinal.

NOTE: the first argument must of be int64 type, as regular 32-bit integer is not enough to fit the whole range of values used inside Cardinal/Rack.

### /get_param h:moduleId i:paramId
*NOT YET IMPLEMENTED*

Sending a `/get_param` message will request the value of the parameter.

Cardinal replies back indicating either success or failure, using `param` path and `h:moduleId i:paramId f:value` message.

#### /load b:patch-blob

Sending a `/load` message with blob parameter will load the patch file contained in the message.  
Patch contents must be in compressed format, not plain-text json.

Cardinal replies back indicating either success or failure, using `/resp` path and "load" message.

### /load s:file-path

Sending a `/load` message with string parameter will load the patch file pointed to by the message.
The full path and filename must be passed for a valid patch file.

Cardinal replies back indicating either success or failure, using `/resp` path and "load" message.

### /add_cable h:srcModuleId i:srcOutputId h:dstModuleId i:dstInputId s:color

Sending a `/add_cable` message will add a new cable and connect it to the specified source module's output and the specified destination module's input.
The optional `color` parameter may be supplied in the format "#rrggbb". If no color is provided then the next color is used.

Cardinal replies back indicating either success or failure, using `/resp` path and "load" message.

### /remove_cable h:cableId

Sending a `/remove_cable` message will remove the specified cable.

There is no reply back from Cardinal.

### /remove_cable h:srcModuleId i:srcOutputId h:dstModuleId i:dstInputId
*NOT YET IMPLEMENTED*

Sending a `/remove_cable` message with these extra parameters will remove the cable connected between the specified source module's output and the specified destination module's input.

There is no reply back from Cardinal.

### /get_cable h:moduleId i:portId i:index
*NOT YET IMPLEMENTED*

Sending a `/get_cable` message will request info about the specified cable attached to the port (input / output). The `index` parameter specifies which cable to request (0..quantity of cables).
If `index` is omitted then a response will be sent for each cable attached to the port. I `portId` is omitted then a response will be sent for all cables attached to the module. If `moduleId` is omitted then a response will be sent for all cables in the current patch.

if a cable exists then Cardinal replies back using `/cable` path and `h:cable_id h:module_id i:port_id s:color` message.

### /add_module s:plugin s:model i:position i:rack
*NOT YET IMPLEMENTED*

Sending a `/add_module` message will add a new module to the rack at the sepcified position. If `position` and `rack` are omitted then the module is added after the last module.

Cardinal replies back indicating either success or failure, using `/module` path and `u:moduleId i:position i:rack`  message.

### /add_module h:module_id
*NOT YET IMPLEMENTED*

Sending a `/remove_module` message will remove the specified module.

Cardinal replies back indicating either success or failure, using `/module` path and `u:moduleId i:position i:rack`  message.

### /remove_module i:position i:rack
*NOT YET IMPLEMENTED*

Sending a `/remove_module` message with these extra parameters will remove the module at the specified position.

There is no reply back from Cardinal.

### /get_modules i:position i:rack
*NOT YET IMPLEMENTED*

Sendind a `/get_modules` message will request info of the module at the specified position. If position and rack are omitted then a response will be sent for each installed module.

Cardinal replies back using '/module' path and `u:moduleId i:position i:rack` message.
