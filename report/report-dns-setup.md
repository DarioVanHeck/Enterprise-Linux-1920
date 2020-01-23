# Enterprise Linux Lab DNS Setup Report

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

This report is for the installation and configuration of the DNS Server. This will contain the setup for the slave and master dns server.

## Test plan

I suggest opening two terminals for this part.
To test the servers, we will run server 'pr001' and 'pr002', we will do this by using the 'vagrant up pr001' and 'vagrant up pr002' command, this should run without errors.   
Connect with the server by using the 'vagrant ssh pr001' and 'vagrant ssh pr002' command. To test if all the requirements are met, we use the 'sudo /vagrant/test/runbats.sh' command on both servers.  

## Procedure/Documentation

### Step 1: Create the 'pr001.yml' and 'pr002.yml' file
All the variables set in these files will be used for every server that's being set up.

### Step 2: Adding the server to the 'Vagrant host' file
* Add the server 'pr001' and 'pr002' to the vagrant host file.

```yml
- name: pr001
  ip: 172.16.192.1
  netmask: 255.255.0.0

- name: pr002
  ip: 172.16.192.2
  netmask: 255.255.0.0
```

### Step 3: Adding the roles for this server in 'site.yml'
* Add these lines to the 'site.yml' file, this way the server will use the right roles.

```yml
- hosts: pr001
  become: true
  roles:
    - bertvv.bind

- hosts: pr002
  become: true
  roles:
    - bertvv.bind
```

### Step 4: Filling the file with the right variables in pr001
All the variables are in the file: [pr001.yml](..\ansible\host_vars\pr001.yml)
Small explanation:  

* Allowing all hosts to the DNS server
```yml
bind_allow_query:
  - any
```

* Let DNS server listen on all interfaces
```yml
bind_listen_ipv4:
  - any
```

* Setting the ip of the master
```yml
bind_zone_master_server_ip: 172.16.192.1
```

* Setting the required domains and hosts found in [this file](..\README.md)
```yml
bind_zone_domains:
  - name: avalon.lan
    hosts:
      - name: pu001
        ip: '192.0.2.10'
        aliases:
          - name: www
      - name: pu002
        ip: '192.0.2.20'
        aliases:
          - name: mail
      - name: pr001
        ip: '172.16.192.1'
        aliases:
          - name: ns1
      - name: pr002
        ip: '172.16.192.2'
        aliases:
          - name: ns2
      - name: pr003
        ip: '172.16.192.3'
        aliases:
          - name: dhcp
      - name: pr004
        ip: '172.16.192.4'
        aliases:
          - name: directory
      - name: pr010
        ip: '172.16.192.10'
        aliases:
          - name: inside
      - name: pr011
        ip: '172.16.192.11'
        aliases:
          - name: files
    networks:
      - '172.16'
      - '192.0.2'
    name_servers:
      - pr001
      - pr002
    mail_servers:
      - name: pu002
        preference: 10
```

* Setting the binding key
```yml
bind_dns_keys:
  - name: master_key
    algorithm: md5
    secret: "Olivier$2019"
```

* Allowing DNS through the firewall
```yml
rhbase_firewall_allow_services:
  - dns
```
### Step 5: Filling the file with the right variables in pr002
All the variables are in the file: [pr002.yml](..\ansible\host_vars\pr002.yml)
Small explanation:

* Allowing all hosts to the DNS server
```yml
bind_allow_query:
  - any
```

* Let DNS server listen on all interfaces
```yml
bind_listen_ipv4:
  - any
```

* Setting the ip of the master
```yml
bind_zone_master_server_ip: 172.16.192.1
```

* Setting the required domains [this file](..\README.md)
```yml
bind_zone_domains:
  - name: avalon.lan
    networks:
      - '172.16'
      - '192.0.2'
```

* Allowing DNS through the firewall
```yml
rhbase_firewall_allow_services:
  - dns
```

## Test report

### Step 1: Log in to both your servers
To login to both the servers and use the 'vagrant ssh pr001' and 'vagrant ssh pr002' command on each server. When you are connected to the server use the 'sudo /vagrant/test/runbats.sh'

### Step 2: Check if all the test ran successfully on pr001
When you have ran all the tests, they should look like this:  
```console
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
Running test /vagrant/test/pr001/masterdns.bats
 ✓ The dig command should be installed
 ✓ The main config file should be syntactically correct
 ✓ The forward zone file should be syntactically correct
 ✓ The reverse zone files should be syntactically correct
 ✓ The service should be running
 ✓ Forward lookups public servers
 ✓ Forward lookups private servers
 ✓ Reverse lookups public servers
 ✓ Reverse lookups private servers
 ✓ Alias lookups public servers
 ✓ Alias lookups private servers
 ✓ NS record lookup
 ✓ Mail server lookup

13 tests, 0 failures
```
### Step 2: Check if all the test ran succesfully on pr002
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
Running test /vagrant/test/pr002/slavedns.bats
 ✓ The dig command should be installed
 ✓ The main config file should be syntactically correct
 ✓ The server should be set up as a slave
 ✓ The server should forward requests to the master server
 ✓ There should not be a forward zone file
 ✓ The service should be running
 ✓ Forward lookups public servers
 ✓ Forward lookups private servers
 ✓ Reverse lookups public servers
 ✓ Reverse lookups private servers
 ✓ Alias lookups public servers
 ✓ Alias lookups private servers
 ✓ NS record lookup
 ✓ Mail server lookup

14 tests, 0 failures
```
## Resources

* [RH-Base bertvv](https://galaxy.ansible.com/bertvv/rh-base)
* [Bind bertvv](https://galaxy.ansible.com/bertvv/bind)
