# foo_out_pulse
Event-based PulseAudio output for foobar2000 using the native TCP protocol, for use in Wine.

## Advantages
Foobar works very well in Wine, but Wine's default Pulseaudio output has some disadvantages:
- Some people report that when performing IO-heavy tasks, such as writing tags in foobar, the
buffer seems to be liable to underrun, causing audio dropouts,
- Existing output components will automatically resample audio to the reported output sample
rate of the playback device. Pulseaudio supports changing the device sample rate to match the
currently playing client even when running in shared mode using the `avoid-resampling` option,
so this isn't optimal
- foobar will have a generic device icon in the system mixer (the true tragedy)

## Shortcomings
Currently
- Doesn't support foobar's volume control (don't hurt your ears)
- Doesn't support authentication via cookies
- Only connects to localhost
- Doesn't properly pass through channel mappings from source files
- Doesn't support connecting via Unix socket like normal programs, unless you build libpulse-0.dll using the libpulse-wine repo. Currently not recommended as in my testing it was unstable and it's not clear that there is any real benefit to using it.

## Setup
At the moment this component tries to connect to 127.0.0.1 using Pulse's native TCP protocol.
First you have to enable the module for this protocol for the Pulse daemon by editing either
`/etc/pulse/default.pa` or `~/.config/pulse/default.pa` and adding or editing the following line:

`load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1`

Understand the security implications of this before making this change.

To minimise the chance of audio dropouts, it is better to disable the option 'Enable smooth seeking, pause and volume changes' in the Output settings of foobar (since version 1.6). When fading is enabled, foobar requests a very small buffer from Pulseaudio as the fading is implemented before the audio is sent to the output, so this is necessary to make seeking seem instant. While this is not a problem under Windows, under Wine it seems problematic. (In the future, smooth seeking can be implemented separately within this component by rewriting the Pulseaudio buffer on seek, allowing the buffer to always remain large enough.)

For the icon to appear in the mixer, you will need to place a png of foobar's icon in, for example, `~/.icons/hicolor/256x256/apps/`



<img src="screenshot.png" width="660">
