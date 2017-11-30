## Override the configuration

[Previous](forge.md) \| [Home](index.md)

We've set up a good baseline for a generic CentOS machine's YUM repositories, and all our CentOS machines should get that configuration. Our Puppet server machine is special though, and needs some special configurations.

Let's add the PostgreSQL repo.

1. Create the 'data/nodes' directory  
   `mkdir data/nodes`
1. Create a node-specific configuration file for 'workshop.vm'  
   `touch data/nodes/workshop.vm.yaml`
1. Add the configuration for the PostgreSQL repo  
   ```yaml
   ---

   yum::managed_repos:
     - 'pgdg96'
   yum::repos:
     'pgdg96':
       descr: 'PostgreSQL 9.6 $releasever - $basearch'
       baseurl: 'https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-$releasever-$basearch'
       enabled: true
       gpgcheck: true
       gpgkey: 'file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG-96'

   ```

[Previous](forge.md) \| [Home](index.md)
