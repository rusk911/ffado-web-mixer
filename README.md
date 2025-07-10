# ffado-web-mixer
A simple websockets server to control internal firewire devices mixers mostly on Dice II based interfaces.

## Why?
There are lot of cheap old firewire interfaces on second hand market because firewire support is removed from recent Windows and MacOS versions. However they are decent pieces of hardware and can be used for some tasks under Linux. For example all DICE II based interfaces have internal 18 ins X 16 outs matrix mixer and flexible signal router. Most of them are supported by ffado-mixer written in python. However it's hard to adjust for some tasks. I used 2 x Focusrite Saffire PRO 40 sound interfaces at rehearsal for IEM monitoring. With this small program you can set up independent stereo buses for each band member and provide easy to use web based mixer interface to use from any smartphone connected to same network.

<img src="https://raw.githubusercontent.com/rusk911/ffado-web-mixer/master/img/screenshot.gif" alt="Screenshot" width="300">

## How?

Use config.json for configuration. There is an example for 7 members band. Should be enough, but one more bus can be added to first device since 2 mixer outputs are unused.

Indexes under "devices" in config.json are devices GUIDs, you can find them from ffado's log output using grep ffado /var/log/syslog or something like that. Or copy from JACK's graph using rename function for a port.
Strings with inputs are not used in script, I have added them just to remember who is using which physical port. I have configured them using ffado-mixer's crossbar router. Then I have adjusted stereo panning in ffado-mixer. Panning is not implementing in web ui but it follows actual panning from ffado-mixer and sets volumes using -6db center rule. All indexes starting from 0. You can simple use this config as template and rename inputs and buses. I assume all guitars and vocals are mono inputs, but drum mix and keyboards are stereo. All buses outputs are stereo. Each device have 10 ouputs so I have configured 2 buses outputs to second device through ADAT.

# Explanation of config

```
{
    "devices": {
        "00130e04048014ef": { // device GUID
            "control": { // A section for control monitor output, a copy of selected bus output will be sent here when [CTRL] button is pressed
                "destinations": ["Line/Out:05", "Line/Out:06"] // Output names from ffado-mixer crossbar router
            },
            "buses": [ // independent buses. may be mono in and mono out, mono in and stereo out, stereo in and stereo out
                {
                    "name": "Vocalist 1 bus", // A label, displayed on mixer channel
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8], // List of indexes from "inputs" section of config. Indexes starting from 0
                    "mixer_outputs": [0, 1], // Mixer output indexes starting from 0
                    "mixer_output_names": ["Mixer/Out:01", "Mixer/Out:02"], // Mixer output names from ffado-mixer crossbar router
                    "destinations": ["Line/Out:01", "Line/Out:02"], // Output destination names from ffado-mixer crossbar router to read peak meters
                    "monitor": { // bus main section
                        "device": "00130e04048014ef", // device to control volume. If multiple devices conected through ADAT it should be device where analog output is connected
                        "outputs": [0, 1], // output indexes startiong from 0, remove this line if a bus have no analog output, for example drums submix
                        "output_names": ["Line/Out:01", "Line/Out:02"] // output names from ffado-mixer crossbar router to read peak meters. Can be mixer inputs if no analog output is set, for example for drums submix
                    }
                },
                {
                    "name": "Vocalist 2 bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [2, 3],
                    "mixer_output_names": ["Mixer/Out:03", "Mixer/Out:04"],
                    "destinations": ["Line/Out:03", "Line/Out:04"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "outputs": [2, 3],
                        "output_names": ["Line/Out:03", "Line/Out:04"]
                    }
                },
                {
                    "name": "Keyboardist bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [4, 5],
                    "mixer_output_names": ["Mixer/Out:05", "Mixer/Out:06"],
                    "destinations": ["Line/Out:05", "Line/Out:06"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "outputs": [4, 5],
                        "output_names": ["Line/Out:05", "Line/Out:06"]
                    }
                },
                {
                    "name": "Guitarist 1 bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [6, 7],
                    "mixer_output_names": ["Mixer/Out:07", "Mixer/Out:08"],
                    "destinations": ["Line/Out:05", "Line/Out:06"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "outputs": [4, 5],
                        "output_names": ["Line/Out:05", "Line/Out:06"]
                    }
                },
                {
                    "name": "Guitarist 2 bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [8, 9],
                    "mixer_output_names": ["Mixer/Out:09", "Mixer/Out:10"],
                    "destinations": ["Line/Out:03", "Line/Out:04"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "outputs": [2, 3],
                        "output_names": ["Line/Out:03", "Line/Out:04"]
                    }
                },
                {
                    "name": "Bassist bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [10, 11],
                    "mixer_output_names": ["Mixer/Out:11", "Mixer/Out:12"],
                    "destinations": ["Line/Out:03", "Line/Out:04"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "outputs": [2, 3],
                        "output_names": ["Line/Out:03", "Line/Out:04"]
                    }
                },
                { // drums submix. It have no analof outputs
                    "name": "Drummer bus",
                    "inputs": [0, 1, 2, 3, 4, 5, 6, 7, 8],
                    "mixer_outputs": [12, 13],
                    "mixer_output_names": ["Mixer/Out:13", "Mixer/Out:14"],
                    "destinations": ["Line/Out:03", "Line/Out:04"],
                    "monitor": {
                        "device": "00130e04048014ef",
                        "output_names": ["Mixer/In:03", "Mixer/In:04"]
                    }
                }
            ],
            "inputs": [ // list of inputs
                {
                    "name": "Vocal 1", // label displayed on input channels
                    "mixer_inputs": [0], // mixer input indexes starting from 0
                    "mixer_input_names": ["Mixer/In:01"], // mixer input names from ffado-mixer crossbar router
                    "sources": ["Mic/Lin/Inst:01"] // Sources names, connected to mixer inputs. Not used in program, just for information.
                },
                {
                    "name": "Vocal 2",
                    "mixer_inputs": [1],
                    "mixer_input_names": ["Mixer/In:02"],
                    "sources": ["Mic/Lin/Inst:02"]
                },
                {
                    "name": "Guitar 1",
                    "mixer_inputs": [2],
                    "mixer_input_names": ["Mixer/In:03"],
                    "sources": ["Mic/Lin/In:03"]
                },
                {
                    "name": "Guitar 2",
                    "mixer_inputs": [3],
                    "mixer_input_names": ["Mixer/In:04"],
                    "sources": ["Mic/Lin/In:04"]
                },
                {
                    "name": "Keyboards",
                    "mixer_inputs": [4, 5],
                    "mixer_input_names": ["Mixer/In:05", "Mixer/In:06"],
                    "sources": ["Mic/Lin/In:05", "Mic/Lin/In:06"]
                },
                {
                    "name": "Bass",
                    "mixer_inputs": [6],
                    "mixer_input_names": ["Mixer/In:07"],
                    "sources": ["Mic/Lin/In:07"]
                },
                {
                    "name": "Drums",
                    "mixer_inputs": [7, 8],
                    "mixer_input_names": ["Mixer/In:08", "Mixer/In:09"],
                    "sources": ["ADAT/In:01", "ADAT/In:02"]
                },
                {
                    "name": "Backing track",
                    "mixer_inputs": [16, 17],
                    "mixer_input_names": ["Mixer/In:17", "Mixer/In:18"],
                    "sources": ["1394/In:01", "1394/In:02"]
                },
                {
                    "name": "Click",
                    "mixer_inputs": [11],
                    "mixer_input_names": ["Mixer/In:12"],
                    "sources": ["1394/In:03"]
                }
            ]
        }
    }
}
```