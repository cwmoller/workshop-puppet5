# Kickstart

From the official documentation

> Kickstart installations offer a means to automate the installation process, either partially or fully. Kickstart files contain answers to all questions normally asked by the installation program, such as what time zone you want the system to use, how the drives should be partitioned, or which packages should be installed. Providing a prepared Kickstart file when the installation begins therefore allows you to perform the installation automatically, without need for any intervention from the user. This is especially useful when deploying Red Hat Enterprise Linux on a large number of systems at once.

Kickstart files are supplied to the installer on boot as a kernel parameter. At the boot prompt, press `TAB` to edit the kernel parameters and add the `inst.ks` parameter with a URL to the config file.

`vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=CentOS\x207\x20x86_64 quiet inst.ks=https://github.com/cwmoller/workshop-puppet5/raw/master/ks.cfg`

The kickstart file we're using contains the following. The first group of settings specify basics like keyboard layout, language and installation source.

```
auth --enableshadow --passalgo=sha512
cdrom
#url --url="http://centos.mirror.ac.za/7/os/x86_64"
cmdline
firewall --enabled --service=ssh
firstboot --disable
keyboard --vckeymap=us --xlayouts="us"
lang en_ZA.UTF-8
network --bootproto=dhcp --ipv6=auto --activate
reboot
rootpw --iscrypted $6$12fe36ec$aTtPc0Hb4r<snip>
selinux --enforcing
timezone Africa/Johannesburg --isUtc
```

Next we have the disk configuration. This example nukes everything on the disks and prepares for both BIOS and UEFI boot. We also set a GRUB2 password.

```
bootloader --location=mbr --iscrypted --password=grub.pbkdf2.sha512.10000.<snip>
zerombr
clearpart --all --initlabel

part biosboot              --fstype="biosboot" --size=1
part /boot/efi --asprimary --fstype="efi"      --size=200
part /boot     --asprimary --fstype="xfs"      --size=500 --label=boot

part pv.01     --grow                          --size=500
volgroup vg0 pv.01

logvol /        --fstype="xfs"  --size=5120 --name=root --vgname=vg0
logvol /opt     --fstype="xfs"  --size=5120 --name=opt  --vgname=vg0
logvol /var     --fstype="xfs"  --size=5120 --name=var  --vgname=vg0
logvol /var/log --fstype="xfs"  --size=5120 --name=log  --vgname=vg0
logvol /tmp     --fstype="xfs"  --size=5120 --name=tmp  --vgname=vg0
logvol swap     --fstype="swap" --size=200  --name=swap --vgname=vg0
```

Next we specify which package groups and individual packages to install. Here we install the `core` group and a handful of other packages. Valid group names are in a file in the `repodata` directory on the install DVD.

```
%packages --nobase
@core
mc
openssh-clients
policycoreutils-python
redhat-lsb-core
screen
vim
%end
```

Next we have normal shell commands that run just after installation in the new system. In this example we set up authorized SSH keys for the `root` user.

```
%post
/bin/mkdir -p /root/.ssh
/bin/cat >/root/.ssh/authorized_keys << EOF
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlz<snip>
EOF
/bin/chmod -R u=rwX,g-rwx,o-rwx /root/.ssh
/sbin/restorecon -R /root/.ssh
%end
```

Completely optional, we disable the kdump feature.

```
%addon com_redhat_kdump --disable

%end
```


[Official documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/chap-kickstart-installations)
