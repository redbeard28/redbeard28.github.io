---
title:  "Howto: Create an ISO CentOS"
date:   2017-07-13 08:00:00
categories: [Linux]
tags: [Linux,redhat,iso,kickstart]
tipue_search_active: true
---
This time, we see howto create an custom iso of CentOS.

Create a directory to mount your source.
<pre class="prettyprint">
mkdir /mnt/bootiso
</pre>


Loop mount the source ISO you are modifying. (Download from Red Hat / CentOS.)
<pre class="prettyprint">
mount -o loop /path/to/some.iso /mnt/bootiso
</pre>


Create a working directory for your customized media.
<pre class="prettyprint">
mkdir /mnt/bootisoks
</pre>


Copy the source media to the working directory.
<pre class="prettyprint">
rsync -av --progress /mnt/bootiso/* /mnt/bootisoks/
</pre>


Unmount the source ISO and remove the directory.
<pre class="prettyprint">
umount /mnt/bootiso && rmdir /mnt/bootiso
</pre>


Change permissions on the working directory.
<pre class="prettyprint">
chmod -R u+w /mnt/bootisoks
</pre>


Copy your Kickstart script which has been modified for the packages and %post to the working directory.
<pre class="prettyprint">
cp /path/to/someks.cfg /mnt/bootisoks/isolinux/ks.cfg
</pre>


Copy any additional RPMs to the directory structure and update the metadata.
<pre class="prettyprint">
cp /path/to/*.rpm /mnt/bootisoks/Packages/
cd /mnt/bootisoks/Packages && createrepo -dpo .. .
</pre>


Add kickstart to boot options.
<pre class="prettyprint">
echo "default vesamenu.c32
#prompt 1
timeout 600

display boot.msg

menu background splash.jpg
menu title Welcome to Red Hat Enterprise Linux 6.8!
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

label Kickstart
  menu label ^Install RHEL6.8
  menu default
  kernel vmlinuz
  append initrd=initrd.img ks=cdrom:/ks.cfg bond=bond0:eth0,eth1:mode=802.3ad,miimon=100,lacp_rate=fast vlan=bond0.474:bond0
label linux
  menu label ^Install or upgrade an existing system
  menu default
  kernel vmlinuz
  append initrd=initrd.img
label vesa
  menu label Install system with ^basic video driver
  kernel vmlinuz
  append initrd=initrd.img nomodeset
label rescue
  menu label ^Rescue installed system
  kernel vmlinuz
  append initrd=initrd.img rescue
label local
  menu label Boot from ^local drive
  localboot 0xffff
label memtest86
  menu label ^Memory test
  kernel memtest
  append -

" > /mnt/bootisoks/isolinux/isolinux.cfg
</pre>


Create the new ISO file.
<pre class="prettyprint">
cd /mnt/bootisoks && mkisofs -o /mnt/boot.iso -b isolinux.bin -c boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T isolinux/. .
</pre>


Exemple of a production kickstart.
With this, you can install a server with multiple NIC cards, bonding eth0,eth1
<pre class="prettyprint">
# Kickstart for RHEL with repo on a Apache Server

# For genarating random password, you can use one of thoses:
#  http://passwordsgenerator.net/
#
#  Transfor in sha512 your password
#  http://passwordsgenerator.net/sha512-hash-generator/

#version=DEVEL
install

# text mode (no graphical mode)
text

# do not configure X
skipx


# installation path
url --url=http://192.168.100.1/RHEL6.8

lang fr_FR.UTF-8
keyboard fr-latin1
#network --onboot yes --device eth0 --bootproto dhcp --noipv6
network --onboot no --device eth1 --bootproto dhcp --noipv6
network --onboot no --device eth2 --bootproto dhcp --noipv6
network --onboot no --device eth3 --bootproto dhcp --noipv6
network --onboot no --device eth4 --bootproto dhcp --noipv6
network --onboot no --device eth5 --bootproto dhcp --noipv6
network --onboot no --device eth6 --bootproto dhcp --noipv6
network --onboot no --device eth7 --bootproto dhcp --noipv6

network --hostname myserver --device eth0 --vlanid YYY --bootproto static --ip=192.168.100.5 --gateway=192.168.100.254 --netmask=255.255.255.0

rootpw  --iscrypted $6$rLMRpbW2y0MPSS7z$MxzCvi9pcV/kqXGXONa8aN.2kidE8YEIS8.vyobfpX5LgbIFtaWJvEcrcXejC749Fyxv1J0XJ14e2ltc0zCAI.
# firewall
firewall --disabled
authconfig --enableshadow --passalgo=sha512
# SElinux
selinux --disabled
timezone --utc Europe/Paris
bootloader --location=partition --driveorder=sda --append="crashkernel=auto rhgb quiet"
# The following is the partition information you requested
# Note that any partitions you deleted are not expressed
# here so unless you clear all partitions first, this is
# not guaranteed to work
#clearpart --none

# delete everything on /dev/sda
clearpart --all --drives=sda,sdb
#ignoredisk --only-use=sda

# Part on disk /dev/sda
part /boot --fstype=ext4 --ondisk=sda --fsoptions="noatime" --size=200
part /boot/efi --fstype=efi --size=200
part pv.01 --size 1 --ondisk=sda --grow
volgroup ROOTVG pv.01
logvol / --fstype ext4 --fsoptions="noatime" --name=rootlv --vgname=ROOTVG --size 4096 --grow

# Part on disk /dev/sdb
part pv.02 --size 1 --ondisk=sdb --grow
volgroup SRVVG pv.02
logvol /app01 --fstype ext4 --fsoption="noatime" --name=app01lv --vgname=SRVXVG --size 102400
logvol /data01 --fstype ext4 --fsoption="noatime" --name=data01lv --vgname=SRVVG --size 102400

%packages
@base
@client-mgmt-tools
@console-internet
@core
@debugging
@directory-client
@hardware-monitoring
@java-platform
@large-systems
@network-file-system-client
@performance
@perl-runtime
@server-platform
@server-policy
pax
python-dmidecode
oddjob
sgpio
device-mapper-persistent-data
samba-winbind
certmonger
pam_krb5
krb5-workstation
perl-DBD-SQLite
%end

%post --nochroot
# bonding for VLANXXXX
cat << EOF > /mnt/sysimage/etc/sysconfig/network-scripts/ifcfg-bond0
DEVICE=bond0
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=none
NETMASK=255.255.255.0
BONDING_OPTS="mode=4 miimon=100 lacp_rate=1"
#MTU=9000
IPADDR=192.168.100.5
EOF

# bonding for VLANYYY
cat << EOF > /mnt/sysimage/etc/sysconfig/network-scripts/ifcfg-bond0.473
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=none
NETMASK=255.255.255.0
VLAN=yes
DEVICE=bond0.473
IPADDR=192.168.200.5
EOF

# ETHx Configuration
cat << EOF > /mnt/sysimage/etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
EOF

cat << EOF > /mnt/sysimage/etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
EOF

for i in {2..7}
do
 cat << EOF > /mnt/sysimage/etc/sysconfig/network-scripts/ifcfg-eth$i
DEVICE=eth$i
TYPE=Ethernet
ONBOOT=no
NM_CONTROLLED=yes
BOOTPROTO=dhcp
EOF
done

cat << EOF >> /mnt/sysimage/etc/sysconfig/network
GATEWAY=192.168.100.254
EOF

mkdir -m0700 /mnt/sysimage/root/.ssh/authorized_keys

# Put your public keys
cat << EOF > /mnt/sysimage/root/.ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA[..]/e4vlfbwYJVL/ToQDXhua9t
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQ[..]MoWokvF3VMklu0ZwvYd/R1w==
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQ[..]aO48lFGl6pRNRHyHuR0Q==
EOF

chmod 400 /mnt/sysimage/root/.ssh/authorized_keys

</pre>