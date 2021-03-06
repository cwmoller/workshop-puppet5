# HPC configuration management using Puppet 5

### Premise

All nodes in a compute cluster should conform to a common standard in order to be useful. With a configuration management system, we can ensure conformity and eliminate common errors which impede effective computing.

### Outcomes

In this workshop we will install and configure Puppet 5, a configuration management system, to manage our compute cluster.

We will install and configure the Puppet server, PuppetDB database, a Puppet agent and multiple modules from the Puppet Forge. Best practises will be implemented, including using Hiera and saving configurations to Git.

### Hands on

Feel free to go on ahead if you're well versed in any of the topics.

We'll be installing a small [CentOS 7.4](http://centos.mirror.ac.za/7.4.1708/) VM to host our Puppet installation. Copies of the VMDK/VHDX files will be made available. If you wish to follow along, please make sure you have a working installation of Hyper-V, VMware (Workstation or Player) or VirtualBox.

### Let's go!!

1. [Install a new CentOS 7.4 VM](install-vm.md)
1. [Configure the VM](configure-vm.md)
1. [Install Puppet Server 5](install-puppet-server.md)
1. [Install PostgreSQL 9.6](install-postgresql.md)
1. [Install Puppet DB](install-puppet-db.md)
1. [Connect the Puppet Server to the Puppet DB](install-puppet-db-termini.md)
1. Create and apply a configuration to the Agent
   1. [Create a new environment](create-environment.md)
   1. [Create a Hiera hierarchy](create-hierarchy.md)
   1. [Add a class from the Forge](forge.md)
   1. [Override the configuration](override.md)
