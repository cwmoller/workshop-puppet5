# Install Puppet Server 5

[Previous](configure-vm.md) \| [Home](index.md) \| [Next](install-postgresql.md)

1. Install Puppet Server from the repository. Note that the Puppet Agent is a dependency and is installed automatically.  
   `yum install puppetserver`
1. Enable and start the service  
   `systemctl enable puppetserver`  
   `systemctl start puppetserver`

   On first startup the server generates its CA certificate and a new certificate for the server.  
   ```
   [sysadm@workshop ~]$ sudo find /etc/puppetlabs/puppet/ssl/ -name "*.pem"
   /etc/puppetlabs/puppet/ssl/certs/ca.pem
   /etc/puppetlabs/puppet/ssl/certs/workshop.vm.pem
   /etc/puppetlabs/puppet/ssl/public_keys/workshop.vm.pem
   /etc/puppetlabs/puppet/ssl/private_keys/workshop.vm.pem
   /etc/puppetlabs/puppet/ssl/ca/signed/workshop.vm.pem
   /etc/puppetlabs/puppet/ssl/ca/ca_pub.pem
   /etc/puppetlabs/puppet/ssl/ca/ca_key.pem
   /etc/puppetlabs/puppet/ssl/ca/ca_crt.pem
   /etc/puppetlabs/puppet/ssl/ca/ca_crl.pem
   /etc/puppetlabs/puppet/ssl/crl.pem
   [sysadm@workshop ~]$ sudo /opt/puppetlabs/bin/puppet cert list --all
   + "workshop.vm" (SHA256) 52:C8:<snip>:F3:BB (alt names: "DNS:puppet", "DNS:workshop.vm")
   ```
1. Test that the agent on the server is happy with the certificates  
   ```
   [sysadm@workshop ~]$ sudo /opt/puppetlabs/bin/puppet agent --test --server workshop.vm
   Info: Using configured environment 'production'
   Info: Retrieving pluginfacts
   Info: Retrieving plugin
   Info: Caching catalog for workshop.vm
   Info: Applying configuration version '1508334053'
   Info: Creating state file /opt/puppetlabs/puppet/cache/state/state.yaml
   Notice: Applied catalog in 0.03 seconds
```

[Previous](configure-vm.md) \| [Home](index.md) \| [Next](install-postgresql.md)
