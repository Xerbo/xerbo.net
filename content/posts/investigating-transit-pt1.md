---
title: "Investigating Transit - Part 1"
date: 2021-01-03T15:17:44Z
draft: false
tags:
  - vhf
  - zombie
---

The story starts in the 1960s with a fleet of satellites called NAVSAT, a satellite constellation for military navigation. While satellites were only launched between 1956 and 1988, one satellite in the constellation has refused to die and entered "zombie" status, enter Transit-5B5.

Transit-5B5 (launched in 1964) is the oldest "working" satellite, broadcasting an intermittent signal when not eclipsed in the 137MHz band. Interestingly enough, its actual navigation systems failed with in 19 days of launch, and it only continues to broadcast telemetry. Below is an image of Transit-5B5's downlink as seen in SDR# (image from [happysat.nl](http://happysat.nl)), this post will attempt to do basic analysis of one of the downlinks.

![Panadaptor and waterfall view of Transit's signal](/img/Transit5B5.jpg)

The process of receiving the bird is made significantly harder due to the current season and drastically lower amount of sunlight (in fact I gave up around the 22nd of December, so no new data has been acquired since then). During the times in which I was receiving I would generally use gqrx (using the `Raw I/Q` modem) with Gpredict to automatically record the passes, since this satellites comes from an era before sensitive radio receivers any SDR and antenna should be capable of picking up its signal.

The signal itself seems to be composed of 2 subcarriers which then are FM modulated, one of the signals appears to be PAM and is not the focus of this post, the other appears to be FSK with 1.2khz deviation and baud rate of around 398 bps (although the rate drifts)

After the recording had been made it was processed by a GNURadio flowgraph, the basic procedure for decoding is: FM demodulation -> shift part of interest to DC -> low pass filter -> FSK demodulation -> write to file

![GNU Radio flowgraph for demodulating transit](/img/TransitDemod.png)

(flowgraph downloadable from [here](/flowgraphs/Transit%20Demodulator.grc))

Basic binary analysis reveals a very strong repeating 256 bit sequence.

![Bitview of transit showing a frame counter](/img/TransitBinView.png)

You can easily see that later in the binary file there is a frame counter incrementing, although it seems to start and stop at random times. Occasionally though the satellite just completely gives up and only transmits a constant stream of 0/1's.

![Bitview showing Transit giving up at modulating data](/img/TransitGivingUp.png)

Why showing that the satellite is no longer fully functional (perhaps the intermittence is due to the satellite spinning) it does show that the satellite's internal systems are still somewhat working, apart from this the contents of what is contained is completely unknown.

Although the satellite is supposed to have a few interesting instruments on board, which if figured out could provide valuable information on the ageing of certain instruments like LES-5.

Some links to resources/information (if you find any more useful resources feel free to email me):

1. [http://happysat.nl/Deadsat/1964-83D.pdf](https://web.archive.org/web/20210909152311/https://happysat.nl/Deadsat/1964-83D.pdf)
2. [http://happysat.nl/Deadsat/index.html](https://web.archive.org/web/20220116201547/http://happysat.nl/Deadsat/index.html)

## Conclusion

There is none...

There is still an awful lot to figure out about Transit, the equipment on board and its downlink, which will only come with time, data and analysis.
