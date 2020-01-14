## TUTORIAL ANSIBLE 

### Scale UP

Usando o repositorio [kvm-provision](https://github.com/jenciso/kvm-provision), adicione 1 VMs:

```shell
./new-vm.sh -n web02 -m 512 -c 1 -i 192.168.122.14
```

Modifique o arquivo `inventory` com o seguinte conteúdo:

```
[web_servers]
192.168.122.13 ansible_ssh_user=centos
192.168.122.14 ansible_ssh_user=centos
```

Verifique se o ansible está configurado e tem comunicação com esta VM

```shell
ansible -m ping -i inventory web_servers
```

## INSTALL NGINX

Agora rode o playbook criado anteriormente

```shell
ansible-playbook -i inventory nginx-install.yml 
```
