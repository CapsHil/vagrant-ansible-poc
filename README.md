# Requirement

- vagrant 1.9.3
- ansible 2.2.1.0 (bug avec la 2.2.2.0)
- virtualbox 5.0

# Common commands

## Watch vagrant global status

```
$ watch -n 1 vagrant global-status
Toutes les 1,0s: vagrant global-status                                                                                                                                               Thu Apr  6 23:07:00 2017

id       name   provider   state    directory
------------------------------------------------------------------------------------------------------------
a6efcc5  e1     virtualbox poweroff /home/cyrille/Documents/kubernetes/coreos-kubernetes/multi-node/vagrant
62ccecf  c1     virtualbox poweroff /home/cyrille/Documents/kubernetes/coreos-kubernetes/multi-node/vagrant
4a31be0  w1     virtualbox poweroff /home/cyrille/Documents/kubernetes/coreos-kubernetes/multi-node/vagrant
33af9c1  node1  virtualbox running  /home/cyrille/Documents/vagrant-ansible/vagrant-swarm
0c6045b  node2  virtualbox running  /home/cyrille/Documents/vagrant-ansible/vagrant-swarm
c7c72a4  node3  virtualbox running  /home/cyrille/Documents/vagrant-ansible/vagrant-swarm
51d5a86  node4  virtualbox running  /home/cyrille/Documents/vagrant-ansible/vagrant-swarm

The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date. To interact with any of the machines, you can go to
that directory and run Vagrant, or you can use the ID directly
with Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"
```


## ansible basic commands

http://docs.ansible.com/ansible/intro_adhoc.html


# vagrant-basic

OS: ubuntu/trusty64

Objectif: Permet d'utiliser vagrant pour vérifier le bon fonctionnement.

- Démarrer la VM
```
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/trusty64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/trusty64' is up to date...
==> default: A newer version of the box 'ubuntu/trusty64' is available! You currently
==> default: have version '20170330.0.1'. The latest is version '20170404.0.0'. Run
==> default: `vagrant box update` to update.
==> default: Setting the name of the VM: vagrant-basic_default_1491512936089_65640
==> default: Clearing any previously set forwarded ports...
==> default: Fixed port collision for 22 => 2222. Now on port 2203.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2203 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2203
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 4.3.36
    default: VirtualBox Version: 5.0
==> default: Mounting shared folders...
    default: /vagrant => /home/cyrille/Documents/vagrant-ansible/vagrant-basic
```

- Vérifie l'état de la VM
```
$ vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

- Connexion à la VM
```
$ vagrant ssh
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-115-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Apr  6 21:09:17 UTC 2017

  System load:  0.21              Processes:           79
  Usage of /:   3.6% of 39.34GB   Users logged in:     0
  Memory usage: 25%               IP address for eth0: 10.0.2.15
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

New release '16.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


vagrant@vagrant-ubuntu-trusty-64:~$ ls
vagrant@vagrant-ubuntu-trusty-64:~$ exit
logout
Connection to 127.0.0.1 closed.
```

- Arrêt de la VM
```
$ vagrant halt
==> default: Attempting graceful shutdown of VM...
```

- Suppression de la VM
```
$ vagrant destroy
cyrille@cyrille-zenika:~/Documents/vagrant-ansible/vagrant-basic$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Destroying VM and associated drives...
```

# vagrant-ansible-basic

OS: centos/7

Objectif: Provisionne la VM par vagrant et gère la configuration via ansible

- Démarre la VM
```
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos/7'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'centos/7' is up to date...
...
==> default: Rsyncing folder: /home/cyrille/Documents/vagrant-ansible/vagrant-ansible-basic/ => /vagrant
==> default: Running provisioner: ansible...
    default: Running ansible-playbook...

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [default]

TASK [ensure ntpd is at the latest version] ************************************
changed: [default]

TASK [ensure ntpd is running] **************************************************
changed: [default]

TASK [installing nginx repo rpm] ***********************************************
changed: [default]

TASK [ensure nginx is at the latest version] ***********************************
changed: [default]

TASK [ensure nginx is running] *************************************************
changed: [default]

RUNNING HANDLER [restart ntpd] *************************************************
changed: [default]

RUNNING HANDLER [restart nginx] ************************************************
changed: [default]

PLAY RECAP *********************************************************************
default                    : ok=8    changed=7    unreachable=0    failed=0
```

- ansible: ping l'ensemble des machines
```
$ ansible all -m ping
default | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

