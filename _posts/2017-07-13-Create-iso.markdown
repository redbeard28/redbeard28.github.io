---
title:  "Howto: Create an ISO CentOS"
date:   2017-07-13 08:00:00
categories: [Linux]
tags: [Linux,redhat,iso,kickstart]
tipue_search_active: true
---
This time, we see howto create an custom iso of CentOS.

Create a directory to mount your source.

{% highlight bash %}
mkdir /mnt/bootiso
{% endhighlight %}

<pre class="prettyprint lang-bash"><span class="desert">
mkdir /mnt/bootiso
# This is a comments
for i in too
 do
   echo $toto
 done
</span></pre>

Loop mount the source ISO you are modifying. (Download from Red Hat / CentOS.)
```bash
mount -o loop /path/to/some.iso /mnt/bootiso
```

Create a working directory for your customized media.
```bash
mkdir /mnt/bootisoks
```

Copy the source media to the working directory.
```bash
rsync -av --progress /mnt/bootiso/* /mnt/bootisoks/
```

Unmount the source ISO and remove the directory.
```bash
umount /mnt/bootiso && rmdir /mnt/bootiso
```

Change permissions on the working directory.
```bash
chmod -R u+w /mnt/bootisoks
```

Copy your Kickstart script which has been modified for the packages and %post to the working directory.
```bash
cp /path/to/someks.cfg /mnt/bootisoks/isolinux/ks.cfg
```

Copy any additional RPMs to the directory structure and update the metadata.
```bash
cp /path/to/*.rpm /mnt/bootisoks/Packages/
cd /mnt/bootisoks/Packages && createrepo -dpo .. .
```

Add kickstart to boot options.
```bash
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
```

Create the new ISO file.
```bash
cd /mnt/bootisoks && mkisofs -o /mnt/boot.iso -b isolinux.bin -c boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T isolinux/. .
```