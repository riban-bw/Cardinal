# OSC Remote Control

OSC (Open Sound Control) is a protocol commonly used for remote control over the network.  
Starting with version 23.08, Cardinal allows remote control of the entire patch/project and individual parameters through OSC.

## Activating remote control

Make sure you are using version 23.08 or later of Cardinal, start up the standalone (both Native and JACK variants will work) and under "Engine" menu click on "Enable OSC remote control". OSC is enabled by default in headless build.

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

#### /clear

Sending a `/clear` message will remove all cables and modules.

Cardinal replies back indicating either success or failure, using `/resp` path with `s:"clear" s:"ok"|"fail"`` message.

#### /load b:patch-blob

Sending a `/load` message with blob parameter will load the patch file contained in the message.  
Patch contents must be in compressed format, not plain-text json.

Cardinal replies back indicating either success or failure, using `/resp` path with `s:"load" s:"ok"|"fail"`` message.

#### /load s:file-path

Sending a `/load` message with string parameter will load the patch file pointed to by the message.
The full path and filename must be passed for a valid patch file.

Cardinal replies back indicating either success or failure, using `/resp` path with `s:"load" s:"ok"|"fail"`` message.

#### /get_modules

Sending a `/get_modules` message will request a list of all modules in the rack.

Cardinal replise back using `/resp/module` path with `u:moduleId` message. (One message for each installed module.)

#### /get_module_info h:module_id

Sending a `/get_module_info` message will request a information about a module.

Cardinal replise back using `/resp/module_info` path with `u:moduleId s:pluginSlug s:modelSlug i:numParams i:numInputs i:numOutputs i:numLights` message.

#### /add_module s:plugin s:model

Sending a `/add_module` message will add a new module to the rack.

Cardinal replies back if the module is added with the module's ID, using `/resp/module` path with `u:moduleId` message.

#### /add_module s:plugin s:model f:posX f:posY

Sending a `/add_module` message with these extra parameters will add a new module to the rack at the sepcified position.

Cardinal replies back if the module is added with the module's ID, using `/resp/module` path with `u:moduleId` message.

#### /remove_module h:module_id

Sending a `/remove_module` message will remove the specified module.

Cardinal replies back indicating either success or failure, using `/resp` path with `s:"remove_module" s:"ok"|"fail"`` message.

#### /get_cables

Sending a `/get_cables` message will request a list of all cables.

Cardinal replies back using `/resp/cable` path and `h:cable_id h:src_module_id i:output h:dst_module i:input` message (one message for each cable).

#### /get_input_cables h:moduleId i:input

Sending a `/get_input_cables` message will request a list of cables connected to the specified input.

Cardinal replies back using `/resp/cable` path and `h:cable_id h:src_module_id i:output h:dst_module i:input` message (one message for each cable). Due to a limitation of rack/cardinal, there is only one cable connection permitted to an input.

#### /get_output_cables h:moduleId i:input

Sending a `/get_output_cables` message will request a list of cables connected to the specified output.

Cardinal replies back using `/resp/cable` path and `h:cable_id h:src_module_id i:output h:dst_module i:input` message (one message for each cable).

#### /add_cable h:srcModuleId i:srcOutputId h:dstModuleId i:dstInputId s:color(optional)

Sending a `/add_cable` message will add a new cable and connect it to the specified source module's output and the specified destination module's input.
The optional `color` parameter may be supplied in the format "#rrggbb". If no color is provided then the next color is used.

Cardinal replies back if the cable is added using `/resp/cable` path and `h:cable_id h:src_module_id i:output h:dst_module i:input` message (one message for each cable).

#### /remove_cable h:cableId

Sending a `/remove_cable` message will remove the specified cable.

Cardinal replies back using /resp path with `s:"remove_cable" s:"ok"|"fail"` message.

#### /remove_cable h:srcModuleId i:output h:dstModuleId i:input

Sending a `/remove_cable` message with these extra parameters will remove the cable connected between the specified source module's output and the specified destination module's input.

Cardinal replies back using /resp path with `s:"remove_cable" s:"ok"|"fail"` message.

#### /host-param i:port f:value

Sending a `/host-param` message will set a port value of the "Host Params" module.  
The port index starts from 0.

There is no reply back from Cardinal.

#### /param h:moduleId i:paramId f:value

Sending a `/param` message will change the parameter value of any loaded module.  
(TODO: describe a way to find the module and param id)

There is no reply back from Cardinal.

NOTE: the first argument must of be int64 type, as regular 32-bit integer is not enough to fit the whole range of values used inside Cardinal/Rack.

#### /param h:moduleId i:paramId

Sending a `/param` message without value will request the value of the parameter.

Cardinal replies back indicating either success or failure, using `/resp/param` path and `h:moduleId i:paramId f:value` message.

