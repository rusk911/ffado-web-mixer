# ffado-web-mixer
A simple websockets server to control internal firewire devices mixers mostly on Dice II based interfaces.

Use config.json for configuration. There is an example for 7 members band. Should be enough, but one more bus can be added to first device since 2 mixer outputs are unused.

I used 2 x Focusrite PRO 40 sound interfaces, interconnected with ADAT optical cables and connected to old Thinkpad T420 in daisy chain mode.
In UbuntuStudio 24.04 it's just working out of the box using pipewire with ALSA snd_dice backend. Yes it has huge latency but it's stable enough and since all monitoring is done using internal mixers, it's enough for the task.
