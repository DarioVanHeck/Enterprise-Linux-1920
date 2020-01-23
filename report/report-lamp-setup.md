# Enterprise Linux Lab Lamp Setup Report

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

This report is for the installation and configuration of the LAMP Server. LAMP means that this is a Linux System with Apache, MariaDB and PHP on it.
Wordpress will also be installed.

## Test plan

To test the server, we will run server 'pu001', we will do this by using the 'vagrant up pu001' command, this should run without errors.   
Connect with the server by using the 'vagrant ssh pu001' command. To test if all the requirements are met, we use the 'sudo /vagrant/test/runbats.sh' command.  

## Procedure/Documentation

### Step 1: Create the 'pu001.yml' file  
All the variables set in this file will be used for setting up the LAMP Server.

### Step 2: Adding the server to the 'Vagrant host' file
* Add the server to the vagrant host file.

```yml
- name: pu001
  ip: 192.0.2.10
```

### Step 3: Adding the roles for this server in 'site.yml'
* Add these lines to the 'site.yml' file, this way the server will use the right roles.
```yml
- hosts: pu001
  become: true
  roles:
    - bertvv.httpd
    - bertvv.mariadb
    - bertvv.wordpress
```

### Step 4: Create the self signed SSL-certificate
For this step I've followed this [guide](https://wiki.centos.org/HowTos/Https).
When these files are created, you put them in the 'Files' folder in your workspace.

### Step 5: Filling the file with the right variables  
All the variables are in the file: [pu001.yml](..\ansible\host_vars\pu001.yml)  
Small explanation:  

* Setting mariadb bind address
```yml
mariadb_bind_address: '0.0.0.0'
```

* Setting up mariadb (pu001_db)
```yml
mariadb_databases:
  - name: wp_db
```

* Adding mariadb user
```yml
mariadb_users:
  - name: wp_user
    password: CorkIgWac
    priv: 'wp_db.*:ALL'
    append_privs: yes
```

* Pass for pu001_db
```yml
mariadb_root_password: fogMeHud8
```

* Define the role variables in the correct location
```yml
httpd_ssl_certificate_key_file: 'acme-inc.key'
httpd_ssl_certificate_file: 'acme-inc.crt'
```

* Setting wordpress database name
```yml
wordpress_database: wp_db
```

* Setting wordpress database password
```yml
wordpress_password: CorkIgWac
```

* Setting wordpress database user
```yml
wordpress_user: wp_user
```

* Allow webtraffic through firewall
```yml
rhbase_firewall_allow_services:
  - http
  - https
```

## Test report

### Step 1: Log in to your server
To login to the server use the 'vagrant ssh pu001' command. When you are connected to the server use the 'sudo /vagrant/test/runbats.sh'

### Step 2: Check if all the test ran successfully
When you have ran all the tests, they should look like this:
``` console
Running test /vagrant/test/common.bats
 ✓ SELinux should be set to 'Enforcing'
 ✓ Firewall should be enabled and running
 ✓ EPEL repository should be available
 ✓ Bash-completion should have been installed
 ✓ bind-utils should have been installed
 ✓ Git should have been installed
 ✓ Nano should have been installed
 ✓ Tree should have been installed
 ✓ Vim-enhanced should have been installed
 ✓ Wget should have been installed
 ✓ Admin user olivier should exist
 ✓ An SSH key should have been installed for olivier
 - Custom /etc/motd should have been installed (skipped)

13 tests, 0 failures, 1 skipped
Running test /vagrant/test/pu001/lamp.bats
 ✓ The necessary packages should be installed
 ✓ The Apache service should be running
 ✓ The Apache service should be started at boot
 ✓ The MariaDB service should be running
 ✓ The MariaDB service should be started at boot
 ✓ The SELinux status should be ‘enforcing’
 ✓ Web traffic should pass through the firewall
 ✓ Mariadb should have a database for Wordpress
 ✓ The MariaDB user should have write
 ✓ The website should be accessible through HTTP
 ✓ The website should be accessible through HTTPS
 ✓ The certificate should not be the default one
 ✓ The Wordpress install page should be visible under http://192.0.2.10/wordpress/
 ✓ MariaDB should not have a test database
 ✓ MariaDB should not have anonymous users

15 tests, 0 failures
```

## Resources

[SSL certificate](https://wiki.centos.org/HowTos/Https)  
[MariaDB bertvv](https://galaxy.ansible.com/bertvv/mariadb)  
[HTTPD bertvv](https://galaxy.ansible.com/bertvv/httpd)  
