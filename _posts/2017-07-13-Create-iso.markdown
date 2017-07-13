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

<pre class="prettyprint lang-bash">
mkdir /mnt/bootiso
# This is a comments
for i in too
 do
   echo $toto
 done
</pre>

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
sed -i 's/append\ initrd\=initrd.img$/append initrd=initrd.img\ ks\=cdrom:\/ks.cfg/' /mnt/bootisoks/isolinux/isolinux.cfg
```

Create the new ISO file.
```bash
cd /mnt/bootisoks && mkisofs -o /mnt/boot.iso -b isolinux.bin -c boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T isolinux/. .
```