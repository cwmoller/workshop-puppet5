# Create a new environment

[Previous](install-puppet-db-termini.md) \| [Home](index.md) \| [Next](create-hierarchy.md)

By default a barebones environment called 'production' is installed by the server package. Unconfigured agents will try to apply the 'production' environment by default. It's therefore a good idea to leave it empty so that you don't accidentally apply configurations to a new agent.

1. Create a new directory in '/etc/puppetlabs/code/environments' for your environment and give your user read/write access to it  
   `sudo mkdir /etc/puppetlabs/code/environments/test`  
   `sudo chown sysadm.users /etc/puppetlabs/code/environments/test`

   ![](create-environment-1.png)
1. Create an empty Git repository in the new environment  
   `cd /etc/puppetlabs/code/environments/test`  
   `git init`
1. Copy the barebones 'production' environment's contents  
   `cp -a ../production/* .`
1. Add the new files to git, and commit the changes  
   `git add .`  
   `git commit -m "New empty environment"`

   ![](create-environment-2.png)
2. Apply the environment to our server  
   `sudo /opt/puppetlabs/bin/puppet agent --verbose --onetime --no-daemonize --server workshop.vm --environment test`

   ![](create-environment-3.png)


[Previous](install-puppet-db-termini.md) \| [Home](index.md) \| [Next](create-hierarchy.md)
