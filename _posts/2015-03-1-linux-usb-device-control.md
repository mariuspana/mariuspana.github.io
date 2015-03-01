---
layout: post
title: "Linux USB Device control"
excerpt: "This sure as hell beats Windows GPO"
tags: [linux, security]
image:
  feature:
  credit:
  creditlink:
modified:
---

Controlling devices in a corporate/business environment is something that you do quite often with Windows operating environments. You may disable and enable different USB devices, peripherals and so on based on a security or IT policy. So how do you go about integrating a Linux desktop into your network while still respecting the same security/IT policy?

It's actually fairly easy. Since the introduction of [udev](http://en.wikipedia.org/wiki/Udev) in 2003 it has pretty much changed the way devices are created and managed in Linux distributions.

udev replaces devfsd and hotplugd. udev creates the devices during boot/hot-plug/add/etc. but also manages them in userspace. This last userspace bit is interesting in that it manages all events related to devices including loading/unloading necessary firmware.

udev differs from traditional Unix systems by creating device nodes in /dev/ dynamically as opposed to a bunch of static files.

udev is divided into 3 components:

* libudev - provides device information and was recently integrated into [systemd](http://en.wikipedia.org/wiki/Systemd)
* udevd - is the userspace daemon that handles the virtual /dev
* udevadm - a command line utility for diagnostics

udev runs as a daemon and listens for events (uevents) sent by the kernel as devices are added or removed from the system. udev provides a rules system that can be used to configure actions to take when certain events take place, for example what to do when a new USB flash disk is inserted. The language is fairly simple to understand however the power it provides is exceptional. You can do anything you wish with a device once udev knows about it.

Rules are stored in /etc/udev/rules.d and depending on your distribution in /usr/lib/udev/rules.d/. Rules in /etc have precedence over the system defaults provided in /usr/lib in our example.

udev rules are parsed in lexical order therefore the order is usually important.

There are two documents I consultfor anything udev related:

[Writing udev rules](http://www.reactivated.net/writing_udev_rules.html) by Daniel Drake
[udev man page](https://www.kernel.org/pub/linux/utils/kernel/hotplug/udev/udev.html)

These two documents will provide everything you need to create rules for your particular needs.

Lets take a quick look at an example. Say you need to disable all USB access to PC with the exception of a certain USB modem or other storage device.

As with building firewalls it is easier to blacklist everything and then allow or whitelist approved devices than to whitelist and blacklist individual devices. So lets first create a rule that disables all of our USB devices. Remember that the order is important so we use 99-blacklist.rules to load at the very end.

Create a rule in /etc/udev/rules.d named 99-blacklist.rules and edit as follows:

    SUBSYSTEM=="usb", ATTR{authorized_default}="0"
    SUBSYSTEM=="usb", ATTR{authorized}="0"

The above tells udev that all USB devices are disabled by default (authorized_default) and  any device that is added will implicitly have its authorized flag set to '0' rendering it useless.

You can read more about [usb authorization here](https://www.kernel.org/doc/Documentation/usb/authorization.txt) and take note of the PKI recommendations for a truly secure solutions.

So now we have effectively killed all USB devices and we need to allow our select devices. USB devices are attached to a USB HUB inside of your computer therefore the USB HUB must be allowed as well. Lets now create our whitelist and well add the USB HUB and our devices.

Create a rule in /etc/udev/rules.d named 99-whitelist.rules and edit as follows:

    ACTION=="add", ATTR{bDeviceClass}=="09", RUN+="/bin/sh -c 'echo 1 >/sys$DEVPATH/authorized'"

Note the name 99-whitelist.rules will be parsed *after* the 99-blacklist.rules.

This first line enables our USB HUB since in our blacklist rules we disabled all USB devices. I have used the bDeviceClass attribute because I believe this is pretty standard however I recommend you make sure this is so for your particular platform. Ill show you how to identify this attribute shortly.

Let's add our USB stick by adding the following to our whitelist:

    ACTION=="add", ATTR{idVendor}=="19d2", ATTR{idProduct}=="1040", RUN+="/bin/sh -c 'echo 1 >/sys$DEVPATH/authorized'"

What we're doing here is running a simple echo command that authorizes our device. We could have used the ATTR parameter here (ATTR{authorized}="0") but I wanted to show you the power of udev rules. Since we can run any command or script we can effectively do a anything we wanted at this point: for example we could mount the device, verify it with an external service, etc.

Thats pretty much it for whitelisting/blacklisting devices. We used USB as our example but you can do this for any and all devices supported by the Linux kernel.

Now how about getting those attributes and properties  so that you can create your own rules? Thats also easy as well. You use the udevadm tool to identify the attributes and properties you are interested in as follows:

    udevadm info --query=all --name=/dev/sda

This will print all information of the /dev/sda device.

    P: /devices/pci0000:00/0000:00:0d.0/host0/target0:0:0/0:0:0:0/block/sda
    N: sda
    W: 37
    S: block/8:0
    S: disk/by-id/ata-VBOX_HARDDISK_VB579a85b0-bf6debae
    S: disk/by-id/scsi-SATA_VBOX_HARDDISK_VB579a85b0-bf6debae
    S: disk/by-path/pci-0000:00:0d.0-scsi-0:0:0:0
    E: UDEV_LOG=3
    E: DEVPATH=/devices/pci0000:00/0000:00:0d.0/host0/target0:0:0/0:0:0:0/block/sda
    E: MAJOR=8
    E: MINOR=0
    E: DEVNAME=/dev/sda
    E: DEVTYPE=disk
    E: SUBSYSTEM=block
    E: ID_ATA=1
    E: ID_TYPE=disk
    E: ID_BUS=ata
    E: ID_MODEL=VBOX_HARDDISK
    E: ID_MODEL_ENC=VBOX\x20HARDDISK\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20...
    E: ID_SERIAL=VBOX_HARDDISK_VB579a85b0-bf6debae
    E: ID_SERIAL_SHORT=VB579a85b0-bf6debae
    E: ID_ATA_WRITE_CACHE=1
    E: ID_ATA_WRITE_CACHE_ENABLED=1
    E: ID_ATA_FEATURE_SET_PM=1
    E: ID_ATA_FEATURE_SET_PM_ENABLED=1
    E: ID_ATA_SATA=1
    E: ID_ATA_SATA_SIGNAL_RATE_GEN2=1
    E: ID_SCSI_COMPAT=SATA_VBOX_HARDDISK_VB579a85b0-bf6debae
    E: ID_PATH=pci-0000:00:0d.0-scsi-0:0:0:0
    E: ID_PART_TABLE_TYPE=dos
    E: LVM_SBIN_PATH=/sbin
    E: UDISKS_PRESENTATION_NOPOLICY=0
    E: UDISKS_PARTITION_TABLE=1
    E: UDISKS_PARTITION_TABLE_SCHEME=mbr
    E: UDISKS_PARTITION_TABLE_COUNT=2
    E: UDISKS_ATA_SMART_IS_AVAILABLE=0
    E: DEVLINKS=/dev/block/8:0 /dev/disk/by-id/ata-VBOX_HARDDISK_VB579a85b0-bf6debae ...

To display all properties of the /dev/sda device including parent information:

    udevadm info --attribute-walk --name=/dev/sda

        ...
      looking at device '/devices/pci0000:00/0000:00:0d.0/host0/target0:0:0/0:0:0:0/block/sda':
        KERNEL=="sda"
        SUBSYSTEM=="block"
        DRIVER==""
        ATTR{range}=="16"
        ATTR{ext_range}=="256"
        ATTR{removable}=="0"
        ATTR{ro}=="0"
        ATTR{size}=="83886080"
        ATTR{alignment_offset}=="0"
        ATTR{capability}=="52"
        ATTR{stat}=="   20884    15437  1254282   338919     5743     8644   103994   109005 ...
        ATTR{inflight}=="       0        0"

      looking at parent device '/devices/pci0000:00/0000:00:0d.0/host0/target0:0:0/0:0:0:0':
        KERNELS=="0:0:0:0"
        SUBSYSTEMS=="scsi"
        DRIVERS=="sd"
        ATTRS{device_blocked}=="0"
        ATTRS{type}=="0"
        ATTRS{scsi_level}=="6"
        ATTRS{vendor}=="ATA     "
        ATTRS{model}=="VBOX HARDDISK   "
        ATTRS{rev}=="1.0 "
        ATTRS{state}=="running"
        ATTRS{timeout}=="30"
        ATTRS{iocounterbits}=="32"
        ATTRS{iorequest_cnt}=="0x6830"
        ATTRS{iodone_cnt}=="0x6826"
        ATTRS{ioerr_cnt}=="0x3"
        ATTRS{modalias}=="scsi:t-0x00"
        ATTRS{evt_media_change}=="0"
        ATTRS{dh_state}=="detached"
        ATTRS{queue_depth}=="31"
        ATTRS{queue_ramp_up_period}=="120000"
        ATTRS{queue_type}=="simple"

      looking at parent device '/devices/pci0000:00/0000:00:0d.0/host0/target0:0:0':
        KERNELS=="target0:0:0"
        SUBSYSTEMS=="scsi"
        DRIVERS==""

      looking at parent device '/devices/pci0000:00/0000:00:0d.0/host0':
        KERNELS=="host0"
        SUBSYSTEMS=="scsi"
        DRIVERS==""

      looking at parent device '/devices/pci0000:00/0000:00:0d.0':
        KERNELS=="0000:00:0d.0"
        SUBSYSTEMS=="pci"
        DRIVERS=="ahci"
        ATTRS{vendor}=="0x8086"
        ATTRS{device}=="0x2829"
        ATTRS{subsystem_vendor}=="0x0000"
        ATTRS{subsystem_device}=="0x0000"
        ATTRS{class}=="0x010601"
        ATTRS{irq}=="21"
        ATTRS{local_cpus}=="00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000003"
        ATTRS{local_cpulist}=="0-1"
        ATTRS{modalias}=="pci:v00008086d00002829sv00000000sd00000000bc01sc06i01"
        ATTRS{numa_node}=="-1"
        ATTRS{enable}=="1"
        ATTRS{broken_parity_status}=="0"
        ATTRS{msi_bus}==""
        ATTRS{msi_irqs}==""

      looking at parent device '/devices/pci0000:00':
        KERNELS=="pci0000:00"
        SUBSYSTEMS==""
        DRIVERS==""

You can use udevadm to identify, query and monitor all uevent activities. It is the tool you will use most when creating udev rules.

While there is no centralized tool like GPO for windows (at least that I know of) it should be farily easy to get this spread across all of your linux machines if you needed a centralized way to manage all of these rules.

So this is how we do it in Linux. It sure as hell beats GPO, doesn't it? I sure think so.
