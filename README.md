# FairyLinux #

The FairyLinux Single-File Linux distribution

## Obligatory Penguin ##

![The LittleBlue Fairy Penguin](misc/fairylinux.png)

Fun facts about this little guy:

1. The original inspiration for the Linux penguin was, in part, because Linus Torvalds
   himself was [bitten by a fairy penguin while in Australia](https://en.wikipedia.org/wiki/Tux_(mascot)).
2. The more-modern name for these guys are "Little Blue Penguins."
3. The even-more-modern name for these guys are just "Little Penguins."
4. They don't actually have fairy-style wings and hang about in the clouds :-)

## Introduction ##

I'm old enough to remember how to make a boot disk on 68k-era Macs:

1. Copy the System file to the floppy disk
2. Copy the Finder file to the floppy disk

...and that's it.

MS-DOS?  Sure:

1. SYS A:

Under the covers, it put IO.SYS, MSDOS.SYS, and COMMAND.COM on the
floppy (with maybe just a bit of tinkering with the boot record), but
that was pretty much it.  Don't get me started on the variations of IBM
DOS, FreeDOS, and so on.

In 2026, there are fancy installers and programs like unetbootin, rufus,
and the horribly inefficient Windows USB Disk Creation Tool, among others.
But I pine for a return to those halcyon days of early OSes that just required
a copy or two...

Enter FairyLinux:  The One-File Linux Distro

## How It Works ##

### Short version ###

0. Have a non-ancient PC with an EFI BIOS. :-)
1. Put the ONE FairyLinux file on any drive in /EFI/BOOT/BOOTX64.EFI.
2. Reboot and convince your EFI BIOS to boot from that drive.
3. Enjoy the ridiculousness of running a reasonably-complete Linux
   distribution from ONE file on disk.

### Longer Explanation ###

Modern EFI BIOSes (i.e. pretty much every PC made in like 10+ years) have a
special case where they will boot any file on a drive (USB, HDD, SSD, or
whatever) that's located in /EFI/BOOT/BOOTX64.EFI.  So all you need is an EFI
binary (a bastardized COFF/WinPE file, but I won't get into that) that knows
how to talk the EFI protocol to load itself.

...and the Linux kernel has a trivial EFI loader to boot itself no problem...

So how do we boot that and have, you know, stuff that's not just a Linux
kernel?  Enter the embedded initramfs!

#### Initramfses for Fun and Profit ####

An *initramfs* is a CPIO archive that uses clever trickery to abuse the cache
system for use as a RAM-based disk (it's actually super-duper clever and one
of my favorite Linus Torvalds hacks ever, but that's a story for another day).
This CPIO archive (think of it as being functionally similar to a TAR file or
a ZIP file) is usually stuffed in memory by a boot loader and pulled into a
RAMfs.  Usually an initramfs will have a collection of *modules* (things like
hardware drivers) and a little program to load them, locate the "real" root
device and cut over to it once it's mounted.

But that's not what we're doing here, because of another trick.

When you build your own Linux kernel, you can specify a CPIO file that will
get jammed into the resulting kernel image that will be automagically loaded
when the kernel starts.  And the resulting image is technically a complete
root filesystem; you can actually jam a whole Linux userland in there if you
want and run from it the entire time.  This is a trick usually used in
embedded systems (indeed, an entire product line I worked on used that trick
to reduce wear on our SSDs and provide improved stability when drives start
acting wonky with time).

#### Putting It All Together ####

So all you need to make a one-file Linux distribution is:

1. Compile your own kernel.
2. Specify that it have a reasonable list of drivers built-in.
3. Embed a CPIOball with an initramfs in it.

...and *poof* you have a single-file Linux distribution.

## Contents of This Directory ##

1. Containerfile - a container specification that lists the directions used
   to download and build the Linux distro.
2. config-\*.json - Config files that specify various flavors of FairyLinux
3. Various helper scripts.

## JSON Config Files ##

These specify what packages are apk-added during the build (for a more- or
less-complete variant of the system) and 

## The FuTuRe... ##

Since the EFI file is literally just a modified COFF file with a stub at the
front of it, I wonder if a new section could be added to embed userdata into
the kernel, such as IP address settings, startup scripts, passwords, or other
such things.  Then a simple objcopy(1) could be used to make a FairyLinux with
something resembling persistence and still be a single file...

...and what if the Linux EFI loader had support for loading a section as an
initramfs instead of it being embedded within the kernel payload itself?  Then
we could really cook up some mischief!

## License ##

As with the Linux kernel, FairyLinux itself is GPLv2.  The embedded Alpine
runtime is *technically* not runtime-linked with the kernel and so all its
internal bits are licensed on a per-package basis per the Alpine distro
itself.  Notably MUSL libc is MIT-licensed and Busybox is GPLv2, but you can
"apk add" stuff with pretty much every OSS-type license you can think of (and
likely some oddball licenses one would never dream of).

## BJ Black ##

BJ is a longtime Linux user (celebrating 30 years with Linux in November,
2023) and has been a devops and/or generic Linux, Java, Golang, and various
other platform developer for decades.  19 years in Silicon Valley and now
lives on the Oregon coast.
