# Requirement

- vagrant 1.9.3
- ansible 2.2.1.0 (bug avec la 2.2.2.0)
- virtualbox 5.0

# Common commands

## Watch vagrant global status

```
watch -n 1 vagrant global-status
```


## ansible basic commands

http://docs.ansible.com/ansible/intro_adhoc.html


# vagrant-basic

OS: ubuntu/trusty64

Objectif: Permet d'utiliser vagrant pour vérifier le bon fonctionnement.

- Démarrer la VM
```
vagrant up
```

- Vérifie l'état de la VM
```
vagrant status
```

- Connexion à la VM
```
vagrant ssh
```

- Arrêt de la VM
```
vagrant halt
```

- Suppression de la VM
```
vagrant destroy
```

# vagrant-ansible-basic

OS: centos/7

Objectif: Provisionne la VM par vagrant et gère la configuration via ansible

- Démarre la VM
```
vagrant up
```

- ansible: ping l'ensemble des machines
```
ansible all -m ping
```

- ansible: lance la commande "/sbin/ip a" sur la machine "default"
```
ansible default -a "/sbin/ip a"
```

- ansible: lance la commande "/bin/uname -a" sur la machine "default"
```
ansible default -a "/bin/uname -a"
```

- ansible: utilisation du user root (--become)
```
ansible default --become -a "/usr/bin/systemctl status nginx"
```

```
ansible default --become -a "/usr/bin/systemctl stop nginx"
```

```
ansible default --become -a "/usr/bin/systemctl start nginx"
```

On peut changer le contenu du playbook (playbook.yml) et appliquer les changements

```
ansible-playbook playbook.yml
```

# vagrant-cluster-basic

OS: centos/7

Objectif: vagrant lance plusieurs VMs, configurables via ansible. vagrant génère l'inventaire pour ansible dans .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory. Le fichier ansible.cfg permet d'indiquer le fichier d'inventaire à ansible.

Note: les 2 VMs ne peuvent pas se voir entr'elles.

- Lancer les VMs
```
vagrant up
```

- Voir l'état des nginx (as default user)
```
ansible all -a "/usr/bin/systemctl status nginx"
```

- Voir l'état des nginx (as root)
```
ansible all --become -a "/usr/bin/systemctl status nginx"
```

# vagrant-cluster-network

OS: ubunty/xenial64

Objectif: Monter les VMs en réseau avec docker

- Lancer les VMs
```
vagrant up
```

- Vérifier que les docker répondent
```
ansible all --become -m command -a "/usr/bin/docker version"
```

- Vérifier que les VMs sont connectées au même réseau

Note: Les IPs commencent par 192.168.77.21, puis +1 par VM

```
ansible all -a "/bin/ping -c 4 192.168.77.21"
```

# vagrant-cluster-swarm

- Lancer les VMs
```
vagrant up
```

- Vérifier que swarm est activé
```
cyrille@cyrille-zenika:~/Documents/vagrant-ansible/vagrant-swarm$ ansible all --become -m shell -a "/usr/bin/docker info 2>/dev/null | /bin/grep ^Swarm"
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
cyrille@cyrille-zenika:~/Documents/vagrant-ansible/vagrant-swarm$ ansible node1 --become -m shell -a "/usr/bin/docker node ls"
node1 | SUCCESS | rc=0 >>
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
eungc6xajucaacwr8ogawuu1c    node3     Ready   Active        
ijwjoia01y9dnfw87r7015xns    node4     Ready   Active        
oj6lp8578toa65g11n1oqlsnd *  node1     Ready   Active        Leader
qcnonkzmr69t8xizcetaw4dyc    node2     Ready   Active        
```
