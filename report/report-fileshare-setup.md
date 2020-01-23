# Enterprise Linux Lab Fileshare Setup Report

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

This report is for the installation and configuration of the file server. The server will contain the following packages:
- SAMBA
- FTP
- VSFTPD

## Test plan

To test the server, we will run server 'pr011', we will do this by using the 'vagrant up pr011' command, this should run without errors.   
Connect with the server by using the 'vagrant ssh pr011' command. To test if all the requirements are met, we use the 'sudo /vagrant/test/runbats.sh' command.  


## Procedure/Documentation

### Step 1: Create the 'pr011.yml' file
All the variables set in this file will be used for the server that's being set up.

### Step 2: Adding the server tot the 'Vagrant host' file
* Add the server 'pr011' to the vagrant host file.

```yml
- name: pr011
  ip: 172.16.192.11
  netmask: 255.255.0.0
```

### Step 3: Adding the roles for this server in 'site.yml'
* Add these lines to the 'site.yml' file, this way the server will use the right roles.

```yml
- hosts: pr011
  become: true
  roles:
    - bertvv.samba
    - bertvv.vsftpd
```

### Step 4: Filling the file with the right variables in pr001
All the variables are in the file: [pr011.yml](..\ansible\host_vars\pr011.yml)  
Small explanation:  

* Defining user groups
```yml
rhbase_user_groups:
  - management
  - technical
  - sales
  - it
  - public
```

* Setting 'netbios name' and define access for guests
```yml
samba_netbios_name: 'files'
samba_map_to_guest: 'Never'
```

* Defining shares root location and samba workgroup
```yml
samba_shares_root: /srv/shares/
samba_workgroup: "avalon"
```

* Allowing 'samba' and 'ftp' through the firewall
```yml
rhbase_firewall_allow_services:
  - samba
  - ftp
```

* Define samba users with the appropriate password
```yml
samba_users:
  - name: stevenh
    password: stevenh
  - name: stevenv
    password: stevenv
  - name: leend
    password: leend
  - name: svena
    password: svena
  - name: nehirb
    password: nehirb
  - name: alexanderd
    password: alexanderd
  - name: krisv
    password: krisv
  - name: benoitp
    password: benoitp
  - name: anc
    password: anc
  - name: elenaa
    password: elenaa
  - name: evyt
    password: evyt
  - name: christophev
    password: christophev
  - name: stefaanv
    password: stefaanv
  - name: olivier
    password: olivier

```

