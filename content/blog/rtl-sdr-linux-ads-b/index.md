---
title: "Receiving ADS-B Messages on Linux"
date: 2025-01-19T22:41:34-05:00
thumbnail:
    src: "/blog/rtl-sdr-linux-ads-b/media/_imagethumb.jpg"
    visibility:
        - list
categories:
    - "_Category"
tags:
    - "_tag1"
    - "_tag2"
draft: true
---
Preview Text

<!--more-->

## Hardware Setup

Lenovo ideapad Flex 14 running Linux Mint

RTL-SDR v4 kit with dipole antenna

## Software setup

RTL-SDR drivers installed via `sudo apt install rtl-sdr`.

[rtl-sdr blog](https://www.rtl-sdr.com/adsb-aircraft-radar-with-rtl-sdr/) pointed me to [antirez's github page](https://github.com/antirez/dump1090)

Followed instructions (clone -> make), Had to install librtlsdr-dev package to get make file to work

Running `./dump1090` starts the rtl-sdr with pre-defined settings, and scrolls through messages as they are received.

Running `./dump1090 --interactive` brings up a clean table interface in the terminal. This will scan for traffic and populate the table as messages are received.

Adding `--net` will start a small webserver to view planes on a map. This is a nice visualization, but the plane icons are rendered pretty small, making it hard to spot them amongst the underlying map features.


