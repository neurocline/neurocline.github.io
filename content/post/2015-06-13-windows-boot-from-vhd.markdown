---
categories: dev
date: 2015-06-13T10:20:00Z
tags: windows
title: Windows boot from VHD
url: /2015/06/13/windows-boot-from-vhd/
---

I'm going to try a workflow that involves running from a system contained in a VHD
file rather than directly installed to a hard disk. This isn't just to try out new
versions like Windows 10, but as a normal practice. Here are a few of the things
driving me to try this.

The main one is the idea of having a clean system at all times. Maintaining a clean system is
hard, because you install stuff in order to get work done, and then in a few months you have a
mess. My hope is that with a VHD-based system, I can set aside milestone VHDs that I can revert
to when I want a clean system again. That's the main workflow I'll be exploring: install clean
OS, install my tools, make snapshot, work for a while, then want to alter the base version, so
revert to it, install new tools, make a snapshot, etc. Hard disks are now fast enough that making
a snapshot is a matter of seconds to minutes, based on how big you keep your operating system
partition.

Another is being able to switch between versions of the operating system. I may prefer a specific
version of Windows as a user, but there are a lot of users out there with different preferences,
and I need to make sure my software works on their Windows version as well. Or I may want to write
software for a bleeding edge version that I don't want to live in day-by-day yet.

And then finally, if I'm semi-frequently wiping my operating system partition, I'll have to learn
to keep my user data off the operating system partition. I already work this way - I don't put my
own stuff into my User folder, but separate folders like "projects".

This is not a set of instructions at the moment, it's a record of actions.

## Grab Windows 10 Technical Preview

Grab it from here - for now, this is going to be final soon.

[Download Windows 10 Insider Preview](http://windows.microsoft.com/en-us/windows/preview-download)

## Create a VHD

### Using Disk Management console

run ```mmc diskmgmt.msc``` from the command-line or launch Computer Management from Administrative tools or directly via Manage Computer. Or use DiskPart.

Action: Create VHD. Dynamically expanding VHDs have a slight performance overhead and aren't appropriate
in certain scenarios (like, for example, booting from it).

This will create an uninitialized disk, so initialize it, either as MBR or GPT. Either is a fine choice
for modern systems. I do GPT.

## MBR versus GPT

Use MBR if you're going to boot from this on a non-UEFI system,
or if it's a small disk, otherwise use GPT. In reality, use MBR for "small" disks (say under 500 GB) and
use GPT for larger ones. GPT is slightly more wasteful since it always includes a Protective MBR so that
older operating systems don't think the drive is uninitialized, and this consumes between 30 to 100 MB,
at least on drives formatted on Windows 7 (bigger if it has an MSR).

* A 2 GB VHD partitioned as GPT and initialized with one volume gives a capacity of 2,111,827,968 bytes.
* A 2 GB VHD partitioned as MBR and initialized with one volume gives a capacity of 2,144,333,824 bytes.

You may or may not consider this substantial. GPT drives should be slightly more resilient than MBR
drives, and as an added bonus, volume labels are 72 bytes (36 UTF-16LE characters) in GPT (MBR
volumes don't actually have names, but FAT-formatted volumes have a 11-byte limit and NTFS-fofmatted
volumes have a 32-character limit). So in reality, it's irrelevant, because we almost always use
NTFS, right?

## Mount a VHD

run `mmc diskmgmt.msc` from the command-line or launch Computer Management from Administrative tools or directly via Manage Computer.

Action: Attach VHD. Select the mosunt point, which is typically going to be a drive letter.

## Unmount a VHD

Detach VHD in Disk Management (right-click on a Disk to see this menu item).

## Using DiskPart

DiskPart can do many disk-related tasks, and more importantly, can be driven with scripts. For
example, create this script

{{< highlight bash >}}
create vdisk file=C:\VHD\Win10.vhd maximum=51200 type=fixed
select vdisk file=C:\VHD\Win10.vhd
attach vdisk
create partition primary
assign letter=v
format quick label=vhd
detach vdisk
exit
{{< / highlight >}}

and run it

{{< highlight bash >}}
> diskpart /s makevhd.txt
{{< / highlight >}}

This will create a 50 GB VHD file, and then create a single primary partition on it.

[Diskpart Scripts and Examples](https://technet.microsoft.com/en-us/library/dn614984.aspx)

## Hyper-V and Windows 8

One reason to upgrade to Windows 8.1 is to get Client Hyper-V

[5 excellent uses of Windows 8 Hyper-V](http://www.infoworld.com/article/2616033/virtualization/5-excellent-uses-of-windows-8-hyper-v.html)

## Burn ISO to USB drive

Well, not really burn. Windows doesn't have native ISO tools, which is really annoying (especially
since ISO is a pretty simple format). Enter Rufus.

[Rufus](http://rufus.akeo.ie/)

[How To Burn an ISO File to a USB Drive](http://pcsupport.about.com/od/file-folder/fl/burn-iso-usb.htm)

## Reference

* [Dual Boot Windows 10 Technical Preview From A VHD](http://www.wintellect.com/devcenter/paulballard/dual-boot-windows-10-technical-preview-from-a-vhd)
* [Windows 10 Technical Preview Using VHD Native Boot](https://newsignature.com/articles/windows-10-technical-preview-using-vhd-native-boot)
* [Guide: How to install Windows 10 using VHDs](http://www.neowin.net/news/guide-how-to-install-windows-10-using-vhds)
* [How to Create Windows 8 VHD for Boot to VHD using simple, easy to follow steps](http://blogs.technet.com/b/haroldwong/archive/2012/08/18/how-to-create-windows-8-vhd-for-boot-to-vhd-using-simple-easy-to-follow-steps.aspx)
* [Windows and GPT FAQ](https://msdn.microsoft.com/en-us/library/windows/hardware/dn640535(v=vs.85).aspx)
* [Creating and Managing MBR Disk Partitions on Windows Server 2008 Basic Disks](http://www.techotopia.com/index.php/Creating_and_Managing_MBR_Disk_Partitions_on_Windows_Server_2008_Basic_Disks)
* [Using Basic Disks and Partitions](https://msdn.microsoft.com/en-us/library/dd163549.aspx)
* [GUID Partition Table](https://en.wikipedia.org/wiki/GUID_Partition_Table)
* [Windows and GPT FAQ](https://msdn.microsoft.com/en-us/library/windows/hardware/dn640535(v=vs.85).aspx)
* [Dual-boot Windows 7 and Windows 8 using a VHD](http://www.techrepublic.com/blog/windows-and-office/dual-boot-windows-7-and-windows-8-using-a-vhd/)
