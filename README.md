## TUTORIAL ANSIBLE 

### Scale

Adicione mais um servidor

```
./new-vm.sh -n web03 -m 512 -c 1 -i 192.168.122.14
```

Modifique o arquivo inventory com o seguinte conteúdo:

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

