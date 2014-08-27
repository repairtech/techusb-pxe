TechUSB – PXE Setup Guide
===========

This tutorial will guide you through process of setting up a PXE Server that allows you to boot <a href="https://repairtechsolutions.com">TechUSB</a> over a local network. For the purposes of this tutorial we assume your network schema to be identical to the following diagram:

<img src="http://i.imgur.com/eddEPIs.png" />

If you want to further customize your PXE installation to better fit your environment, we strongly suggest following this tutorial first, and then making your modifications in a test environment (for example in VirtualBox).

<h3>Requirements:</h3>

<ol>
<li>TechUSB installation files found here ( <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/techusb.iso">techusb.iso</a>, <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/minirtpxe.gz">minirtpxe.gz</a>, <a href="https://8a460776177d49c765ce-a2065d3226b6f083a3fe1d53a8aa037e.ssl.cf1.rackcdn.com/techusb.pxe">techusb.pxe</a>)
</li>
<li>A linux server able to serve booting to a generic PXE environment with a simple syslinux.cfg file.<br />
We used debian 7.5 64-bit.
<br/><br/>
For the purposes of this tutorial this server will have ip address 10.50.0.115 and has already been configured for internet access.
</li>
<li>Working DHCP Server on the network
</li>
</ol>
<h3>TechUSB PXE Server Configuration:</h3>
<ol>
<li>Copy these files to the PXE Server:<br/>
- techusb.iso<br/>
- minirtpxe.gz<br/>
- techusb.pxe<br/>
</li>
<li>
Execute the preparation script, pass as parameters: path to iso file, path to minirtpxe.gz file, nfs ip address for our clients, path to the pxe server folder, and path to tmp folder you want to use for mounting:<br/>
root@techusb:~#   chmod +x techusb.pxe<br/>
root@techusb:~#   ./techusb.pxe techusb.iso minirtpxe.gz 10.50.0.115 /srv/tftp /tmp/techusb<br/>
</li>
<li>DHCP configuration<br/>
You probably already have a DHCP server in your network and if so you really don’t want to set up another one. Below are examples for how to configure two of the most popular DHCP servers for PXE boot with TechUSB:<br/>

<br/><b>Windows Server</b><br/>

a) Go to Start -> Administrative Tools -> DHCP<br/>
b) Expand applicable scope<br/>
c) Right click Scope Options -> Configure Options<br/>
d) Add option 066 -> 10.50.0.115<br/>
e) Add option 067 -> pxelinux.0<br/>

<br/><b>Linux DHCP Server</b><br/>
a)	Edit your dhcpd.conf<br/>
b)	Depending on your configuration add to the global section of your subnet or to single host two entries:<br/>
next-server 10.50.0.115;<br/>
filename “pxelinux.0”<br/>

If you want to limit hosts which would be able to boot via PXE you could configure TechUSB for single host by mac-address:
<br/><br/>host TechUSB-Client {<br/>
&nbsp;&nbsp;hardware ethernet 00:1C:C4:C6:A3:01;<br/>
&nbsp;&nbsp;fixed-address 10.50.0.116;<br/>
&nbsp;&nbsp;next-server 10.50.0.115;<br/>
&nbsp;&nbsp;filename "pxelinux.0";<br/>
}<br/><br/>
c)	Restart your dhcp server
</li>
<li>Configure your client PC or laptop to boot via PXE. You can do so by changing the boot priority in the BIOS, or depending on your hardware you also could choose one-time boot process selection by pressing the special function key just after powering on ( most common are F9 or F12 ) and choosing the network card. After that you should get an IP Address from your DHCP server and a few seconds later you should get a nice TechUSB boot menu.
</li></ol>

<h3>Other helpful files:</h3>
We've included the resulting knoppix.menu file and minirtpxe.gz in the git repo so that you can see what our syslinux entries look like for booting. This should help if you already have an existing PXE system and want to modify it, instead of using the techusb.pxe script.

If you have any questions feel free to shoot us an email at <a href="mailto:support@repairtechsolutions.com">support@repairtechsolutions.com</a>.