- ansible: liste les interfaces via la commande "/sbin/ip a" sur la machine "default"
```
$ ansible default -m shell -a "/sbin/ip a"
default | SUCCESS | rc=0 >>
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:1f:db:b7 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 86160sec preferred_lft 86160sec
    inet6 fe80::5054:ff:fe1f:dbb7/64 scope link 
       valid_lft forever preferred_lft forever
```

- ansible: lance la commande "/bin/uname -a" sur la machine "default"
```
$ ansible default -m shell -a "uname -a"
default | SUCCESS | rc=0 >>
Linux localhost.localdomain 3.10.0-514.6.2.el7.x86_64 #1 SMP Thu Feb 23 03:04:39 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

- ansible: utilisation du user root (--become)
```
$ ansible default --become -m shell -a "systemctl status nginx"
default | SUCCESS | rc=0 >>
● nginx.service - nginx - high performance web server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since jeu. 2017-04-06 21:23:03 UTC; 5min ago
     Docs: http://nginx.org/en/docs/
  Process: 12045 ExecStop=/bin/kill -s QUIT $MAINPID (code=exited, status=0/SUCCESS)
  Process: 12049 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
  Process: 12048 ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 12051 (nginx)
   CGroup: /system.slice/nginx.service
           ├─12051 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.con
           └─12052 nginx: worker process                   

avril 06 21:23:03 localhost.localdomain systemd[1]: Starting nginx - high performance web server...
avril 06 21:23:03 localhost.localdomain nginx[12048]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
avril 06 21:23:03 localhost.localdomain nginx[12048]: nginx: configuration file /etc/nginx/nginx.conf test is successful
avril 06 21:23:03 localhost.localdomain systemd[1]: Failed to read PID from file /run/nginx.pid: Invalid argument
avril 06 21:23:03 localhost.localdomain systemd[1]: Started nginx - high performance web server.
```

```
$ ansible default --become -m shell -a "systemctl stop nginx"
default | SUCCESS | rc=0 >>
```

```
$ ansible default --become -m shell -a "systemctl start nginx"
default | SUCCESS | rc=0 >>
```

On peut changer le contenu du playbook (playbook.yml) et appliquer les changements

```
$ ansible-playbook playbook.yml
```

# vagrant-cluster-basic

OS: centos/7

Objectif: vagrant lance plusieurs VMs, configurables via ansible. vagrant génère l'inventaire pour ansible dans .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory. Le fichier ansible.cfg permet d'indiquer le fichier d'inventaire à ansible.

Note: les 2 VMs ne peuvent pas se voir entr'elles.

- Lancer les VMs
```
$ vagrant up
```

- Voir l'état des nginx (as default user)
```
$ ansible all -a "/usr/bin/systemctl status nginx"
```

- Voir l'état des nginx (as root)
```
$ ansible all --become -a "/usr/bin/systemctl status nginx"
```

# vagrant-cluster-network

OS: ubunty/xenial64

Objectif: Monter les VMs en réseau avec docker

- Lancer les VMs
```
$ vagrant up
```

- Vérifier que les docker répondent
```
$ ansible all --become -m command -a "/usr/bin/docker version"
```

- Vérifier que les VMs sont connectées au même réseau

Note: Les IPs commencent par 192.168.77.21, puis +1 par VM

```
$ ansible all -a "/bin/ping -c 4 192.168.77.21"
```

# vagrant-cluster-swarm

Objectif: monter un cluster Swarm avec 4 noeud, dont 3 sont des managers et un est un worker, afin d'assurer la haute dispo des managers

- Lancer les VMs
```
$ vagrant up
```

- Vérifier que swarm est activé
```
$ ansible all --become -m shell -a "/usr/bin/docker info 2>/dev/null | /bin/grep ^Swarm"
node3 | SUCCESS | rc=0 >>
Swarm: active

node2 | SUCCESS | rc=0 >>
Swarm: active

node1 | SUCCESS | rc=0 >>
Swarm: active

node4 | SUCCESS | rc=0 >>
Swarm: active
```

- Voir la liste des noeuds
```
$ ansible node1 --become -m command -a "/usr/bin/docker node ls"
node1 | SUCCESS | rc=0 >>
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
0w8zhurv8imzfnrl5e3w0vng4    node3     Ready   Active        Reachable
7ygu341xqp68814jxngpyyudl    node2     Ready   Active        Reachable
cplhbrf0mq8l8j4kuftz7hzwf *  node1     Ready   Active        Leader
za4h2o4wg7sj7htvo7oylce2j    node4     Ready   Active        
```
