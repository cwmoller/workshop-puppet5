### Kickstart

From the official documentation

> Kickstart installations offer a means to automate the installation process, either partially or fully. Kickstart files contain answers to all questions normally asked by the installation program, such as what time zone you want the system to use, how the drives should be partitioned, or which packages should be installed. Providing a prepared Kickstart file when the installation begins therefore allows you to perform the installation automatically, without need for any intervention from the user. This is especially useful when deploying Red Hat Enterprise Linux on a large number of systems at once.

Kickstart files are supplied to the installer on boot as a kernel parameter. At the boot prompt, press `TAB` to edit the kernel parameters and add the `inst.ks` parameter with a URL to the config file.

`vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=CentOS\x207\x20x86_64 quiet inst.ks=https://github.com/cwmoller/workshop-puppet5/raw/master/ks.cfg`

The kickstart file we're using contains the following.

```
auth --enableshadow --passalgo=sha512
cdrom
#url --url="http://centos.mirror.ac.za/7/os/x86_64"
cmdline
firewall --enabled --service=ssh
firstboot --disable
keyboard --vckeymap=us --xlayouts='us'
lang en_ZA.UTF-8
network --bootproto=dhcp --ipv6=auto --activate
reboot

rootpw --iscrypted $6$12fe36ec$aTtPc0Hb4rjmITbCiOAwERoSLCSs9LaYZEqvO3tmWioHCS3R1fUEhh6Vr0JhzO8MyS6/jF/FDRBgA9qc4QH/v/
selinux --enforcing
timezone Africa/Johannesburg --isUtc

bootloader --location=mbr --iscrypted --password=grub.pbkdf2.sha512.10000.3CD58888167128B41EBD5A2DF6B3EF49B708BCCCE28614889B93F146D4F1AD4AA7EC35376023AD8A654AD8A04F15D10CEDF8D765038F2721DF6C78E6F66EEADC.00E5876D6E704EC0D4A9502C76789523084281D38C3AF059B0F270DE9493017B6EDD2E145A1A22387B4CFC8889FD997F2909B1D92D454DD7C748D8AE1C629330
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

%post

/bin/mkdir -p /root/.ssh
/bin/cat >/root/.ssh/authorized_keys << EOF
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAGha6yLH47pcULE0GIeTLvUOcSfBPwQjTQYlRxOTViryhhYqI8FSd0iGVFmUeryq/xQ/kaBb0Hr74rXP9oVWqg=
EOF
/bin/chmod -R u=rwX,g-rwx,o-rwx /root/.ssh
/sbin/restorecon -R /root/.ssh

%end

%packages --nobase
@core
mc
openssh-clients
policycoreutils-python
redhat-lsb-core
screen
vim

%end

%addon com_redhat_kdump --disable

%end
```


[Official documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/chap-kickstart-installations)
