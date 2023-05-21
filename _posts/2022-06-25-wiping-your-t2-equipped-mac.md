---
title: Wiping your T2-equipped Mac
date: 2022-06-25T10:32:34.000Z
categories: [Mac]
tags: [security]
---

# Erasing an Apple Mac equipped with a T2 security chip

## Part 1. The adventure begins!

This story begins when I needed to prepare my Intel Macbook Pro (2018, 13" with Touchbar) to go in for repairs, to have the keyboard replaced due to the
[well-known](https://support.apple.com/en-gb/keyboard-service-program-for-mac-notebooks) repeating/non-responsive keys issue. I started by making a Time Machine backup of the laptop,
in preparation for doing a fresh install of macOS. I wasn't about to send in my laptop with any of my personal information on it after all. This is where my problems began...

I had downloaded the latest version of [macOS Big Sur](https://support.apple.com/en-gb/HT201475), and prepared a bootable USB drive containing the installer as per
[Apple's instructions](https://support.apple.com/en-gb/HT201372). I intended to boot from it, however, I ended up opting to use the built-in macOS Recovery System (`Cmd + R`).
I opened up Disk Utility, and formatted/wiped the entire drive in the Macbook. As it turns out, this is a **bad idea** to do on a T2-equipped Mac! This is because, as I understand it,
the encryption keys for the T2 chip are stored *somewhere* on the drive. When you format the drive, you also end up wiping out the Recovery Partition, and this sensitive information,
thus requiring an external reinstall.

When you reboot after having wiped the drive, you're presented with a globe icon, and the option to connect to a Wifi network. This is Apple's
[Internet recovery](https://support.apple.com/en-gb/HT204904) system. I then attempted to boot up from the USB drive I'd created (by holding down Alt (`Option`) on startup), however,
I had no luck with this. After a bit of research I realised that the likely cause was that I had neglected to enable USB booting in the
[Secure Startup Utility](https://support.apple.com/en-us/HT208198), via Recovery Mode, prior to wiping the system.

There seemed to be no way of enabling it without Recovery Mode, which I no longer had access to. At this point I resigned myself to using the Internet Recovery option, which
will download a basic recovery mode image, and then allow the installation of macOS from the Internet. Of course, this would be a lot slower than installing it via a USB drive.

Unfortunately, Internet Recovery was to be very intermittent and the errors it gave (e.g. `-2101f`) didn't help very much. I ended up trying a few things, such as removing the device
from the 'Find My' app via my phone, and from my account via the iCloud website (even though I had disabled [Find My](https://support.apple.com/en-gb/HT201065), and signed out of iCloud
before starting this process!). I tried it via both ethernet connection and Wifi. Eventually, after many attempts and much patience, it succeeded and allowed me to install a new copy of
macOS! I did this, setup the guest account for Apple, and sent the laptop in for repairs... 🎉

## Part 2. The adventure continues

Four weeks later, I finally got the laptop back (no thanks to UPS!), and that's where the real fun began... and what prompted me to write this blog post.

For security reasons, I wasn't about to trust a device that had been in someone else's control, even Apple's. For this reason, I decided to first test it out,
the keyboard had been replaced and it worked just fine. I then planned to wipe the drive again, and do a fresh install of macOS. This time, I thought I'd learnt my lesson, and I made
sure to boot into Recovery Mode first, and enable booting from USB devices via the secure startup utility. I then proceeded to wipe the drive from Disk Utility in Recovery Mode again,
and hope for the best. 🤞

Once again, I was presented with the Internet Recovery globe upon reboot. Well, *that's strange*, I thought. I then proceeded to try my USB drive in every USB port, and... nothing!
I still only received the Internet Recovery startup screen. At this point I thought there might be something wrong with my USB or the installer (I had created it from my M1 Mac Mini,
and this was an Intel Macbook). So, I downloaded Big Sur and created a new bootable USB from my old Intel based Macbook. This didn't work either!

I tested both the USB drives in my other systems, and they were detected and presented as bootable options just fine. I'm still not entirely sure why this happened, my only guess is that
I should have also lowered the Secure Boot *security level* in the security startup settings screen where I enabled USB booting. My advice here is to beware of this if you have a
T2-based Mac device. Double check that you can boot from the USB drive you created **before** doing anything drastic, like formatting your main drive!

At this stage I'd decided that I wasn't going to get USB booting working, and resolved to relying on the Internet Recovery option again. This had worked before, albeit after many attempts.
I tried it multiple times over a few days, using Wifi and Ethernet (with different adaptors and switch ports), and each time it failed after between 30 minutes and 2 hours. It always
failed with the `-2101f` [error](https://discussions.apple.com/thread/250498411). Which, if you look that up online, doesn't lead you to anything too useful. Only advice saying to create a
bootable USB, which obviously wasn't going to work in my case.

I did some research and found a few standard suggestions, such as
[resetting the NVRAM/PRAM](https://www.macworld.com/article/224955/how-to-reset-a-macs-nvram-pram-and-smc.html), resetting the SMC, and so on, but none of these worked, nor behave like they
were supposed to. I even went as far as following Apple's instructions to [revive or recover an Apple device](https://support.apple.com/en-gb/guide/apple-configurator-2/apdebea5be51/mac)
using a USB-C cable attached to another Mac device, via the Apple Configurator software. I followed the instructions and performed a *revive* on the Macbook. This was an interesting
experience, and something that I didn't realise was possible! However, it didn't seem to help at all in my case.

I eventually found a few forum threads, and one [blogpost](https://discussions.apple.com/thread/4400711?answerId=19999477022#19999477022), with people having similar issues. The blog post I
found noted that all `-XXXX` error numbers from Internet Recovery indicated some kind of network issue. They suggested trying Wifi, as Apple ethernet adaptors can be unreliable, and if all
else fails, to try it on a completely separate network, such as the hotspot from your iPhone. With nothing to lose, I setup my mobile hotspot, and connected my Macbook to it. This time the
progress bar on the Internet Recovery screen **actually moved**! Albeit, ever so slowly, and the 'estimated time remaining' number also started decreasing. However, after a few hours of
very little progress, I gave up on this option.

I tried to think what else it could be... perhaps something on my ISP's side, or to do with my own network, that was blocking Internet Recovery from working? This is when I realised that in
between doing this for the first time, I had replaced my Ubiquiti USG with a [Protectli Vault](https://protectli.com/vault-4-port/) running **pfsense**! I hadn't customised much in pfsense
yet, besides for adding some recommended extensions, so I thought it unlikely to be the problem, but this was my only lead. I decided to disable both
[pfBlockerNG-Devel](https://protectli.com/kb/how-to-setup-pfblockerng/), and **Snort IDS**, on the firewal and try again. This time, much to my surprise, via Ethernet the progress bar
moved appreciably and the time remaining also decreased steadily. This eventually failed, but with a different error this time, `-5101F`! I decided to try it once more, using Wifi this time,
and it worked! It successfully booted up into Recovery Mode! I left it installing Big Sur overnight, and woke up to a fresh install! 😁

## Summary

After many wasted hours, and some help and encouragement from [@Em0nTw1tter](https://twitter.com/Em0nTw1tter) and [@HypnInfosec](https://twitter.com/HypnInfoSec), I decided to make
the most of things and to write a blog post with what I'd learnt. Hopefully, this post will help someone else, and save them from many wasted hours of frustration and confusion!

**Takeaways from my experience:**

1. Formatting the internal drive is a lot more dangerous to do on a T2-equipped Mac than on an older Mac, (or a PC).
2. If you plan to install macOS via USB, first enable booting to USB from Recovery Mode, then lower the Secure Boot security level. Make sure that you **TEST THAT IT WORKS**.
3. If you run into Internet Recovery errors (specifically high negative numbers), make sure your network is allowing the traffic through.
  - Try using Ethernet and Wifi
  - Try using another network to determine if the problem is within your network
5. Make sure you have a Time Machine (or other) backup before starting.
6. Be sure to leave yourself enough time, Internet recovery takes hours, even when it works.

If you made it this far, thanks for reading! 😊 I hope you found this post useful.


## References

1. [How FileVault and the T2 Security Chip work together in newer Macs](https://www.macworld.com/article/234494/how-filevault-and-the-t2-security-chip-work-together-in-newer-macs.html)
2. [Apple Platform Security - Apple Support](https://support.apple.com/en-gb/guide/security/welcome/web)


