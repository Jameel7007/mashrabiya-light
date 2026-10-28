# Mashrabiya Light

A quiet room facing a carved wooden mashrabiya screen. Sunlight passes through the lattice and
through a few panes of jewel-coloured glass, and lays star-shaped light, white and coloured, across
the floor and walls. Move the sun from a rose dawn through white noon and a deep orange sunset into
the blue hour.

One HTML file, no build step. [three.js](https://threejs.org) r160 is loaded from jsDelivr.

## Using it

- **Desktop:** move the cursor across the room (or press ← →) to move the sun.
- **Phone:** drag across the room. On an iPhone, tap **Use tilt** and allow motion access to steer
  the sun by tilting the phone (this needs the page to be served over HTTPS, as on GitHub Pages).
- **Top toolbar:** four lattice patterns (eight-pointed stars, six-pointed stars, twelve-pointed
  rosettes, turned wood) and the sound button. Sound starts on the first tap or click and can be
  turned off; both choices are remembered. On an iPhone the ring/silent switch also mutes it.

## How it works, in plain terms

**The lattice.** The star patterns use Hankin's method: tile the plane with polygons (octagons and
squares; hexagons; dodecagons, hexagons and squares), then from the middle of every edge draw two
lines into the polygon at a fixed "contact angle". Where the lines meet they make stars: 67.5°
in the octagons gives the eight-pointed {8/3} star, 60° gives hexagrams and twelve-pointed
rosettes. The turned-wood pattern is the classic Cairene grid of round spindles with beads.

**The light mask.** The whole screen is drawn once into a canvas texture: colour is the colour of
light let through (white for an opening, a jewel tint for glass, nothing for wood). Every surface
in the room asks "if I look back along the sunlight, what part of the screen do I see?" and reads
that texture. It reads it at two depths, which gives the screen its thickness, and from blurrier
copies (mipmaps) the further the surface is from the screen, which makes shadows soft at a
distance the way real sunlight does.

**Finding the glass.** To glaze a pattern, the lattice is drawn small in black and white and
flood-filled: every connected white area is one opening. Each opening's centre is checked against
simple rules (petals of this rosette ruby and amber, the ring around it lapis, and so on) and the
chosen colours are painted into the light mask. This works for any lattice, so all four patterns
share it. A single sheet behind the wood shows the glass glowing where the mask is tinted.

**Light beams and dust.** For each pixel (at half resolution) the beam pass steps along the view
ray and adds up how much sunlight, in which colour, crosses it, with a slowly drifting 3D noise
for the dust. Bright areas and the beams get a soft bloom; a gentle vignette finishes the frame.

**The view outside.** Each pixel of the sky follows its own ray from the camera and checks where
it crosses upright planes: palms at 16 and 24 metres, a minaret, dome and roofline at 190 metres.
The shapes are drawn with distance functions and softened by distance, like a lens focused on the
screen, and they shift against the lattice as the camera drifts.

**The floor.** Large slabs of polished cream marble with soft veins. Besides the sunlight, each
floor pixel bounces its view ray off the stone, finds where it meets the screen and reads the light
mask there, blurred with distance, so the glowing screen and its glass are mirrored softly in the
floor.

**The day.** Sun colour and the sky's overhead and horizon colours are keyframes along the sun's
path. The view is shown dimmer than its real brightness so it reads through the gaps instead of
burning white.

**Sound.** Everything is synthesised with the Web Audio API. A fountain loop (a soft wash of water
and hundreds of tiny rising "bubble" chirps) and a cricket loop are rendered in a background
worker; birds are scheduled live and follow the time of day: a dawn chorus, a drowsy noon with a
laughing dove, crickets in the blue hour.

**Performance.** Phones render at 1.35× their CSS pixels (not their full 3×) with fewer beam
steps; large desktop windows are held to about four million pixels. If the frame rate stays below
about 54 fps, resolution and beam detail step down. Phones that cannot render to float textures get
an 8-bit path that stores colours in sRGB so dark areas do not band.

## Running it locally

Any static file server works, for example:

```bash
python3 -m http.server 8000
```

then open http://localhost:8000. Optional URL parameters for testing:

| Parameter | Effect |
|---|---|
| `?t=0.5` | jump to a time: 0 sunrise, 0.5 noon, 1 sunset, 1.05 blue hour |
| `?p=2` | open with a pattern: 0 eight-pointed, 1 six-pointed, 2 twelve-pointed, 3 turned wood |
| `?q=1` | force a render scale and turn off automatic scaling |
| `?ldr` | force the 8-bit path that phones without float textures use |
