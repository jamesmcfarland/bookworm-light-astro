---
title: hacking a chinese photo frame
meta_title: "hacking a chinese photo frame"
description: ""
date: 2026-05-18T11:00:00.000Z
image: "/images/posts/hacking-a-chinese-photo-frame-cover.png"
categories: []
authors:
  - James McFarland
tags:
  - reverse engineering
  - security
  - fun
draft: false
---

I was gifted a cheap Chinese digital photo frame.

As gifts go, this was excellent, because it was also obviously a bit cursed.

On paper it was a modern-ish Wi-Fi photo frame. In reality, it turned out to be an old Android tablet in disguise, wrapped in a thin OEM shell, running a deeply out-of-date and deeply untrustworthy software stack. Devices like that usually go one of two ways: either they are annoyingly locked down, or they are barely held together and surprisingly easy to take over. Thankfully, this one was the second kind.

The end result is much better than I expected going in. The OEM apps are disabled, the frame now boots into a custom launcher app, and that launcher points at a small local web service which proxies and prepares images from Immich for display on the frame. The frame is no longer really a “smart photo frame” in the vendor sense. It is now a single-purpose display terminal that happens to live in a photo frame enclosure.

## The starting point

The device identified itself as a `P105`, manufactured by `ZED`, running Android `6.0.1` with a `2016-07-05` security patch level. At the same time, the build date was from May 2024.

That combination is exactly as dubious as it sounds. In practice it usually means an ancient Android userspace, an ancient kernel branch, a vendor rebuild recent enough to keep shipping units, and very little interest in long-term software quality or security. That suspicion held up almost immediately. Under the skin, the frame was a Rockchip `RK3126` device in the `rk312x` family, with a 32-bit ARMv7 userspace, permissive SELinux, and a very old Android 6 board support package.

In other words: not modern, not secure, but probably hackable.

## The first good sign: ADB was already there

The first thing I wanted to know was whether this was a proper locked appliance, or just a generic Android system wearing a photo-frame costume.

It did not take long to answer that. USB ADB was available straight away, and once I had a shell it became clear that the frame was much closer to “cheap tablet with a custom launcher” than “locked embedded appliance”.

Even better, `su` was present and working.

```bash
adb shell whoami
adb shell su -c whoami
```

That is the sort of result that changes the whole shape of a project. Without root, you are negotiating with a device. With root, you are mostly deciding how patient you want to be.

Wireless ADB was also possible, which made iterating on the device much less annoying once the initial access work was done.

## What was actually on this thing

The OEM experience lived in `com.waophoto.smartframe`, which was installed as a system app and acting as the default HOME launcher. There were also update-related packages like `com.adups.fota` and `android.rockchip.update.service`, which is exactly the sort of thing I do not want on an old, opaque Android device sitting on my network.

The important thing, though, was that the frame was not trapped in some irreversible kiosk mode. Standard Android Settings could be opened over ADB, other apps could be installed, and the launcher behaviour was ordinary enough that it could be replaced cleanly.

That shaped the first phase of the project. I wanted to identify the platform properly, preserve anything useful before I started disabling things, confirm what recovery options actually existed, and avoid bricking the frame before I understood how it had been put together. I pulled partition backups, extracted OEM APKs, and mapped enough of the system to be comfortable experimenting. That mattered because devices like this are often weird in exactly the wrong ways. Standard Android assumptions do not always hold, and in this case standard fastboot was not really the story. `adb reboot bootloader` dropped the frame into a Rockchip-style low-level mode instead, which was useful to know but not something I particularly wanted to rely on unless I had to.

## The alternate firmware route was considered

Before committing to the “work with the existing Android install” path, I did spend some time checking whether a more dramatic route was realistic. That mostly meant answering three questions: was there a real recovery environment, was fastboot usable, and if neither of those was especially good, was there at least a vendor-specific loader mode as a last resort. The answers turned out to be yes, sort of but not really, and yes, in a very Rockchip way.

`adb reboot recovery` brought the frame into a genuine Android recovery screen rather than some fake stub. It had the usual encouraging-looking options, including `Apply update from ADB`, `Apply update from SD card`, `Mount /system`, and `View recovery logs`.

That was the first point where alternate firmware started to feel plausible rather than purely theoretical.

Unfortunately, the hardware then reminded me what sort of device this was. The frame effectively only had one useful physical button, and recovery navigation with it was flaky enough to be borderline unusable. Tiny presses often acted like selections, which meant that while recovery clearly existed, it was not a dependable interface to work through. It was more “good to know this is here if things go badly wrong” than “great, I can comfortably sideload and iterate through recovery”.

Fastboot was even more awkward. In one state the device would enumerate in a fastboot-like way, and in another it would appear as a Rockchip USB device with `2207:310d`, which is much more suggestive of a vendor loader path than a clean Android fastboot workflow. Even when `fastboot devices` showed something, meaningful commands mostly just hung.

That is a useful lesson with old embedded Android hardware: seeing a device in `fastboot devices` does not mean you have a practical flashing workflow. Sometimes it just means you have found another strange half-working interface.

So yes, the alternate firmware path was explored. Recovery mode on the tablet was real. Low-level modes were real. But none of them looked clean or robust enough to justify making them the main plan when ADB plus root in normal Android was already working well.

