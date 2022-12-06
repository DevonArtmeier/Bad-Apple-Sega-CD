# Bad Apple Demo for Sega CD (30 FPS, Fullscreen)
A demo that plays back the [Bad Apple!! shadow art video](https://www.youtube.com/watch?v=UkgK8eUdpAo) at a solid 30 FPS with only 2 colors. Programmed in just 3 days in 2021 for fun.

## How It Works
The video, at 30 FPS and only in 2 colors (black and white), was split into 2 second "packets". Each packet contains 60 video frames, with every 4 frames compressed into 1 by assigning a pixel from each frame to each bit in a palette index. An individual frame is displayed by highlighting the palette indices that use their assigned bit (here's a [video](https://www.youtube.com/watch?v=c-aQvP7CUAI) going into detail). Each set of 4 frames is then compressed again with an LZ-based compression called "Comper". I used H32 mode (horizontal resolution is set to 256 pixels instead of 320), because then a full screen frame would only take up less than half of VRAM. In H40 (the 320 pixel horizontal resolution mode) mode, it would take up more than half, which isn't suitable for double buffering.

While it does that, for every packet, it loads the next packet into memory on the Sub CPU side, and on the Main CPU side, it takes what it currently has, and then for every 8 frames (the length it takes for 1 set of 4 frames to display at 30 FPS), it decompresses a 4 frame set, and piece by piece, loads it into VRAM. Double buffering is used so that it can be loaded offscreen, and then swapped when ready. Word RAM is also set to 1M/1M mode so that both CPUs can have a half of Word RAM to work with.

On the Sub CPU side, after it loads a packet, it then copies the audio data from that packet into a section of PCM wave RAM (double buffered here, too). Testing has shown that loading a packet and loading the PCM data is fast enough to not have to do any weird timing tricks or checks on the Sub CPU. The audio is set to 15360 Hz so that 2 seconds of it would fit rather snuggly in a PCM wave RAM section without filling it up all the way to make room for loop flags.

## Compatibility
Only supports the US region, but the code shoukd work on NTSC console. It won't work on PAL (50 Hz, as opposed to 60 Hz), due to timing differences (video runs slower, and desyncs from the audio).

## Special Thanks
vladikcomper - [Comper compression/decompressor](https://pastebin.com/aMrfKTbz)
