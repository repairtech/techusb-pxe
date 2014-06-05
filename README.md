TechUSB – PXE Setup Guide
===========

This tutorial will guide you through process of setting up a PXE Server that allows you to boot TechUSB over a local network. For the purposes of this tutorial we assume your network schema to be identical to the following diagram:

<img src="http://i.imgur.com/eddEPIs.png" />

If you want to further customize your PXE installation to better fit your environment, we strongly suggest following this tutorial first, and then making your modifications in a test environment (for example in VirtualBox).

Requirements:

1)	TechUSB installation files found here ( <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/techusb.iso">techusb.iso</a>, <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/minirtpxe.gz">minirtpxe.gz</a>, <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/techusb.pxe">techusb.pxe</a>)

2)	Fresh installation of Debian 7.5
You can download it here:
http://cdimage.debian.org/debian-cd/7.5.0/amd64/iso-cd/debian-7.5.0-amd64-netinst.iso

For the purposes of this tutorial this server will have ip address 10.50.0.115 and has already been configured for internet access.

3)	Working DHCP Server on the network

TechUSB PXE Server configuration:

1)	Copy these files to the PXE Server:
- techusb.iso
- minirtpxe.gz
- techusb.pxe

2)	Execute preparation script, pass as parameters: path to iso file, path to minirtpxe.gz file and nfs ip address for our clients:
root@techusb:~#   chmod +x techusb.pxe
root@techusb:~#   ./techusb.pxe techusb.iso minirtpxe.gz 10.50.0.115

3)	DHCP configuration
You probably already have a DHCP server in your network and if so you really don’t want to set up another one. Below are examples for how to configure two of the most popular DHCP servers for PXE boot with TechUSB:

Windows Server

a) Go to Start -> Administrative Tools -> DHCP
b) Expand applicable scope
c) Right click Scope Options -> Configure Options
d) Add option 066 -> 10.50.0.115
e) Add option 067 -> pxelinux.0

Linux DHCP Server
a)	Edit your dhcpd.conf
b)	Depending on your configuration add to the global section of your subnet or to single host two entries:
next-server 10.50.0.115;
filename “pxelinux.0”

If you want to limit hosts which would be able to boot via PXE you could configure TechUSB for single host by mac-address:
host TechUSB-Client {
hardware ethernet 00:1C:C4:C6:A3:01;
fixed-address 10.50.0.116;
next-server 10.50.0.115;
filename "pxelinux.0";
}
c)	Restart your dhcp server

4)	Configure your client PC or laptop to boot via PXE. You can do so by changing the boot priority in the BIOS, or depending on your hardware you also could choose one-time boot process selection by pressing the special function key just after powering on ( most common are F9 or F12 ) and choosing the network card. After that you should get an IP Address from your DHCP server and a few seconds later you should get a nice TechUSB boot menu.
