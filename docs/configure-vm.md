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
   1. Delete the password for the `root` user from `/etc/shadow`  
      `passwd -d root`
   1. Set SSH to either disallow root logins (`no`), or only allow root logins with a SSH key (`without-password`)  
      `sed -ri 's/^#?(PermitRootLogin).*/\1 without-password/g' /etc/ssh/sshd_config`  
      `sed -ri 's/^#?(PermitRootLogin).*/\1 no/g' /etc/ssh/sshd_config`  
      `systemctl restart sshd`
   1. Log in with your new user.
1. Static hostname  
   Puppet generates SSL certificates based on the machine's hostname. If the hostname changes due to the network being down or misconfigured, we don't want Puppet to get confused. The hostname doesn't need to conform to puppet.domain, but it helps a bit.  
   `sudo hostnamectl set-hostname workshop.vm`
1. Active network connection  
   The first network interface should have been automatically connected and set up to use DHCP. If not, make sure you have network connectivity and can access the internet.  
   Let's assume we're connected to a network with available addresses in the 172.30.30.0/24 network and route via 172.30.30.1 which is also acting as a DNS resolver. Edit `/etc/sysconfig/network-scripts/ifcfg-eth0` and add the following:  

   ```
   IPADDR=172.30.30.2
   GATEWAY=172.30.30.1
   PREFIX=32
   DNS1=172.30.30.1
   ```

1. Updated repositories
   1. Install the repositories for Puppetlabs, EPEL and SCL  
      `yum install http://yum.puppetlabs.com/puppet/puppet-release-el-7.noarch.rpm epel-release centos-release-scl-rh`
   1. (Optional) Disable the mirrorlists, and force the use of the local centos.mirror.ac.za mirror  
      ```
      sed -ri 's|^(mirrorlist)|#\1|g;s|#baseurl=http://mirror.centos.org/centos/(.*)|baseurl=http://centos.mirror.ac.za/\1|g' /etc/yum.repos.d/CentOS-*.repo
      ```
      ```
      sed -ri 's|^(mirrorlist)|#\1|g;s|#baseurl=http://download.fedoraproject.org/pub/(.*)|baseurl=http://fedora.mirror.ac.za/\1|g' /etc/yum.repos.d/epel*.repo
      ```
   1. Update the VM  
      `yum clean metadata && yum update`
1. Reboot

[Previous](install-vm.md) \| [Home](index.md) \| [Next](install-puppet-server.md)
