# Enterprise Linux Lab Integration Setup Report

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

Complete the network we've been building with a DHCP server for workstations and a router.

## Test plan

To test the server, we will run all the servers, we will do this by using the 'vagrant up' command, these should all run without errors. While these are being set up, we will also create the workstation. Give this host two NIC's(host-only adapters).  
Check the following settings on the workstation:
- Both interfaces should have an IP provided by DHCP
- Ping to verify connection
  - 'ping 192.0.2.10' = webserver
  - 'ping 172.16.192.1' = Primary DNS
  - 'ping 172.16.192.2' = Secondary DNS
  - 'ping 172.16.192.3' = DHCP Server
  - 'ping 172.16.192.11' = Fileserver
- Surf to the following sites via the browser
  - 'https://www.avalon.lan'
  - 'ftp://files.avalon.lan'

## Procedure/Documentation

### Step 1: Adding the server tot the 'Vagrant host' file
* Add the server 'ws0001' to the vagrant host file.

```yml
- name: ws0001
  ip: 172.16.192.11
  netmask: 255.255.0.0
```

### Step 2: Configure the 'router-config.sh' file
All the variables are in the file: [router-config.sh](..\scripts\router-config.sh)  
Small explanation:  

* Setting the interfaces
``` shell
set interfaces ethernet eth0 description "WAN link"
set interfaces ethernet eth0 address dhcp

set interfaces ethernet eth1 description DMZ
set interfaces ethernet eth1 address 192.0.2.254/24

set interfaces ethernet eth2 description Internal
set interfaces ethernet eth2 address 172.16.255.254/16
```  

* Setting the NAT rules
``` shell
set nat source rule 100 outbound-interface 'eth0'
set nat source rule 100 source address '172.16.0.0/16'
set nat source rule 100 translation address 'masquerade'

set nat source rule 200 outbound-interface 'eth0'
set nat source rule 200 source address '192.0.2.0/24'
set nat source rule 200 translation address 'masquerade'

set nat source rule 300 outbound-interface 'eth1'
set nat source rule 300 source address '172.16.0.0/16'
set nat source rule 300 translation address 'masquerade'
```  

* Setting the DNS forwarding
``` shell
set service dns forwarding domain avalon.lan server 172.16.192.1
set service dns forwarding name-server 10.0.2.3
set service dns forwarding listen-on 'eth1'
set service dns forwarding listen-on 'eth2'
```  
### Step 3: Create the 'pr003.yml' file
All the variables set in this file will be used for the server that's being set up.

### Step 4: Adding the server tot the 'Vagrant host' file
* Add the server 'pr003' to the vagrant host file.

```yml
- name: pr003
  ip: 172.16.192.3
  netmask: 255.255.0.0
```

### Step 5: Adding the roles for this server in 'site.yml'
* Add these lines to the 'site.yml' file, this way the server will use the right roles.

```yml
- hosts: pr003
  become: true
  roles:
    - bertvv.dhcp
    - bertvv.rhbase
```

### Step 6: Filling the file with the right variables in pr001
All the variables are in the file: [pr003.yml](..\ansible\host_vars\pr003.yml)  
Small explanation:  

* Install and allow DHCP through the firewall
```yml
rhbase_install_packages:
  - dhcp
rhbase_firewall_allow_services:
  - dhcp
```

* Setting default and max lease time
```yml
dhcp_global_default_lease_time: 14400
dhcp_global_max_lease_time: 1400
```

* Configuring subnet make
```yml
dhcp_global_subnet_make: 255.255.0.0
```

* Configuring the domain name
```yml
dhcp_global_domain_name: avalon.lan
```

* Configuring the router as the only DNS server
```yml
dhcp_global_domain_name_servers:
      - 172.16.255.254
```

* Configuring the global router
```yml
dhcp_global_routers: 172.16.255.254
```

* Configuring the subnets
```yml
dhcp_subnets:
  - ip: 172.16.0.0
    netmask: 255.255.0.0
    routers: 172.16.255.254
    pools:
      - range_begin: 172.16.0.2
        range_end: 172.16.127.254
      - range_begin: 172.16.128.1
        range_end: 172.16.191.254
```

* Configuring the DHCP Host
```yml
dhcp_hosts:
  - name: ws0001
    mac: '08:00:27:6e:3a:2c'
    ip: 172.16.128.2
```

## Test report

