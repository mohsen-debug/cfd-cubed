---
layout: post
title:  "Using TUI for Animation"
date:   2020-07-25 08:49:11 +0200
categories: Fluent
---
### 1. Configure image output

As the sequence of images are saved for certain number of time-steps or 
iterations, it is utmost important to make sure the output images
are consistence. This is done via the following commands in the journal file:

```
/display/set/contours/filled-contours yes
/display/set/picture/driver png
/display/set/picture/landscape yes
/display/set/picture/x-resolution 1440
/display/set/picture/y-resolution 1000
/display/set/picture/color-mode color
/display/set/windows/text/date yes
/display/set/windows/text/visible yes
/display/set/windows/text/border no
/display/set/windows/main/border no
/display/set/colors/graphics-color-theme black
/display/set/line-weight 2
```

The commands are rather self-explanatory, but for clarity, they setup the file
extension to `png`, fix the resolutions, make sure the time is visible and set
the color theme for images.

### 2. Create contours

After setting up the images, the second step is to create the contour plots.
This is perhaps the straightforward phase that includes selecting the 
surfaces on which the flow variables are to be displayed and defining details
of the contour plots. Here is an example for generating contours of gas volume
fraction:

```
display/set/contours/surfaces
inlet_diffusion
plane-normy-5mm
plane-xy
capsule
wall_electrode
wall_pin-air_zone
wall_pin-part-solid
wall_probe
wall_tube-air_zone
wall_tube-part-solid

/display/set/contours/n-contour 25
/display/set/contours/auto-range no
/display/set/contours/clip-to-range no
/display/views/restore-view iso
/display/views/auto-scale
/display/contour air vof 0 0.15
```

A neat trick is to save the contour plots for `t = 0 s`, so the movie starts at
time zero of the simulation.

```
/display/save-picture airvof_%t.png
```

### 3. Setup `execute-commands`

The last step is to use `execute-commands` feature of Fluent to save snapshots 
during the simulation for different time instances. One can save pictures at 
different time-steps or iterations and always remember to enable that command!

```
/solve/execute-commands/add-edit command-3 30 "time-step" "/display/contour air vof 0 0.15"
/solve/execute-commands/enable command-3
/solve/execute-commands/add-edit command-4 30 "time-step" "/display/save-picture airvof_%t.png"
/solve/execute-commands/enable command-4
```

And that's pretty much all. 