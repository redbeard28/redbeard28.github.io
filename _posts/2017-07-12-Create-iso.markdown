---
title:  "Howto: Create an ISO CentOS"
date:   2017-07-12 16:00:23
categories: [Linux]
tags: [Linux,redhat,iso,kickstart]
tipue_search_active: true
---
This time, we see howto create an custom iso of CentOS.

Create a directory to mount your source.

{% highlight bash %}
mkdir /tmp/bootiso.
{% endhighlight %}


Loop mount the source ISO you are modifying. (Download from Red Hat / CentOS.)
```bash
mount -o loop /path/to/some.iso /tmp/bootiso
```

Create a working directory for your customized media.
```bash
mkdir /tmp/bootisoks
```

Copy the source media to the working directory.
```bash
cp -r /tmp/bootiso/* /tmp/bootisoks/
```

Unmount the source ISO and remove the directory.
```bash
umount /tmp/bootiso && rmdir /tmp/bootiso
```

Change permissions on the working directory.
```bash
chmod -R u+w /tmp/bootisoks
```

Copy your Kickstart script which has been modified for the packages and %post to the working directory.
```bash
cp /path/to/someks.cfg /tmp/bootisoks/isolinux/ks.cfg
```

Copy any additional RPMs to the directory structure and update the metadata.
```bash
cp /path/to/*.rpm /tmp/bootisoks/Packages/
cd /tmp/bootisoks/Packages && createrepo -dpo .. .
```

Add kickstart to boot options.
```bash
sed -i 's/append\ initrd\=initrd.img$/append initrd=initrd.img\ ks\=cdrom:\/ks.cfg/' /tmp/bootisoks/isolinux/isolinux.cfg
```

Create the new ISO file.
```bash
cd /tmp/bootisoks && mkisofs -o /tmp/boot.iso -b isolinux.bin -c boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T isolinux/. .
```