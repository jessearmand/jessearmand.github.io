---
title: '2020: symmetrical year'
date: 2020-01-05 09:16:40
tags: robotics, fpv, drone, quadcopter, engineering, software
---

It has been a long time since I wrote anything, and I have decided that I had to at least once in 2020. As it's the only year in my lifetime that is symmetrical. Do numbers mean anything? Probably yes or no, either way I have recently worked on more robotics related software than a consumer app on iOS. I still wrote an iOS app to interface with a backend running on a headless computer such as Raspberry Pi, Intel NUC, NVIDIA Jetson or it can be any kind of computer. I have to admit there aren't many nice solution out there. Because most robotics software came out from research lab, and it doesn't often consider smartphone as their frontend. There isn't a lot of software engineers working in the robotics industry apparently for a valid reason.

If we talk about robotics, probably an arm manipulator, roomba-like differential drive, humanoid, or a dog robot came into our imagination. But, those are not the only robot. A "drone" can be a robot. A car could also be a robot. The only element needed to drive them is sequence of commands to achieve semi or fully autonomous behavior, without us controlling every aspect of their movement.

Now, I thought robotics is cool. Yes, it's cool to have a computer to move on its own. But, when we delegate our responsibilities to autonomous robots, our job is done. We don't consider them as exciting anymore. Who would be excited with a washing machine for an example? Unless a robot could interact with us like a human or animal does, it's boring.

As I have worked with drones for a few months, another world has revealed itself to me. The first drone I have touched is a DJI Matrice 100. It's an amazing piece of equipment, as I never worked with an aircraft before. However, after a while as any other piece of equipment, it's not something that I get excited over.

I guess the main reason is the difficulty with which to use it on various activities. It's too large, noisy, and although it's programmable, how far can we get with it without years of work, to make it on par to the latest and significantly cheaper drone on the market?

Then, I have also tried the newer DJI Mavic 2 Pro. It's much better, more stable, and easier to control. It does what it's designed to do (flying, taking photos and videos), but it's not an open platform where you can replace the hardware or software. But, just last year [Skydio 2](https://www.skydio.com/) was released. It has far better autonomous flying capabilities, and for some reason they'd rather state that they're using NVIDIA Jetson TX2 as a computer. This is not the case with Mavic. Though DJI is using an Ambarella vision processor but they don't advertise it anywhere in their marketing materials. Skydio is another drone targeted at Mavic's customer base. It's also not a programmable drone. at least for the moment.

Now, if I want to build a fully autonomous drone or I prefer to call it quadcopter — as it's a multirotor with 4 motors driving its propellers — the only way is by building it myself. But, while I have a thought of doing that, I found that quite a few of DIY builders are doing it for racing or freestyle flying with an FPV (First Person View) goggles. I found this activity to be very active and growing, and you can search on the internet to find various setup of racing / freestyle quad with various configuration. It's also an art in flying, video shoot.

I could consider that a racing / freestyle quad and FPV goggles has pushed the miniaturization in electronics and innovation in radio transmission technology which an autonomous drone project has not achieved so far or they are moving at a slower rate. The differentiating factor is the kind of people or flyers that used them. An autonomous drone require heavy computation, and there aren't a lot of hobbyist in programming and electronics, while an FPV hobby on the other hand is more appealing to anyone who is seeking an adrenaline rush. Anyone with little background in electronics and software can enter the hobby with persistent practice. There's always those who seek thrill, thus there are always demands. It's not very hard to manufacture electronics that could drive motors aggressively. But, it's very hard to manufacture electronics that can simultaneously drive motors and do a lot of heavy matrix calculations at real time, in other words to put a GPU on a mini or micro quadcopter.

The other side of casual user is probably better served with a stable drone that flies by itself, which is so far only Skydio that could achieve this. Any Mavic series so far is relatively okay at obstacle avoidance but it's very limited, you need to fly it at a certain height to track you. Also you can't send Mavic for an autonomous mission without good GPS signal. Because in order to move itself without GPS it needs a point of reference. At the moment it's just the person flying it, or a car. While ideally it should build a map of its environment from its own motion and visual processing a.k.a SLAM, and that's what Skydio is doing so far and why it has an amazing tracking and obstacle avoidance capability.