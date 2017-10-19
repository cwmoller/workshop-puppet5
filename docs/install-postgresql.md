# Install PostgreSQL 9.5

[Previous](install-puppet-server.md) \| [Home](index.md) \| [Next](install-puppet-db.md)

1. Install PostgreSQL 9.5 from the SCL repository. CentOS 7 ships with PostgreSQL 9.2. The SCL repository provides version 9.5.  
   `yum install rh-postgresql95-postgresql-{server,contrib}`
1. Initialise the database.  

   ```
   [root@workshop ~]# scl enable rh-postgresql95 'bash -c "postgresql-setup --initdb --unit rh-postgresql95-postgresql"'
    * Initializing database in '/var/opt/rh/rh-postgresql95/lib/pgsql/data'
    * Initialized, logs are in /var/lib/pgsql/initdb_rh-postgresql95-postgresql.log
   ```

   Here we enabled the rh-postgresql95 environment, and ran a `bash` session inside.   

   The following would have had the same effect:  
   `scl enable rh-postgresql95 bash`  
   `postgresql-setup --initdb --unit rh-postgresql95-postgresql`  
   `exit`
1. Enable and start the service.  
   `systemctl enable rh-postgresql95-postgresql`  
   `systemctl start rh-postgresql95-postgresql`


[Official documentation](https://www.softwarecollections.org/en/scls/rhscl/rh-postgresql95/)

[Previous](install-puppet-server.md) \| [Home](index.md) \| [Next](install-puppet-db.md)
