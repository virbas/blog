---
layout: single
title: "Hololens + MRTK 2.x Fingers Menu"
date: 2022-11-22
comments: true
tags: "Hololens MRTK2.x Unity UX"

---

Before I move on to MRTK3, I thought it would be nice to release this bird into the wild...

Behold, the Fingers menu.
{% include youtube.html id="CRmsXuNiCOs" %}


That’s right - actions literally at your fingertips. Works really well in cases where you want to have your field-of-view as clean as possible. No solvers or voice commands. Just flip your palms over and have your buttons ready. Each hand works autonomously - no more interactions like holding one panel with one hand and clicking the buttons with the other. During our test sessions we figured out this UX approach works really well. There is no learning curve. Just touch your finger with your thumb..

[Go ahead and test it your self](https://github.com/virbas/blog-hololens-finger-menu).



## How it works
There are two main .cs scripts:
- *FingerMenu.cs* is responsible for checking if the hand is flat and facing the camera. Basically a copy-paste from MRTK's hand-menu slover with addition to tracking the thumb and attaching a proximity light to it.
- *ThumbButton.cs* - positions the button to assigned finger joint and checks the distance of your thumb tip and that assigned finger joint. If the `distance < thumbClickDistanceThreshold` and `itStaysAtThatDistance > clickThreshold`  - rise the click event.

For testing in Unity Editor, you might want to change clone & change the *MixedRealityInputSimulationProfile/Hand Gesture Settings/*
- Default Hand Gesture = Flat
- Left Mouse Hand Gesture = "Pinch" 

## Next Steps
Over the upcoming weeks I plan to port this interaction system to MRTK3. So if you're interested, come back soon to check my progress.

Project code: [https://github.com/virbas/blog-hololens-finger-menu](https://github.com/virbas/blog-hololens-finger-menu).

Comments: [https://github.com/virbas/blog-hololens-finger-menu/issues](https://github.com/virbas/blog-hololens-finger-menu/issues).