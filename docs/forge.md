# Install a module from the Puppet Forge

[Previous](create-hierarchy.md) \| [Home](index.md) \| [Next](override.md)

The [Puppet Forge](https://forge.puppet.com) is a repository of modules for Puppet.

In this example we'll install a module which will manage our YUM repositories. Searching for 'yum', we find the first 'Approved' module is 'puppet/yum'. It has a high quality score, a high community rating and a lot of downloads. It'll probably suit our purposes.

## Install the module

To install the module, we can either download it manually and check its dependencies manually, or have Puppet sort it out for us:  
`/opt/puppetlabs/bin/puppet module install puppet-yum --modulepath /etc/puppetlabs/code/environments/test/modules`

We need to specify the path to the modules directory because we're not running the command as 'root'. We also have the option of installing the modules into '/etc/puppetlabs/code/modules', sharing the modules between all environments.

![](images/forge-1.png)

Notice how it automatically installed two other modules (puppetlabs-stdlib and puppetlabs-concat) as dependencies.

Let's commit our changes:  
   `git add modules`  
   `git commit -m "Added puppet-yum module"`

## Include the module

We've installed the module, but it's not doing anything yet. Let's include the module in 'data/common.yaml' but not configure it. Then let's see what the module will try to do without any configuration.

1. Edit 'data/common.yaml' to include the 'yum' class  

   ```yaml
   ---
   classes:
     - yum
   ```

1. Run the agent in 'dryrun' or 'noop' mode  
   ```bash
   sudo /opt/puppetlabs/bin/puppet agent --verbose --onetime --no-daemonize --server workshop.vm --environment test --noop
   ```

   ![](images/forge-2.png)

1. We can see that it wants to install the 'yum-utils' package, and set a couple of configuration options for deleting old, unused kernels. We're quite happy with that, so have it apply  
   ```bash
   sudo /opt/puppetlabs/bin/puppet agent --verbose --onetime --no-daemonize --server workshop.vm --environment test
   ```

## Configure the module

Read the module's [readme](https://forge.puppet.com/puppet/yum/readme) on the Forge and the 'manifests/init.pp' file to get an idea of how to configure the module.

Let's have Puppet control all the YUM repositories on the host. This is accomplished with the '[yum::repos](https://forge.puppet.com/puppet/yum/readme#manage-a-custom-repo-via-hiera-data)' key.

Seeing as YUM repos are settings that apply to all hosts running the same operating system, it would make sense to put this configuration in a per-OS configuration file in our hierarchy. We'll place repos that are specific to CentOS in 'data/os/CentOS.yaml', and repos that are valid for all OS's in the RedHat family (RedHat, CentOS, Scientific, Amazon, Oracle) in 'data/os/RedHat.yaml'.

1. Create the 'data/os' directory  
   `mkdir data/os`
1. Create 'data/os/CentOS.yaml' with the following contents  

   ```yaml
   ---
   yum::managed_repos:
     - 'base'
     - 'updates'
     - 'extras'
     - 'centosplus'
   yum::repos:
     'base':
       baseurl: 'http://centos.mirror.ac.za/$releasever/os/$basearch/'
       mirrorlist: 'absent'
     'updates':
       baseurl: 'http://centos.mirror.ac.za/$releasever/updates/$basearch/'
       mirrorlist: 'absent'
     'extras':
       baseurl: 'http://centos.mirror.ac.za/$releasever/extras/$basearch/'
       mirrorlist: 'absent'
     'centosplus':
       baseurl: 'http://centos.mirror.ac.za/$releasever/centosplus/$basearch/'
       mirrorlist: 'absent'
   ```

1. Create 'data/os/RedHat.yaml' with the following contents  

   ```yaml
   ---
   yum::managed_repos:
     - 'epel'
   yum::repos:
     'epel':
       baseurl: 'http://fedora.mirror.ac.za/epel/$releasever/$basearch/'
       mirrorlist: 'absent'
   ```

1. Run the agent in 'dryrun' or 'noop' mode  
   ```bash
   sudo /opt/puppetlabs/bin/puppet agent --verbose --onetime --no-daemonize --server workshop.vm  --environment test --noop
   ```

   ![](images/forge-3.png)

1. We can see that only minor changes are required have this machine's repositories conform with our configuration. We're quite happy with that, so have it apply  
   ```bash
   sudo /opt/puppetlabs/bin/puppet agent --verbose --onetime --no-daemonize --server workshop.vm --environment test
   ```

Let's commit our changes:  
   `git add data/common.yaml data/os`  
   `git commit -m "Added first puppet-yum configurations"`

![](images/forge-4.png)


[Previous](create-hierarchy.md) \| [Home](index.md) \| [Next](override.md)
