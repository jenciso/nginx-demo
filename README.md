## TUTORIAL ANSIBLE 

### Preparando o Ambiente

Usando o repositorio [kvm-provision](https://github.com/jenciso/kvm-provision), crie uma VM:

```
./new-vm.sh -n web01 -m 512 -c 1 -i 192.168.122.13
```

Logo provisione crie um arquivo `inventory` com o seguinte conteúdo:

```
[web_servers]
192.168.122.13  ansible_ssh_user=centos
```

Verifique se o ansible está configurado e tem comunicação com esta VM

```
ansible -m ping -i inventory web_servers
ansible -m ping -i inventory web_servers[0]
```

## INSTALL NGINX

Instale o pacote `nginx` via ansible. Para aquilo crie um arquivo `nginx-install.yml` com seguinte conteúdo

```yaml
---
- hosts: all
  tasks:
    - name: Install epel-release
      yum: name=epel-release state=present

    - name: ensure nginx is at the latest version
      yum: name=nginx state=latest

    - name: start nginx
      service:
          name: nginx
          state: started

```

Agora rode o playbook criado 

```
ansible-playbook -i inventory nginx-install.yml 
```

Vuelva rodar o playbook agora sendo mais verbose
```
ansible-playbook -i inventory nginx-install.yml -v
```

Alem disso você pode observar o que está fazendo o ansible acessando ao servidor web01
```
ssh centos@192.168.122.13
sudo su - 
tail -f /var/log/messages
```

Novamente: 
```
ansible-playbook -i inventory nginx-install.yml -vv
```

Verificar se o webserver esta disponivel

``` 
firefox http://192.168.122.13
```

Ou simpleslmente com o curl 

```
curl http://192.168.122.13
```

Brinque com o curl 
``` 
curl -vs http://192.168.122.13 -o /dev/null
curl -m 10 -s -w "http_status: %{http_code} | time: %{time_total}\n" http://192.168.122.13 -o /dev/null
while true; do curl -m 10 -s -w "http_status: %{http_code} | time: %{time_total}\n" http://192.168.122.13 -o /dev/null; done
```

### UNINSTALL NGINX

Criamos outro playbook com o nome: `nginx-uninstall` e com o seguinte conteúdo

```
---
- hosts: all
  tasks:

    - name: stop nginx
      service:
          name: nginx
          state: stopped

    - name: ensure nginx is not installed
      yum: name=nginx state=absent
```

Rodamos o playbook 
```
ansible-playbook -i inventory nginx-uninstall.yml
``` 

