# Enterprise Linux Lab Server Setup Report

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

Make a basic server which will be used as template for all the upcoming servers.   
Makes sure these requirements are present:  
* The firewall should be enabled and running.
* SELinux should be set to 'Enforcing'.
* The EPEL repository must be installed (Extra Packages for Enterprise Linux, a third-party package repository)
* The following packages must be installed on all servers
    * bash-completion
    * bind-utils
    * git
    * nano
    * tree
    * vim-enhanced
    * wget

* Create a user account for yourself. Take your first name in lowercase. This user will become "administrator" of the system, which means `sudo` rights in practice.
* Generate an ssh key pair on your *host system* and install the public key on every server in your user account. You should be able to log into your servers without having to enter a password.
* Use the option to generate a custom `/etc/motd` file that shows network connection information after login.
Describe the goals of the current iteration/assignment in a short sentence.

## Test plan

To test the basic server, we will run server 'pu001', we will do this by using the 'vagrant up pr001' command, this should run without errors.   
Connect with the server by using the 'vagrant ssh pu001' command. To test if all the requirements are met, we use the 'sudo /vagrant/test/runbats.sh' command.
Afterwards we try to log in 5 times with ssh and ftp.  

## Procedure/Documentation

### Step 1: Create the 'All.yml' file  
All the variables set in this file will be used for every server that's being set up.

### Step 2: Adding the roles for this server in 'site.yml'
* Add these lines to the 'site.yml' file, this way the server will use the right roles.
```yml
- hosts: all
  become: true
  roles:
    - bertvv.rhbase
```

### Step 3: Filling the file with the right variables  
All the variables are in the file: [all.yml](..\ansible\group_vars\all.yml)  
Small explanation:  

* Installation of the epel repository  
``` yml
rhbase_repositories:
  - epel-release
```

* Installation of the required pacakages  
``` yml
rhbase_install_packages:
  - bash-completion, bash-completion-extras
  - bind, bind-utils
  - git
  - nano
  - tree
  - vim-enhanced
  - wget
```

* Addition of the admin_user  
``` yml
rhbase_users:
  - name: olivier
    comment: 'Olivier Troch'
    groups:
      - wheel
    password: "$6$huIyFIj/FYrKWXJi$2kAUI4c16rDggm4t8fjxKjpfGVc3h1L14/dkbuGzcHGloBJJ4qQ/MKWu.OMnpofQfddXEYU.FFVkhOXpWZFWA/"
```

* Addition of the ssh admin_user  
``` yml
rhbase_ssh_user: olivier
rhbase_ssh_key: ssh-rsa 'SSHKEY' (this key should be generated on your host system)
```

* Activation dynamic message of the day  
``` yml
rhbase_dynamic_motd: yes
```

#### Addition of Fail2Ban
All the variables are in the file: [all.yml](..\ansible\group_vars\all.yml)  
Small explanation:  

* Ingoring the ip's from inside the server
``` yml
fail2ban_ignoreips:
 - 192.0.2.0/24
# - 172.16.0.0/16
 - 127.0.0.1/8
```

* Setting the ban time and max tries
```yml
fail2ban_bantime: 86400
fail2ban_maxretry: 5
```

* Configuring specific services
```yml
fail2ban_services:
 - name: sshd
   port: 22
   maxretry: 3
   bantime: 86400
 - name: ftp
   port: 20, 21
   maxretry: 3
   bantime: 86400
   logpath: /var/log/ftpF2B.log
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
```
### Step 3: Log in using ssh on the given ip
Log in on the server using this ip: '192.0.2.10'. This should work without giving a password. Use this command:
``` bash
ssh olivier@192.0.2.10
```

## Resources

* [RH-Base bertvv](https://galaxy.ansible.com/bertvv/rh-base)
* [Ansible getting started](https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html)
* [YML syntax in Ansible](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)
* [MKPasswd](https://www.mkpasswd.net/index.php)
* [Fail2Ban](https://galaxy.ansible.com/nbigot/ansible-fail2ban)
