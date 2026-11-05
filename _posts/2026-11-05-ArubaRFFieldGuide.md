---
title: "Building an RTL-SDR Field Guide for Aruba"
date: 2026-11-05 13:30:00 -0400
categories: [Projects, Radio]
tags: [SDR, Aruba, RTL-SDR, RF, Projects]
---

Most software-defined radio guides assume you live in the US or Europe. The hardware
advice travels; the frequencies, the airport channels, the marine traffic and the legal
framework do not. Aruba is small enough that nobody had written the local version, so I
built one.

![Band plan of Aruba from 24 MHz to 1.7 GHz](/assets/img/aruba-rf-bandplan.png)

**The guide is live at
[thankful-forest-023e9360f.5.azurestaticapps.net](https://thankful-forest-023e9360f.5.azurestaticapps.net/).**

## What it is

A legal, reproducible RTL-SDR field guide for Aruba:

- **67 curated signals** — FM/AM broadcast, the Reina Beatrix airband, marine VHF, AIS,
  amateur repeaters, ADS-B, NOAA/Meteor/GOES weather satellites, GPS, ISM and the hydrogen
  line. Every row has a mode, bandwidth, legal status and a source URL.
- **A Python scanner** that drives `rtl_power`, parses the raw CSV, estimates a local noise
  floor, finds peaks and matches them against the catalogue. Each run produces a Markdown
  report, `peaks.csv`, JSON metadata and two figures.
- **An audio capture command** for `rtl_fm` with AM/NFM/WFM/USB/LSB and metadata.
- **Ten legal experiments** — from reading RDS on the FM dial to capturing a NOAA APT pass
  and attempting the 1420 MHz hydrogen line.
- **A blog and reference section** with the Aruban legal notes and the full source list.

The whole thing is a private GitHub repository with CI, a strict docs build and a deploy
workflow to Azure Static Web Apps. The guide is public; the code and raw scans stay
private while the catalogue is being verified.

## The legal line

An RTL-SDR is receive-only. It has no transmit hardware, and the project never asks it to
transmit. That matters in Aruba: the **Telegraaf- en telefoonverordening** (AB 1996 GT 2)
requires permission for transmitting and receiving apparatus, but Article 4(2) says
permission for a receiver can only be refused in narrow circumstances, and Article 4a
exempts type-approved equipment in the public register. The practical reading is that
ordinary reception is normal; transmitting without authorisation is not.

The guide deliberately excludes public-safety, security, encrypted business radio, pagers,
phone calls and cellular user traffic. It also does not publish live AIS/ADS-B tracks of
named private vessels or aircraft. The public services are interesting enough.

## The signals worth starting with

- **118.000 MHz** — Beatrix Tower at Queen Beatrix International. AM, 25 kHz. The current
  eAIP lists 118.000 as tower and 120.900 as approach; a lot of older hobby pages get this
  backwards.
- **1090 MHz** — ADS-B. Every modern airliner broadcasts identity, altitude, speed and
  position in the clear.
- **161.975 / 162.025 MHz** — AIS. Cruise ships, cargo and fishing boats around Oranjestad
  and Barcadera.
- **137.100 MHz** — NOAA 19 APT. A weather satellite image downlinked as an analogue audio
  signal.
- **1420.405751 MHz** — the hydrogen line. About 30 dB below the noise floor of a bare
  RTL-SDR, which is what makes it a good antenna and integration project.

## How the scan pipeline works

`rtl_power` writes a matrix of sweeps × frequency bins. The naive approach — median across
time per bin — fails on a continuous carrier because the carrier becomes the "noise". The
scanner instead estimates a local floor from a wide slice of neighbouring frequencies on
each sweep, then takes the median over time. Peaks are grouped into candidates with
frequency, bandwidth, power, SNR and persistence. A catalogue match is labelled as a
hypothesis, not an identification.

The report includes a section called **"What this scan cannot tell you"** because an 8-bit
receiver with a 25 kHz bin is not a spectrum analyser. The raw CSV is always kept next to
the report.

## What is next

The first real survey from Oranjestad: FM dial, airband, marine and 1090 MHz, captured with
the same antenna and gain, with the raw CSV committed next to the report. Then the same
from San Nicolas, and a comparison.

If you are in Aruba and you have an SDR, the most useful thing you can do is tune to a
frequency in the guide and tell me what you actually hear. The island is the source of
truth.

**[Read the guide →](https://thankful-forest-023e9360f.5.azurestaticapps.net/)**