* Adding the rhbase users with password(crypt-sha512) and groups
```yml
rhbase_users:
  - name: stevenh
    password: '$6$MFU5gA0C/ACXNLG4$tEUOYqq0PkN/ooRg1Ahyyur/zpBSnmf45KdzmkBamApvo0EcH8jF2PTnlRMTqJ5HhU6OBOvBQS.4geo4gDbIf1'
    groups:
      - management
      - public
  - name: stevenv
    password: '$6$d5pVotLBV8KCckyJ$5BFw7DI611AntBczpY4SSNREyD59HN7SKpasw0U6vfTaI.1Jp/Au9a3eg3Pih1W1rSV8zJ4xb66Z6hJc4X6Uh1'
    groups:
      - technical
      - public
  - name: leend
    password: '$6$kOw3k5yD1l9gT9zm$AOXah/rEcnlfoKHSMzzFeS9BU.3fi7bdCrfmhRIGjeHl2XQ/kO1UFIwS8gima7X8cHQB1fg6PqVwdzwb3yExH.'
    groups:
      - technical
      - public
  - name: svena
    password: '$6$J6Gv13KwQieFBQUL$1/DqYFN3csWzOtoU/dH3zsgw57LMqJpAGawSXeVqJW12PzxejGJCJWt8rr.v6mctqXyDtCs68WxzdSMwMuN53.'
    groups:
      - sales
      - public
  - name: nehirb
    password: '$6$k8WzUy6qvbM/uZfY$W.6bcwuFbCSqiTxyfNuo7XpxD6QMiEycOEQWCTjJVQRBggROOEx9x4y/6w682f2yvp26e9oMia45T/YSvm2Wz0'
    groups:
      - it
      - public
  - name: alexanderd
    password: '$6$2USPpx3f23dsWFqM$EpeEhgRBn2RwBAOX9N7QNT23O.6.n9Gb9rIGycQYeqOkK1BFJKye2kJLpsaluqOzgVlrQa87mtBr4YTMKbdF2/'
    groups:
      - technical
      - public
  - name: krisv
    password: '$6$ULFdMW1oPHHfyUAi$P.TWGluZgIeiRzhdkxQ1d.8UL.Znbz8o0.dj75Mh1HFfgokdr6KfA/NPEeq6R/6/VsqpDbloDgs4wmP8tZcvw.'
    groups:
      - management
      - public
  - name: benoitp
    password: '$6$V1QxTaF7x8uZ2R9w$JX2F4ESYnZFhF42R9qA0cp0PLY4zhFg2igga.bzoZi2GC90SZUgcCarUf/e7o89vbuh7BDjwV3rWoB3cvkylv0'
    groups:
      - sales
      - public
  - name: anc
    password: '$6$12sXsG9pFpussSMf$NT6VgjoKU0wrDrJhvdAJvU8f0OS295YDYgTtwXE98J0e6khixzb/LxJcbthpdbxGMOPVCh8gpEvGKrkxv070s1'
    groups:
      - technical
      - public
  - name: elenaa
    password: '$6$wTmNawxIbnkEe/MD$bOeDavCsP1kifBEdKlVXE.sdu8H6S0w9lXX32FYW/cSqmyYtAFUxKneQmOTike8dyINp/0517pkxCbi1uDPVe1'
    groups:
      - management
      - public
  - name: evyt
    password: '$6$d1ndyOKq4fKHuShG$U7Y5UbqJt0Jh04sstDDSJzj.BgNkr6NWkZ4GaxAhrwalyTe.qxIDsVRvfQZkXo/j5ItamsR3ovQ95ZBbuv7nT.'
    groups:
      - technical
      - public
  - name: christophev
    password: '$6$ZuOQs9qRpCNNpCJF$Y4q0hRK5lfLIu8xiIrxLe6ItzL103VP80pP9p3yP6ze2TH3/6FP77.iMaWT3c1eSKpUREuUoIC1S7UGFzp7D20'
    groups:
      - it
      - public
  - name: stefaanv
    password: '$6$2e2tM0lVfeUJJZ/U$4hWVaxzcQWsjJIbr09VzmB58qSJ86ScmurFKY.bnlFrwjFRo71rNfruth1pFWhNVMgDs7mI5plQPuyP99oAQI.'
    groups:
      - technical
      - public
  - name: olivier
    password: '$6$huIyFIj/FYrKWXJi$2kAUI4c16rDggm4t8fjxKjpfGVc3h1L14/dkbuGzcHGloBJJ4qQ/MKWu.OMnpofQfddXEYU.FFVkhOXpWZFWA/'
    groups:
      - it
      - public
      - wheel
```

* Setting the correct samba shares with the right permissions
```yml
samba_shares:
  - name: public
    group: public
    write_list: +public
    valid_users: +public
    path: /srv/shares/public
  - name: management
    group: management
    write_list: +management
    valid_users: +management
    path: /srv/shares/management
  - name: technical
    group: technical
    write_list: +technical
    valid_users: +technical, +public
    path: /srv/shares/technical
  - name: sales
    group: sales
    write_list: +sales
    valid_users: +sales, +management
    path: /srv/shares/sales
  - name: it
    group: it
    write_list: +it
    valid_users: +it, +management
    path: /srv/shares/it
```

