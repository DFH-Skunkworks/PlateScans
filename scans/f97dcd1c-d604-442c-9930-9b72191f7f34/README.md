# Plate Scan Results

Plate scan results for plate number: **<module 'uuid' from '/usr/lib/python3.9/uuid.py'>**

Plate scan date: **2022/8/3**

Plate variance: **0.1417**

Plate bin: **AA**

Probe repeatability: **0.0003**

Reference scan variance: **0.0688**

## Plate scan

<img src="./mesh.png" width=800>


## Test setup

When you take a bed mesh in your 3D printer, you are really measuring the
combined effects of the build plate and the motion system. For example, even
if your build plate is perfectly flat, a bowed X axis will make it look as if
the bed is curved! This can make it very difficult to troubleshoot issues,
because you never *really* know if the variance you're seeing is a product of
the build plate itself or coming from somewhere else. 

To help with this, we devised a system to test each build plate against an
A-grade machinist's granite surface plate, giving you a known reference for
your specific build plate.

Plates are scanned using a dedicated flatness tester, essentially a CoreXY
motion system with a dedicated Z probe toolhead sitting on a granite surface
plate. The Z probe uses a mechanical microswitch driven by a leadscrew.

Prior to each scan, the probe is checked for precision using Klipper's
`PROBE_ACCURACY` command.

Then, a reference scan is taken of the surface plate. This reference scan is
used to account for any imperfections in the motion system: the A-grade
surface plate is assumed to be 'true' flat (it is flat across its entire
surface within +/- 0.002 mm), allowing any observed variation to be
substracted from the plate scan itself.

Next, the aluminum bed is placed on the surface plate on top of a thin piece
of foam, and a scan taken. The foam prevents the plate from rocking during
probing (if, for example, the plate is slightly convex on the bottom), which
could lead to unreliable results at the edges. The deviations from the
reference scan are subtracted from the 'raw' scan to remove the effect of the
motion system.

Finally, the 'delta' scan undergoes digital tramming to yield the final
surface mesh. The 'delta' scan may be tilted slightly due to imperfections in
the thickness of the foam under the plate. Thus, we fit a 2D plane through
the points of the 'delta' mesh and take the residuals, setting the new
minimum point to zero. This effectively 'levels' the bed as you would in a
printer, giving us the best approximation of the true surface profile.

## Test data

### Reference scan

<img src="./pre.png" width=400>


### Raw scan

<img src="./post.png" width=400>


### Klipper code

You can paste the following code at the end of your `printer.cfg` in klipper
to save your reference mesh in firmware.

***NOTE: DO NOT USE THIS AS A PRINT MESH!*** 

This is *only a reference* for you to load into your Fluidd or Mainsail mesh
visualizer. It does *not* incorporate any deviations from your printer's
motion system, and **can not** be used as a print mesh!


```

#*# [bed_mesh plate_reference]
#*# version = 1
#*# points =
#*# 	0.111881, 0.068709, 0.044301, 0.029880, 0.026084, 0.053551, 0.109442
#*# 	0.110259, 0.064588, 0.041117, 0.022320, 0.016962, 0.040991, 0.080320
#*# 	0.139906, 0.097985, 0.082013, 0.063843, 0.058484, 0.065013, 0.078405
#*# 	0.141721, 0.117613, 0.103830, 0.100033, 0.094363, 0.094329, 0.120533
#*# 	0.109787, 0.105679, 0.105332, 0.110599, 0.099929, 0.106144, 0.111724
#*# 	0.047871, 0.060638, 0.074979, 0.088995, 0.080513, 0.093916, 0.110433
#*# 	0.000000, 0.015579, 0.026795, 0.040499, 0.045765, 0.058856, 0.081311

#*# tension = 0.2
#*# min_x = 9.00
#*# algo = bicubic
#*# y_count = 7
#*# mesh_y_pps = 2
#*# min_y = 15.00
#*# x_count = 7
#*# max_y = 335.00
#*# mesh_x_pps = 2
#*# max_x = 329.00

```
