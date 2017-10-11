sed -ri 's|^mirrorlist|#mirrorlist|g;s|#baseurl=http://mirror.centos.org/centos/(.*)|baseurl=http://centos.mirror.ac.za/\1\nproxy=http://manager.hpc:3128|g' /etc/yum.repos.d/CentOS-*.repo



cat <<EOF > /etc/yum.repos.d/puppet.repo
[puppet]
name=Puppet Labs Repository el \$releasever - \$basearch
baseurl=http://yum.puppetlabs.com/puppet/el/\$releasever/\$basearch
enabled=1
gpgcheck=1
gpgkey=http://yum.puppetlabs.com/RPM-GPG-KEY-puppetlabs http://yum.puppetlabs.com/RPM-GPG-KEY-puppet
EOF

#cat <<EOF > /etc/yum.repos.d/epel.repo
#[epel]
#name=Extra Packages for Enterprise Linux \$releasever - \$basearch
#baseurl=http://fedora.mirror.ac.za/epel/\$releasever/\$basearch
#enabled=1
#gpgcheck=1
#gpgkey=http://fedora.mirror.ac.za/epel/RPM-GPG-KEY-EPEL-\$releasever
#EOF

cat <<EOF > /etc/yum.repos.d/scl.repo
[scl]
name=Software Collections RH
baseurl=http://centos.mirror.ac.za/\$releasever/sclo/\$basearch/rh
enabled=1
gpgcheck=1
gpgkey=https://raw.githubusercontent.com/sclorg-distgit/centos-release-sclo/master/RPM-GPG-KEY-CentOS-SIG-SCLo
EOF

#cat <<EOF > /etc/yum.repos.d/pgdg.repo
#[pgdg96]
#name=PostgreSQL 9.6 \$releasever - $basearch
#baseurl=https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-\$releasever-\$basearch
#enabled=1
#gpgcheck=1
#gpgkey=https://download.postgresql.org/pub/repos/yum/RPM-GPG-KEY-PGDG-96
#EOF

yum clean all
yum update

yum install rh-postgresql96-postgresql-{server,contrib}
scl enable rh-postgresql96 'bash -c "postgresql-setup initdb"'
systemctl enable rh-postgresql96-postgresql
systemctl start rh-postgresql96-postgresql 

###
### MAKE SURE THIS DOMAIN IS CORRECT
###
hostnamectl set-hostname hpcstorage.my.domain

yum install puppetserver
systemctl enable puppetserver
systemctl start puppetserver

cat <<EOF >> /etc/puppetlabs/puppet/puppet.conf
[agent]
server = hpcstorage.my.domain
EOF

/opt/puppetlabs/bin/puppet agent --test

yum install puppetdb
rm /etc/puppetlabs/puppetdb/conf.d/jetty.ini.*
cd /tmp
su - postgres -c scl enable rh-postgresql96 
createuser -DRSP puppetdb; createdb -E UTF8 -O puppetdb puppetdb; psql puppetdb -c "create extension pg_trgm"

### You will be prompted for a password. Choose your own, just make sure it matches the one you set in /etc/puppetlabs/puppetdb/conf.d/database.ini later
###
### This example's password = 0H49hu5NmfSZFXITrfxY
###

### exit back to root

sed -i -e '/^local/ilocal   all             postgres                                trust' -e 's/^\(local.*\)peer/\1md5/;s/^\(host.*\)ident/\1md5/' /var/opt/rh/rh-postgresql96/lib/pgsql/data/pg_hba.conf

systemctl restart rh-postgresql96-postgresql 
	
systemctl enable puppetdb

sed -i 's/^# subname/subname/;s/^# username.*/username = puppetdb/;s/^# password.*/password = 0H49hu5NmfSZFXITrfxY/' /etc/puppetlabs/puppetdb/conf.d/database.ini
sed -i 's/^\(JAVA_ARGS="-Xmx\).*"/\11g"/' /etc/sysconfig/puppetdb
systemctl start puppetdb

### Fix issue in server 5.0.0
puppetserver gem install gettext-setup

yum install puppetdb-termini

cat <<EOF >> /etc/puppetlabs/puppet/puppetdb.conf
[main]
server_urls = https://hpcstorage.my.domain:8081
EOF

sed -ri 's/(\[master\])/\1\nstoreconfigs = true\nstoreconfigs_backend = puppetdb\nreports = store,puppetdb\n/g' /etc/puppetlabs/puppet/puppet.conf

cat <<EOF >> /etc/puppetlabs/puppet/routes.yaml
---
master:
  facts:
    terminus: puppetdb
    cache: yaml
EOF

systemctl restart puppetserver
