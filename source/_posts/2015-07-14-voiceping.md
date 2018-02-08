---
layout: post
title: "VoicePing"
description:
date: 2015-07-14 01:59:22 +0800
categories: real-time
keywords: "voiceping, real-time"
---
Lesson learned while building [VoicePing](https://www.linkedin.com/pulse/my-new-startup-voiceping-available-google-play-store-zhou-wenhan).

It’s neither about pursuing the big ideas nor technical challenges associated to building a highly mutable state machine with multitude of side effects.

Not even about achieving the elegant architecture of cooperating microservices which would scale to billion clients.

Yes, at some point the above big goals will seem to be the overall purpose or the end result of a real-time system. But right now:

80% are about making sure things are working as expected at the right time for:

1. Consumers of the services (mobile clients).
2. End users of the mobile clients.

20% are about making sure every little part of the software should be written and configured in the most understandable way, with as little overhead as possible in terms of memory or processing resources.
