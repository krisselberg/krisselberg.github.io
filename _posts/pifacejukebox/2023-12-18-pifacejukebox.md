---
key: blog
title: Pi Face Jukebox
date: "2023-12-18T0:51:03.284Z"
excerpt: "A RaspberryPi program that plays theme songs based on recognized faces"
---

I wanted to prank my friends, and I thought what better way to do it than make a program that recognizes them and plays an embarrassing/funny song when they walk into a room. Check out some of their reactions:

<iframe width="560" height="315" src="https://youtu.be/r57Zn2JfX4s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I started off by recruiting my friend [Jasper Levy](https://github.com/jaslevy) to build it to work on macOS with the webcam. It had some hiccups (see video below), but still, overall, worked really well with training an SVM on face embeddings extracted from photos I had scavenged of my friends from their social media accounts. See the [GitHub repo](https://github.com/jaslevy/FaceRecogThemeSongenator) to check out how it works.

<iframe width="560" height="315" src="https://youtube.com/shorts/4BjLPnus9k8?feature=share" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

However, I didn't want to leave my computer in some random room while the program ran, so I attempted to get it running headless on a Raspberry Pi. There were many challenges along the way. The first Raspberry Pi I got was a knockoff, so I wasn't able to connect it to a display (spent hours troubleshooting SD cards, external monitors, and the power source before getting to the inevitable step of finding another Raspberry Pi to test with). I started off working on getting the device to automatically connect to a bluetooth speaker with `bluetoothctl`. Then, I moved the files to my Raspberry Pi using Google Drive. Looking back, a quicker way to do this would be to SSH into the Raspberry Pi and use `sch` to transfer files (this is what I do now when I want to add a newly trained SVM model).

Once, this was ready, I worked on translating the code to work with the Raspberry Pi Camera Module. The first step was downloading the proper packages (which was a bitch as not all newer packages are compatible with Raspberry Pi 3). With a few other tweaks (using `espeak` instead of macOS's `say` to greet people, optimizing resolution - 640x480 - for good speed and accuracy, and tweaking the confidence threshold), the program was working on the Raspberry Pi. It was simple to get the program working headlessly. All I had to do was SSH into the Raspberry Pi from a computer and run the main program. BANG, it works, made a few friends' days, and helped me understand Raspberry Pi devices more. Check out the final repo [here](https://github.com/krisselberg/PiFaceJukebox).
