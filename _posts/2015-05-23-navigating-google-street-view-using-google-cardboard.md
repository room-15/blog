---
layout: post
title: Navigating Google Street View Using Google Cardboard
post_author: cygery
---

In this post, a simple method to navigate in Google Streetview while using Google Cardboard is presented.
It makes use of the automation tool [Tasker](http://tasker.dinglisch.net/) and the touch input 
record/replay tool RepetiTouch Pro (requires root) although probably for both apps free alternatives 
with the required capabilities exist.

### Google Cardboard

[Google Cardboard](https://www.google.com/get/cardboard/) is a simple virtual reality platform 
which makes extensive use of a smartphone, especially its display, to work.
There exists already a variety of different models by a couple of manufacturers.
Typically, Cardboard provides a magnetic button on the side for input.

### Google Streetview's Virtual Reality Mode

The Google Streetview app features an Easter egg which makes it usable with VR glasses like Cardboard.
After starting Streetview at a supported location via the Google Maps app this mode is activated 
by double-clicking on the button to switch between automatic and manual rotation in the bottom 
right corner of the screen.
In this VR mode, the screen is split in half, providing one image each on the left and right half for 
both eyes.
Using VR glasses like Cardboard this results in a (admittedly modest) VR experience.

However, there's an issue: Streetview uses touch input for navigation. When using a VR headset, 
tapping the screen is usually difficult or even impossible.
Unfortunately, the Streetview app doesn't make use of the magnetic button of Cardboard, either.

### Using the Magnetic Button

Cardboard's magnetic button works by influencing a magnetic field. This can be detected by the phone's 
magnetic sensor and translated into a button click event.
However, this detection must be built into an app which isn't the case for Streetview.

Fortunately, Tasker can monitor the magnetic field strength and makes it available in the `%MFIELD` 
variable.
Here, a simple method is shown to detect when the magnetic button is pressed.
Please note that this method isn't fool-proof.
Presses might be missed or presses might be detected wrongly.
Thus, this method should be used with caution.
Generally, it might be wise to stop the detection when it's not used, e.g., outside of the Streetview app.

#### Monitoring the Magnetic Field

In Tasker, setup an Event profile which detects when the `%MFIELD` variable is set (to an arbitrary value).
The corresponding task is described in the next section.

#### Detect Significant Changes to the Magnetic Field

This method detects when the magnetic field changes significantly as it's the case when the 
magnetic button is pressed. 
The task makes use of a user-set variable (`%MFIELD_OLD`) and looks as follows:

    Variable Set (%diff To %MFIELD-%MFIELD_OLD)
    If %diff > 1000
        <do action>
    End If
    Variable Set (%MFIELD_OLD To %MFIELD)

This task detects when the magnetic field increases by more than 1000 between two measurements.
Tasker measures the magnetic field once per second so it takes a moment until a button press is 
recognized.
The value 1000 is based on tests using a Nexus 5. 
On other devices, a different value might be better.

### Navigating in Streetview

To navigate in Streetview, double taps are used.
In this example, RepetiTouch Pro is used to record and save such a double tap.
Then, in the Tasker task shown above, a "start replay" action using the recorded double tap is 
inserted at `<do action>`. 
Thus, whenever a significant increase in the magnetic field strength is detected by Tasker a 
double tap is performed causing the Streetview app to navigate in the current direction.

That's it!