### Step 1: Log in on the client server
Start the server and follow the setup wizard.

### Step 2: Check if DHCP gave both interfaces an ip
Use the 'ip a' command in the terminal to check the ip addresses

### Step 3: Ping each server to check the connections
[x] 'ping 192.0.2.10' = webserver

```bash
ping 192.0.2.10
PING 192.0.2.10 (192.0.2.10) 56(84) bytes of data.  
64 bytes from 192.0.2.10: icmp_seq=1 ttl=63 time=2.34 ms  
64 bytes from 192.0.2.10: icmp_seq=2 ttl=63 time=0.693 ms  
64 bytes from 192.0.2.10: icmp_seq=3 ttl=63 time=0.518 ms  
64 bytes from 192.0.2.10: icmp_seq=4 ttl=63 time=0.651 ms  
^C  
--- 192.0.2.10 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3042ms  
rtt min/avg/max/mdev = 0.518/1.050/2.339/0.746 ms  
```

[x] 'ping 172.16.192.1' = Primary DNS
```bash
ping 172.16.192.1
PING 172.16.192.1 (172.16.192.1) 56(84) bytes of data.  
64 bytes from 172.16.192.1: icmp_seq=1 ttl=64 time=0.484 ms  
64 bytes from 172.16.192.1: icmp_seq=2 ttl=64 time=0.268 ms  
64 bytes from 172.16.192.1: icmp_seq=3 ttl=64 time=0.470 ms  
64 bytes from 172.16.192.1: icmp_seq=4 ttl=64 time=0.301 ms  
^C  
--- 172.16.192.1 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3050ms  
rtt min/avg/max/mdev = 0.268/0.380/0.484/0.097 ms   
```

[x] 'ping 172.16.192.2' = Secondary DNS
```bash
ping 172.16.192.2
PING 172.16.192.2 (172.16.192.2) 56(84) bytes of data.  
64 bytes from 172.16.192.2: icmp_seq=1 ttl=64 time=0.458 ms  
64 bytes from 172.16.192.2: icmp_seq=2 ttl=64 time=0.353 ms  
64 bytes from 172.16.192.2: icmp_seq=3 ttl=64 time=0.369 ms  
64 bytes from 172.16.192.2: icmp_seq=4 ttl=64 time=0.270 ms  
^C  
--- 172.16.192.2 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3108ms  
rtt min/avg/max/mdev = 0.270/0.362/0.458/0.066 ms  
```
[x] 'ping 172.16.192.3' = DHCP Server
```bash
ping 172.16.192.3
PING 172.16.192.3 (172.16.192.3) 56(84) bytes of data.  
64 bytes from 172.16.192.3: icmp_seq=1 ttl=64 time=0.284 ms  
64 bytes from 172.16.192.3: icmp_seq=2 ttl=64 time=0.379 ms  
64 bytes from 172.16.192.3: icmp_seq=3 ttl=64 time=0.273 ms  
64 bytes from 172.16.192.3: icmp_seq=4 ttl=64 time=0.267 ms  
^C  
--- 172.16.192.3 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3077ms    
rtt min/avg/max/mdev = 0.267/0.300/0.379/0.045 ms  
```
[x] 'ping 172.16.192.11' = Fileserver
```bash
ping 172.16.192.11
PING 172.16.192.11 (172.16.192.11) 56(84) bytes of data.  
64 bytes from 172.16.192.11: icmp_seq=1 ttl=64 time=0.648 ms  
64 bytes from 172.16.192.11: icmp_seq=2 ttl=64 time=0.288 ms  
64 bytes from 172.16.192.11: icmp_seq=3 ttl=64 time=0.259 ms  
64 bytes from 172.16.192.11: icmp_seq=4 ttl=64 time=0.353 ms  
^C  
--- 172.16.192.11 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3087ms    
rtt min/avg/max/mdev = 0.259/0.387/0.648/0.154 ms  
```

### Step 4: Use the built in browser and surf to these sites
[x] 'https://www.avalon.lan'  
![wwwDOTavalonDOTlan](/images/wwwDOTavalonDOTlan.png)  
[x] ´ftp://files.avalon.lan´  
![filesDOTavalonDOTlan](/images/filesDOTavalonDOTlan.png)  

## Resources

* [dhcp bertvv](https://galaxy.ansible.com/bertvv/dhcp)
* [Fedora Workstation](https://getfedora.org/en/workstation/download/)