* Extra permissions for vsftpd
```yml
vsftpd_extra_permissions:
    #public
  - folder: "/srv/shares/public"
    entity: "public"
    etype: "group"
    permissions: "rwx"

    #management
  - folder: "/srv/shares/management"
    entity: "management"
    etype: "group"
    permissions: "rwx"
  - folder: "/srv/shares/management"
    entity: "sales"
    etype: "group"
    permissions: "---"
  - folder: "/srv/shares/management"
    entity: "it"
    etype: "group"
    permissions: "---"
  - folder: "/srv/shares/management"
    entity: "technical"
    etype: "group"
    permissions: "---"

    #sales
  - folder: "/srv/shares/sales"
    entity: "sales"
    etype: "group"
    permissions: "rwx"
  - folder: "/srv/shares/sales"
    entity: "it"
    etype: "group"
    permissions: "---"
  - folder: "/srv/shares/sales"
    entity: "technical"
    etype: "group"
    permissions: "---"
  - folder: "/srv/shares/sales"
    entity: "management"
    etype: "group"
    permissions: "r-x"

    #it
  - folder: "/srv/shares/it"
    entity: "it"
    etype: "group"
    permissions: "rwx"
  - folder: "/srv/shares/it"
    entity: "management"
    etype: "group"
    permissions: "r-x"
  - folder: "/srv/shares/it"
    entity: "sales"
    etype: "group"
    permissions: "---"
  - folder: "/srv/shares/it"
    entity: "technical"
    etype: "group"
    permissions: "---"

    #technical
  - folder: "/srv/shares/technical"
    entity: "technical"
    etype: "group"
    permissions: "rwx"
  - folder: "/srv/shares/technical"
    entity: "public"
    etype: "group"
    permissions: "r-x"
```

* Disallow anonymous access and set the local root
```yml
vsftpd_anonymous_enable: false
vsftpd_local_enable: true
vsftpd_local_root: /srv/shares
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
Running test /vagrant/test/pr011/samba.bats
 ✓ The ’nmblookup’ command should be installed
 ✓ The ’smbclient’ command should be installed
 ✓ The Samba service should be running
 ✓ The Samba service should be enabled at boot
 ✓ The WinBind service should be running
 ✓ The WinBind service should be enabled at boot
 ✓ The SELinux status should be ‘enforcing’
 ✓ Samba traffic should pass through the firewall
 ✓ Check existence of users
 ✓ Checks shell access of users
 ✓ Samba configuration should be syntactically correct
 ✓ NetBIOS name resolution should work
 ✓ read access for share ‘public’
 ✓ write access for share ‘public’
 ✓ read access for share ‘management’
 ✓ write access for share ‘management’
 ✓ read access for share ‘technical’
 ✓ write access for share ‘technical’
 ✓ read access for share ‘sales’
 ✓ write access for share ‘sales’
 ✓ read access for share ‘it’
 ✓ write access for share ‘it’

22 tests, 0 failures
Running test /vagrant/test/pr011/vsftp.bats
✓ VSFTPD service should be running
✓ VSFTPD service should be enabled at boot
✓ The ’curl’ command should be installed
✓ The SELinux status should be ‘enforcing’
✓ FTP traffic should pass through the firewall
✓ VSFTPD configuration should be syntactically correct
✓ Anonymous user should not be able to see shares
✓ read access for share ‘public’
✓ write access for share ‘public’
✓ read access for share ‘management’
✓ write access for share ‘management’
✓ read access for share ‘technical’
✓ write access for share ‘technical’
✓ read access for share ‘sales’
✓ write access for share ‘sales’
✓ read access for share ‘it’
✓ write access for share ‘it’


17 tests, 0 failures

```
## Resources

* [MKPasswd](https://www.mkpasswd.net/index.php)
* [vsftpd bertvv](https://galaxy.ansible.com/bertvv/vsftpd)
* [samba bertvv](https://galaxy.ansible.com/bertvv/samba)
