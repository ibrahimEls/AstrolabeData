# AstrolabeData

Particle data behind the interactive 3D figure on
[ibrahimelsharkawy.com/blog/astrolabe-flow-matching.html](https://ibrahimelsharkawy.com/blog/astrolabe-flow-matching.html):
reduced particle sets from the Astrolabe simulations, one folder per case,
served from this repository through GitHub Pages. Built by
`tools/build_astro3d.py` in the site's repository from the Astrolabe web3d
export.

    movies/<group>/<name>.mp4, .jpg    the cinematic movies for the post's gallery, re-encoded from
                                       the masters for the web (H.264, two-pass at ~14 Mbps, 24 for
                                       the TNG pair; 1920x1080, no sound), each with a poster frame
    index.json                         the cases, for the figure's menu
    <case>/figure.json                 case, camera, colour scale, fields, files
    <case>/<field>/series_a<a>.u16     positions at one keyframe, uint16 x 3, in units of the box
    <case>/<field>/series_a<a>_h.u8    smoothing length per particle, uint8, log scale
    <case>/<field>/z0.u16, z0_h.u8     a larger set at z = 0, same encoding
    <case>/<field>/halo.u16, halo_h.u8 every particle within 2.5 R200m of the most massive halo,
                                       positions relative to the halo centre

Fields: `truth` (the N-body simulation) and the Astrolabe fields — `small`
and `medium` for the 2M-particle boxes; `small_pre` (the 2M-trained model
applied as is) and `small_ft` (fine-tuned on TNG50) for the TNG runs, which
are z = 0 only. Particles are matched by index across fields and keyframes,
which is what lets the figure interpolate between keyframes and draw both
sides of its comparison from one camera.

Encoding: `series`/`z0` position = value / 65536 * box; `halo` position =
(value / 65535 * 2 - 1) * cutout radius, about `halo_centre_kpc_h` of the
field; h = h_min * (h_max / h_min)^(value / 255), the distance to the 8th
nearest exported neighbour. All little-endian, no header. Lengths are
comoving kpc/h. `figure.json` carries the colour scale: `lo`/`hi` are the
20th and 99.98th percentiles of log10(column density / mean column) in the
z = 0 truth frame at the movie's starting view, computed the way the
cinematic movie script does it.
