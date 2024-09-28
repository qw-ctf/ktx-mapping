Packaging maps for the Hub
--------------------------
If a zip archive is appended to a BSP file FTE will load
this as a map-specific filesystem. This allows for bundling
lit files, locs, external textures etc.

Here's an example of how to package external content for
schloss while trying to keep the end result as small as
possible:

```
$ du -sh textures/schloss
 68M	textures/schloss

# Convert to JPEG with for example graphicsmagick to trim size.
$ for x in textures/schloss/*.png; do
  gm convert -strip -quality 85 -define jpeg:dct-method=float $x ${x/png/jpg};
  rm $x;
done

$ du -sh textures/schloss
 12M	textures/schloss

# Add all relevant external files to a zip archive.
$ zip -r -9 schloss3.zip textures/schloss maps/schloss.lit locs/schloss.loc
  adding: textures/schloss/ (stored 0%)
  adding: textures/schloss/162.jpg (deflated 1%)
  ...
  adding: textures/schloss/147.jpg (deflated 0%)
  adding: maps/schloss.lit (deflated 56%)
  adding: locs/schloss.loc (deflated 81%)

# Append the zip file at the end of the bsp file.
$ cat maps/schloss.bsp schloss.zip > schloss.bsp

# Verify content.
$ unzip -t schloss.bsp
Archive:  schloss.bsp
warning [schloss.bsp]:  2197772 extra bytes at beginning or within zipfile
  (attempting to process anyway)
    testing: textures/schloss/#teleport.jpg   OK
    testing: textures/schloss/001.jpg   OK
    ...
    testing: textures/schloss/skydark_solid.jpg   OK
    testing: maps/schloss.lit         OK
    testing: locs/schloss.loc         OK
No errors detected in compressed data of schloss.bsp.
```

The same treatment goes for skyboxes:
```
unzip -t rctf3_b2.bsp
Archive:  rctf3_b2.bsp
warning [rctf3_b2.bsp]:  24406144 extra bytes at beginning or within zipfile
  (attempting to process anyway)
    testing: gfx/env/sc_terranova_bk.jpg   OK
    testing: gfx/env/sc_terranova_dn.jpg   OK
    testing: gfx/env/sc_terranova_ft.jpg   OK
    testing: gfx/env/sc_terranova_lf.jpg   OK
    testing: gfx/env/sc_terranova_rt.jpg   OK
    testing: gfx/env/sc_terranova_up.jpg   OK
    testing: locs/rctf3_b2.loc        OK
No errors detected in compressed data of rctf3_b2.bsp.
```