# How I Fixed My PSVR2 PC Tracking

Hey, I love my PSVR2. The hardware is amazing and affordable. But for a long time, the tracking on PC was so unstable for me that it almost made me lose interest for PCVR with it:

* HMD tracking would drop every few seconds
* Constant "cannot find play area" errors
* Controllers lagging, disappearing, or drifting away

It was borderline unusable. But after some testing, I finally reached a point where I can play for long sessions with only a few hiccups.

Here's what I did to fix it

---

## Table of Contents

1. [HMD Tracking](#hmd-tracking)
2. [Environment and Lighting](#environment-and-lighting)
3. [Controller Stability and Bluetooth](#controller-stability-and-bluetooth)
4. [Quick Recovery Steps](#quick-recovery-steps)
5. [How Tracking Works Technical Breakdown](#how-tracking-works-technical-breakdown)
6. [My Setup For Reference](#my-setup-for-reference)

---

# HMD Tracking

## The Thorough Scan Method

This was the biggest improvement to HMD tracking stability.

### Step 1: Start With a Clean Slate

Delete your existing play area. Let the headset build a fresh map.

### Step 2: Scan Your Room Slowly

Initially, I incorrectly assumed that I had to scan the room before "Scan Complete" appeared, don't worry about this prompt, just keep going and focus on a good scan.

Turn your body a little at a time while keeping your head still. After each small turn, stop your body. Then slowly move your head up and down to let the headset capture the room in that section. Keep repeating this pattern until you have covered the entire room. It should feel like you are carefully painting every surface with your view.

https://github.com/user-attachments/assets/2ba12857-95fe-44f8-9ea7-baca3ef386c7

### Step 3: Do Not Worry About The "Scan Complete" Prompt

Even when the setup says "Scan Complete", keep looking around. I usually scan for about one more full minute.
> The headset's onboard SLAM (Simultaneous Localization and Mapping) is <i>always</i> working to map your environment. The initial setup process is just a guided way to build the map. By continuing to scan slowly, you are feeding the SLAM system more data, resulting in a much more robust and detailed map.

### Step 4: Press Done After You Are Satisfied

When you feel the headset saw everything you will be playing near, press done.

> **Tip:** The spot where you begin the scan seems to become a major anchor point. Try to keep that part of your room in view frequently during play.

---

# Environment and Lighting

The HMD cameras are basically the headsets eyes. They perform far better if:

* the room is bright
* there are unique visual features to keep track of
* reflective or moving objects are reduced

### Check Lighting With Passthrough

Enter passthrough using the passthrough button.
If the view looks noisy or grainy, the room is too dark.

### Improve Lighting

* Use bright room lights
* Avoid direct sunlight hitting lenses
* Open curtains for soft light
* For dark rooms, IR illuminators should work well. They flood the room in infrared light, that is invisible to us, but looks like daytime to the cameras.

### Environments That Cause Tracking Problems

* Large blank walls
* Shiny floors or glass surfaces
* Mirrors
* Anything reflective that creates false features

Adding posters, textured decor, or anything with stable detail can help. Inside out tracking heavily benefits from interesting rooms.

---

# Controller Stability and Bluetooth

Laggy movement, disappearing controllers, and tracking loss mostly all are likely cased by Bluetooth issues

## Step 1: Use Reliable Bluetooth Hardware

### Best Option: Internal Bluetooth(/Wifi) Cards

These have better antennas, better shielding, and lower latency.

Recommended:

* Intel AX200
* Intel AX210
* Qualcomm QCNCM865

Most onboard Bluetooth/Wifi cards will work great for PSVR2, I personally use the AX210.

### Dongles Are Mixed

Avoid these due to unreliable v2 hardware revisions:

* TP Link UB500
* ASUS USB BT500

Reported good ([@Kanzaji](https://github.com/Kanzaji):

* Zemxte BT 5.4 with external antenna

### Dongle Best Practices

If you must use a dongle:

* Use USB 2.0
* Use a short extension cable
* Keep it away from your tower and USB 3 ports
* Do not place it next to active 2.4 GHz devices

Unplugging or moving other USB 3.0 devices away helps

## Step 2: Reduce 2.4 GHz Interference

Bluetooth controllers fight for bandwidth with:

* wireless mice, keyboards, headphones, speaker
* other 2.4 GHz dongles
* 2.4GHz routers
* USB 3 ports causing 2.4GHz interference

Unplug anything you do not need during VR.
This helped me more than expected.

## Step 3: Do Not Use Bluetooth Audio From Your PC

When your Bluetooth adapter handles audio and two motion controllers at the same time, something has to suffer. Usually it is controller tracking.

### Best Fix: Use Wired Audio

Plug wired headphones straight into the PSVR2 3.5 mm jack.

### If You Must Use Bluetooth Audio

You can do what I do, and use your phone for audio instead:

1. Pair your headphones to your phone
2. Use AudioRelay (or something similar) to stream PC audio to the phone

There will be some latency, but your controllers will track properly.

## Step 4: WiFi Adjustments

Generally, use ethernet if you can for PCVR. If you need to use WiFi like I do:
* Connect your PC to 5 GHz instead of 2.4GHz
* If your router sits next to your PC, you can temporarily disable its 2.4 GHz band during VR sessions

---

# Quick Recovery Steps

Even with everything perfect, the occasional dropout can still happen.

### Controller Drops, disappears, or Floats Away

Double tap the passthrough button (enter/exit).
* This immediately resyncs at the maximum duty cycle and almost always resolves controller issues instantly.
Otherwise, it is just assumed that the controllers went out of view and will take 8-12 seconds to come back.

If there's still an issue, ensure that you're using the [@RealSupremium](https://github.com/RealSupremium) custom-controller-sync build
> You can find this build in the artifacts of this workflow build: [custom-controller-sync](https://github.com/BnuuySolutions/PSVR2Toolkit/actions/runs/18515528451)

With the custom-controller-sync build, reconnect the controllers to recalibrate and resync.
If necessary, enter/exit passthrough.

### HMD Loses Play Area

While entering passthrough fixes controller drops, in my case, it does nothing for lost HMD tracking

1. Hold the headset power button to turn it off
2. Reboot the headset

If your HMD tracking issues are caused by a USB issue, a reboot will ensure that everything is running smoothly again

After a thorough room scan, HMD tracking loss should become rare.

---

## How Tracking Works (Technical Breakdown)
Understanding how the system works can make it easier to troubleshoot issues.

### Headset Tracking: On-Device SLAM

The PSVR2 uses four cameras and an onboard processor to run a SLAM (Simultaneous Localization and Mapping) algorithm.

*   SLAM builds a 3D map of your environment using stable visual features (like posters, corners, etc).
*   The headset then constantly compares what its cameras see to that internal map to determine its exact position and orientation.

A more detailed initial scan gives the SLAM system a higher quality map to work with, which directly improves position stability, orientation accuracy, recovery from occlusion, and resilience to sudden movements.

### Controller Tracking: Hybrid Optical + IMU

Each Sense controller uses a hybrid system:

1.  **IMU (Inertial Measurement Unit):** An accelerometer and gyroscope inside each controller report fast motion and orientation changes. On their own, they are prone to "drift" and become inaccurate over time.
2.  **LED Rings:** The headset's cameras track the pattern of infrared LEDs on the controller rings. This provides an absolute, drift-free position in space.

The system combines the fast, low-latency data from the IMU with the slower, but more accurate, position data from the cameras to provide smooth and precise tracking.

#### Controller Syncing: Why Stock Tracking Can Fail

The main point of failure is perfectly synchronizing the IMU data with the camera's sighting of the LEDs.

The stock driver attempts this using an unreliable method involving network packet timings and a hardcoded offset to *guess* the Bluetooth latency. In practice, this is not accurate enough due to the unpredictable nature of Bluetooth scheduling, especially with the Windows Bluetooth stack. It's a major reason for controller drift and freezes.

The **custom sync build** (with patches from [@RealSupremium](https://github.com/RealSupremium)) uses a much more concrete method. It performs a visual calibration step every time a controller connects:

1.  The driver flashes the controller's LEDs in a specific, timed pattern.
2.  The headset's cameras visually detect this light pattern.
3.  By knowing exactly when the flash command was sent and when the cameras *saw* the flash, the driver can calculate the true, real-world motion-to-photon latency for your specific Bluetooth adapter.

This allows for a much more accurate synchronization between the fast IMU data and the absolute position from the camera frames. This calibration is why tracking takes a few extra seconds to initialize when controllers are connected using this build, but results in far more stable tracking.

> You can find this build in the artifacts of this workflow build: [custom-controller-sync](https://github.com/BnuuySolutions/PSVR2Toolkit/actions/runs/18515528451)

---

# My Setup For Reference

* Bluetooth and WiFi card: Intel AX210
* PSVR2 driver: PSVR2Toolkit build with [@RealSupremium](https://github.com/RealSupremium)'s custom-controller-sync patches
  * Without these patches, my controllers constantly desynced even with a perfect Bluetooth connection. This driver build is great for reliable, and smoother controller tracking

---

This combination of a detailed room scan, improved lighting, strong Bluetooth hardware, and removing 2.4 GHz interference made my PSVR2 PC experience worthwhile.

Feel free to open an Issue or Pull Request if you have ideas for improvements.
