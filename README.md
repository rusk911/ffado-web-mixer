# ffado-web-mixer
A simple websockets server to control internal firewire devices mixers mostly on Dice II based interfaces.

Use config.json for configuration. There is an example for 7 members band. Should be enough, but one more bus can be added to first device since 2 mixer outputs are unused.

I used 2 x Focusrite PRO 40 sound interfaces, interconnected with ADAT optical cables and connected to old Thinkpad T420 in daisy chain mode.
In UbuntuStudio 24.04 it's just working out of the box using pipewire with ALSA snd_dice backend. Yes it has huge latency but it's stable enough and since all monitoring is done using internal mixers, it's enough for the task.

Indexes under "devices" in config.json are devices GUIDs, you can find them from ffado's log output using grep ffado /var/log/syslog or something like that.
Strings with inputs and outputs names are not used in script, I have added them just to remember who is using which physical port. I have configured them using ffado-mixer's crossbar router. Then I have adjusted stereo panning in ffado-mixer. Panning is not implementing in web ui but it follows actual panning from ffado-mixer and sets volumes using -6db center rule.