## Replacing the OEM layer

The easiest first win was to install a proper launcher and a couple of tools so the frame stopped behaving like a captive appliance.

Nova Launcher installed cleanly. So did Firefox, Fully Kiosk Browser, and a small navigation overlay utility to compensate for the ROM’s slightly bizarre navigation situation.

Once a replacement HOME experience was proven to work, I disabled the OEM launcher, the update services, and a couple of factory-test packages rather than deleting anything outright. That was a good example of the general strategy throughout the project: do the reversible thing first. On cheap Android hardware, “I can remove it later” is often much wiser than “I should rip it out immediately”.

## The obvious solution that turned out not to be the right one

At first, `Fully Kiosk Browser` looked like the cleanest way to finish the job. It was already a decent fit conceptually: it can run fullscreen, it can boot into a single URL, and it is built for kiosk-style deployments.

I inspected its config, used its supported import mechanism to push settings onto the device, and got it opening the right URL in the right sort of full-screen mode.

That part worked.

The part that did not work was boot behaviour.

On paper, Fully had the right manifest entries and a boot receiver. In practice, after rebooting the frame it would often just land in Nova instead. Log inspection showed Android trying to deliver `BOOT_COMPLETED` and then refusing to launch Fully because the process was considered bad.

That is a wonderfully cursed old-Android problem. Nothing is obviously wrong, but the firmware and the app do not quite agree on how boot-time process startup should behave.

I also confirmed that Fully had hidden HOME-capable activities inside the APK, and those could be enabled as root. That made it possible to surface Fully as a real launcher candidate rather than just a foreground app.

It was a useful discovery, but also the point where it became clear that I was spending effort making a workaround stack behave like a first-class solution.

That is usually the sign to step back.

## The real constraint was the browser, not the launcher

The bigger issue was not actually launcher selection. It was browser compatibility.

The stock WebView on this device is based on Chromium `44.0.2403.119`.

That is prehistoric.

A lot of modern web apps simply do not target anything remotely that old, and reasonably so. Even if you can install newer apps around the system, the core embedded browsing story is still constrained by an Android 6-era rendering stack and extremely modest hardware.

That changed the design space quite a bit.

Instead of asking “how do I make this frame run a modern web app?”, the better question became:

“What is the simplest possible system I can build that this frame can render reliably?”

That is a much more productive question.

## Building for the device we actually had

The final setup ended up split into two very small pieces: a custom Android launcher app on the frame, and a tiny local server that prepares a slideshow from Immich.

The launcher app is intentionally boring. It registers as `HOME`, opens a configurable URL in a `WebView`, and stays out of the way. I built it specifically to be Android 6-compatible and deliberately stripped it back to plain framework Java rather than dragging in a pile of modern Android dependencies that would only make life harder on this hardware. It has a small settings screen for changing the target URL, a hidden control bar revealed with a triple-tap, and startup logic that waits for network connectivity before trying to load the page.

That last one mattered more than it sounds. On boot, the app often came up before Wi-Fi was ready. An embedded display that fails once and gives up is annoying. One that waits a few seconds and carries on is useful.

## Why a local server made more sense than talking to Immich directly

The slideshow side followed the same principle: keep it simple, and keep the complexity somewhere more capable than the frame itself.

The local service sits between the frame and Immich. It keeps the Immich API key off the frame, proxies image requests, selects and randomises images from a chosen album, normalises images server-side, rebuilds the slideshow queue when the album refreshes, and serves a minimal HTML, CSS, and JavaScript frontend that an ancient browser can actually cope with. That architecture solved a few problems at once.

First, it avoided exposing credentials to a device I do not trust. Second, it removed any need to rely on Immich’s browser-facing behaviour directly. Third, it let the slideshow page be written for the constraints of Chromium 44 instead of the expectations of a modern frontend stack.

The frontend is intentionally plain. No framework, no build-heavy nonsense, no assumption that the browser is anything other than old and slightly grumpy. The viewer preloads the next image, handles timing predictably, and does the bare minimum needed to be a pleasant digital frame rather than a browser demo.

## Why I did not go deeper

There are always more extreme routes with devices like this.

I could have pushed harder on system partition changes, tried to wire in boot scripts, or spent more time exploring Rockchip flashing paths and alternate firmware options. Some of that is probably still viable. But the point of this project was not to prove that the frame could be completely reborn at every layer. The point was to make it useful.

Once I had owner control over the device, the OEM software disabled, a stable custom launcher, a slideshow service designed around the hardware’s real limits, and a clean path into Immich, there was not much value in forcing a more dramatic solution.

That is probably the most important lesson from the whole exercise. Repurposing old hardware is not always about doing the most technically impressive thing. Often it is about finding the simplest architecture that respects the limitations of the device and still produces something genuinely pleasant to use.

## The result

The frame now boots into a custom launcher app, loads a local slideshow endpoint, and displays images sourced from Immich through a small proxy service built for the job.

The Chinese OEM apps are out of the way. The update services are disabled. The device is still old and insecure, and I would not trust it with anything sensitive, but it no longer needs to be trusted very much. It is doing one job, on my terms.

That feels like the right ending for hardware like this: repurposed and on an isolated VLAN!

Not a perfect rescue. Not a full custom ROM. Just a slightly sketchy object, understood well enough to be useful again.
