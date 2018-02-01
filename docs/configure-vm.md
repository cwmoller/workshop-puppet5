# Configure the VM

[Previous](install-vm.md) \| [Home](index.md) \| [Next](install-puppet-server.md)

### Requirements

1. Security first!!
1. Static hostname
1. Active network connection
1. Updated repositories

### Let's go!!

1. Security
   1. Create a normal user for yourself, and grant that user `sudo` access.  
      `adduser -m -g users -G wheel -c "System Administrator" sysadm`  
      `passwd sysadm`
      The password was set to 'p@ssw0rd' in the provided VMDK/VHDX files.
   1. Delete the password for the `root` user from `/etc/shadow`  
      `passwd -d root`
   1. Set SSH to either disallow root logins (`PermitRootLogin no`), or only allow root logins with a SSH key (`PermitRootLogin without-password`)  
      `sed -ri 's/^#?(PermitRootLogin).*/\1 without-password/g' /etc/ssh/sshd_config`  
      `sed -ri 's/^#?(PermitRootLogin).*/\1 no/g' /etc/ssh/sshd_config`  
      `systemctl restart sshd`
   1. Log in with your new user.
1. Static hostname  
   Puppet generates SSL certificates based on the machine's hostname. If the hostname changes due to the network being down or misconfigured, we don't want Puppet to get confused. The hostname doesn't need to conform to puppet.domain, but it helps a bit.  
   `sudo hostnamectl set-hostname workshop.vm`
1. Active network connection  
   The first network interface should have been automatically connected and set up to use DHCP. If not, make sure you have network connectivity and can access the internet.  
   Let's assume we're connected to a network with available addresses in the 172.30.30.0/24 block and route via 172.30.30.1. We'll use Google's DNS for starters.
   1. Edit `/etc/sysconfig/network-scripts/ifcfg-eth0` and add the following:  
      ```
      BOOTPROTO=none
      IPADDR=172.30.30.2
      GATEWAY=172.30.30.1
      PREFIX=24
      DNS1=8.8.8.8
      ```
   2. Restart networking  
      `systemctl restart network`
   3. Test with a ping to www.google.com   
      `ping www.google.com`
1. Updated repositories
   1. Install the repositories for Puppetlabs, EPEL and PGDG  
      ```bash
      sudo yum install http://yum.puppetlabs.com/puppet/puppet-release-el-7.noarch.rpm epel-release https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7-x86_64/pgdg-centos96-9.6-3.noarch.rpm
      ```
   1. (Optional) Disable the mirrorlists, and force the use of the TENET's mirror.ac.za mirror  
      ```bash
      sudo sed -ri 's|^(mirrorlist)|#\1|g;s|#baseurl=http://mirror.centos.org/centos/(.*)|baseurl=http://centos.mirror.ac.za/\1|g' /etc/yum.repos.d/CentOS-*.repo
      ```
      ```bash
      sudo sed -ri 's|^(mirrorlist)|#\1|g;s|#baseurl=http://download.fedoraproject.org/pub/(.*)|baseurl=http://fedora.mirror.ac.za/\1|g' /etc/yum.repos.d/epel*.repo
      ```
   1. Update the VM  
      `sudo yum clean metadata && sudo yum update`
1. Reboot

[Previous](install-vm.md) \| [Home](index.md) \| [Next](install-puppet-server.md)
