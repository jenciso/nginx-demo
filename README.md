## TUTORIAL ANSIBLE 

### Provisionando 

Apague as VM's

```
./new-vm.sh -n web01
./new-vm.sh -n web02
./new-vm.sh -n web03
```

Crie as VM's denovo
```
./new-vm.sh -n web01 -m 512 -c 1 -i 192.168.122.13
./new-vm.sh -n web02 -m 512 -c 1 -i 192.168.122.14
./new-vm.sh -n web03 -m 512 -c 1 -i 192.168.122.15
``` 

O arquivo inventory já deve ter o seguinte conteúdo:

```
[web_servers]
192.168.122.13 ansible_ssh_user=centos
192.168.122.14 ansible_ssh_user=centos
192.168.122.15 ansible_ssh_user=centos
```

Verifique se o ansible está configurado e tem comunicação com esta VM

```
ansible -m ping -i inventory web_servers
```

Verifique a organização dos novos arquivos. Explore todas as pastas

```
sudo apt  install tree
tree 
``` 

Rode o playbook
```
ansible-playbook -i inventory site.yml
```

Verifique nos 3 servidores se o nginx está com o conteúdo do website

```
firefox 192.168.122.13 
firefox 192.168.122.14
firefox 192.168.122.15
```

Modifica a configuração e rode o playbook usando tags

```
ansible-playbook site.yml -i inventory -t nginx-config
```

Modifique novamente e agora só execute numa VM só

```
ansible-playbook site.yml -i inventory -t nginx-config --limit=web_servers[0]
```

Baixe manualmente o nginx de uma VM

```
ssh centos@192.168.122.13
sudo su - 
systemctl stop nginx
exit; exit
```

Rode o playbook

```
ansible-playbook site.yml -i inventory --skip-tags nginx-install
``` 

Crie um arquivo `site-quick.yml`

```
---
- name: nginx-demo | provisiona um website em nginx
  hosts: all
  gather_facts: no
  remote_user: centos
  roles:
    - nginx-website
```

Rode o playbook criado

```
ansible-playbook site-quick.yml -i inventory 
```

Compare os tempos usando o comando `time`

