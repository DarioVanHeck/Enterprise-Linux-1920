# Cheat sheets and checklists

- Student name: Olivier Troch
- Github repo: <https://github.com/HoGentTIN/elnx-1920-sme-OlivierTroch>

## Basic commands

| Task                                       | Command                                |
| :---                                       | :---                                   |
| Query IP-adress(es)                        | `ip a`                                 |
| Show mac address(es)                       | `ifconfig -a | grep -Po 'HWaddr \K.*$'`|
| Directory listing with hidden files        | `ls -al`                               |
| Change file permissions                    | `chmod xxx`                            |
| Connect with ssh as a user                 | `ssh user@host`                        |
| Continue as superuser                      | `su`                                   |
| Repeat the last command                    | `!!`                                   |

## Basic Shortcuts

| Task                                       | shortkey                               |
| :---                                       | :---                                   |
| Halts the current command                  | `Ctrl+C `                              |
| Stops the current command                  | `Ctrl+z `                              |
| Pasting clipboard in bash                  | `Middle mouse button`                  |


## Vagrant commands

| Task                                               | Command             |
| :---                                               | :---                |
| Start a virtual machine                            | `vagrant up`        |
| Stops and delete all traces of the vagrant machine | `vagrant destroy`   |
| Status of the current vm's in the directory        | `vagrant status`    |
| Stop the vagrant machine                           | `vagrant halt`      |
| Provision the vagrant machine                      | `vagrant provision` |
| Restart vm with new Vagrantfile                    | `vagrant reload`    |
| Resume a suspended vagrant machine                 | `vagrant resume`    |
| Connect with the vm via ssh                        | `vagrant ssh`       |
| Suspends the machine                               | `vagrant suspend`   |  

**Note: You can also follow these commands with a specific server**

## Git workflow

Simple workflow for a personal project without other contributors:

| Task                                         | Command                   |
| :---                                         | :---                      |
| Current project status                       | `git status`              |
| Select files to be committed                 | `git add FILE...`         |
| Add all files to be committed                | `git add .`               |
| Commit changes to local repository           | `git commit -m 'MESSAGE'` |
| Push local changes to remote repository      | `git push`                |
| Pull changes from remote repository to local | `git pull`                |
| Clone repository from Github                 | `git clone repository`    |
| Abort a rebase                               | `git rebase --abort`      |


## Checklist network configuration

1. Is the IP-adress correct? `ip a`
2. Is the router/default gateway correct? `ip r -n`
3. Is a DNS-server available? `cat /etc/resolv.conf`
