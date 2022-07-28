---
layout: post
title:  "Format an sd card to fat32 in linux using fdisk"
excerpt: "The title says it all."
date:   2018-05-22 00:15:20 -0500
author: "Jake Gallow"
---

## The Problem ##
&nbsp;&nbsp;&nbsp; So the quick Googler did not give me an immediate answer for when I searched for "format an sd card to fat32 linux" from a site that wasn't associated with some massive techblog.
 So here's a real quick guide on formatting a (micro)sd card to fat32.
 I'm on Ubuntu Linux and I'm using fdsik.
 Note that you may need to root to do this as fdisk can be destructive if you are stupid with it.


1. Find the device

	```
	fdisk -l

	Disk /dev/mmcblk0: 14.9 GiB, 15931539456 bytes, 31116288 sectors
	Units: sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disklabel type: dos
	Disk identifier: 0x00000000

	Device         Boot Start      End  Sectors  Size Id Type
	/dev/mmcblk0p1       8192 31116287 31108096 14.9G  c W95 FAT32 (LBA)
	```

	As you can see my devices name is /dev/mmcblk0p1.
	 It has 1 partition of type W95 FAT32.
	 Don't screw up and grab one of you file system partitions.
	 (HINT: if you do fdisk -l before and after you insert the sd card it should be pretty freaking clear which one you are looking for.
	 You can also tell from the size, most likely.)


2. Delete existing partitions on the card.

	```
	fdisk /dev/mmcblk0

	Command (m for help): d
	Selected partition 1
	Partition 1 has been deleted.
	```

	I only had 1 partition so it just deleted that partition.
	If you have multiple partitions on the card then you can just select the partitions to format.

3. Check out your partitions (for your own edification).

	```
	Command (m for help): p
	Disk /dev/mmcblk0: 14.9 GiB, 15931539456 bytes, 31116288 sectors
	Units: sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disklabel type: dos
	Disk identifier: 0x00000000
	```

4. Create a new partition for the card

	```
	Command (m for help): p
	Disk /dev/mmcblk0: 14.9 GiB, 15931539456 bytes, 31116288 sectors
	Units: sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disklabel type: dos
	Disk identifier: 0x00000000

	Command (m for help): n
	Partition type
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended (container for logical partitions)
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-31116287, default 2048):
	Last sector, +sectors or +size{K,M,G,T,P} (2048-31116287, default 31116287):

	Created a new partition 1 of type 'Linux' and of size 14.9 GiB.

	Command (m for help): p
	Disk /dev/mmcblk0: 14.9 GiB, 15931539456 bytes, 31116288 sectors
	Units: sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disklabel type: dos
	Disk identifier: 0x00000000

	Device         Boot Start      End  Sectors  Size Id Type
	/dev/mmcblk0p1       2048 31116287 31114240 14.9G 83 Linux
	```

	I just left the defaults so it uses my full card.

5. Make the type Fat32.

	```
	Command (m for help): t
	Selected partition 1
	Partition type (type L to list all types): L

	 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
	 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
	 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
	 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
	 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
	 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
	 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
	 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
	 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
	 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
	 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
	 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
	 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
	 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs
	 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT
	10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
	11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
	12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor
	14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor
	16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary
	17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS
	18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE
	1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
	1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep
	1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT
	Partition type (type L to list all types): b
	Changed type of partition 'FAT12' to 'W95 FAT32'.
	```

	(I had accidentally changed it to FAT12 first before I hit the right button.

6. Write the changes.

	```
	Command (m for help): w
	The partition table has been altered.
	Calling ioctl() to re-read partition table.
	Syncing disks.
	```

7. Now that fdisk has written the new parttion you need to format the drive suchly.

	```
	mkfs.vfat /dev/mmcblk0p1                                                                              [22:59:38]
	mkfs.fat 3.0.28 (2015-05-16)
	```


&nbsp;&nbsp;&nbsp; The deed is done.
 fdisk is a super useful command, use it to format stuff and write partion tables.
 The cli is super useful.

This is basically the same guide Norman Dunbar wrote up for formatting, but his is in reference for NOOBS.
 I'm mostly writing it here so I can reference it and not have to find a the NOOBS bootloader how to when this is what I'm looking for.
 Internet indexing is an awesome thing.

Please give Mr. Dunbar some views if you read this blog.
The credit goes to him.

[NOOBS FOR Raspberry Pi (includes formatting stuff)](http://qdosmsq.dunbar-it.co.uk/blog/2013/06/noobs-for-raspberry-pi/))
