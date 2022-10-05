---
tags:
  - BCC8
  - Install_BCC8
  - kafka
  - BCC8+kafka
  - DHCP8
---

<h1 style="text-align: center;">
Procedimento para instalar o DHCP8 nos Templates Virtua
<br>
[ BCC8 | DHCP8 Gpon | Rede Neutra ] 
</h1>
##  Instalando Java
O java 1.8 ou superio é requerido para instalação do DHCP8 + Kafka

```Shell
dnf install java-1.8.0-openjdk
```

## Firewall - Portas utilizadas
Vamos listar somete as portas utilizadas pelo DHCP8 + Kafka

| Service | Port | UDP/TCP | Description |
| --------| -----| --------| ----------- |
| DHCP | 37 | UDP |  Time Server |
| DHCP | 37 | TCP | Time Server | 
| DHCP | 67 | UDP | DHCP Server |
| DHCP | 68 | UDP | DHCP Client |
| DHCP | 80 | TCP | SOAP |
| DHCP | 161 | UDP | SNMP Agent |
| DHCP | 647 | UDP/TCP |  DHCP Failover |
| DHCP | 3798 |  TCP |  ZeroMQ® | 
| DHCP | 6998 | UDP/TCP | REST for SNMP API No(non-secure) |
| DHCP | 7998 | UDP/TCP | Secure SOAP |
| DHCP | 8998 | TCP | DHCP SOAP/XML |
| DHCP | 9998 | TCP | DHCP CORBA |

## Instalando Kafka Server
O BCC a partir da versão 8, permite que as mensagens multicast sejam substituídas por um servidor Kafka.
O BCC requer o  Apache Kafka 2.5.x ou 2.6.x como seu sistema de fila de mensagens.

## Instalando o Kafka em Cluster com High Availability

## Criando o usuário kafka de colocando uma senha
```Shell
useradd kafka -m
passwd kafka
```

## Criando os diretórios de trabalho
```Shell
mkdir /opt/kafka/ && cd /opt/kafka/
chown -R kafka:kafka /opt/kafka
```

## Download dos arquivos de instalação Kafka
	https://kafka.apache.org/downloads

```Shell
curl "https://archive.apache.org/dist/kafka/2.6.2/kafka_2.12-2.6.2.tgz" -o /opt/kafka/2.6.2.tgz
```

## Extrair os arquivos
```Shell
tar -xvzf 2.6.2.tgz --strip 1
```

## Criar o Unit Service do Kafka
```Shell
cat << EOF >> /etc/systemd/system/kafka.service
[Unit]
Requires=zookeeper.service
After=zookeeper.service
[Service]
ExecStart=/opt/kafka/bin/kafka-server-start.sh config/server.properties
User=kafka
Group=kafka
WorkingDirectory=/opt/kafka
[Install]
WantedBy=multi-user.target
EOF
```

## Criar o Unit Service do ZooKeeper
```Shell
cat << EOF >> /etc/systemd/system/zookeeper.service
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target
[Service]
ExecStart=/opt/kafka/bin/zookeeper-server-start.sh config/zookeeper.properties
User=kafka
Group=kafka
WorkingDirectory=/opt/kafka
[Install]
WantedBy=multi-user.target
EOF
```
## Acertando as permissões
```Shell
chmod 644 /etc/systemd/system/kafka.service
chmod 644 /etc/systemd/system/zookeeper.service
```
## Kafka Logs
```Shell
mkdir /opt/kafka/logs/ && chown kafka.wheel /opt/kafka/logs && chmod 0755 /opt/kafka/logs
sed -i 's/^log.dirs=.*/log.dirs=\/opt\/kafka\/logs/g' /opt/kafka/config/server.properties
```
## Zookeeper data directory
```Shell
mkdir /opt/kafka/data-zookeeper && chown kafka.wheel /opt/kafka/data-zookeeper && chmod 0755 /opt/kafka/data-zookeeper
sed -i 's/^dataDir=.*/dataDir=\/opt\/kafka\/data-zookeeper/g' /opt/kafka/config/zookeeper.properties
```
## Instalar o nmap-ncat [Opcional]
```Shell
yum install nmap-ncat
```
## Configurando o Kafka e Zookeeper
ainda em criação!!

# Instalando o DHCP8

## Adicionando o Repositório da Incognito
```Shell
rpm -ivh http://repository.incognito.com/rpms/ga/incognito/8/noarch/incognitoPublic-8.0.1-1.el8.noarch.rpm
```
## Adicionando o Repositório do EPEL
```Shell
rpm -Uvh http://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
## Instalar  BCC Repositorio do DHCP8
```Shell
yum install incognitoDHCP8
```
## Adicionando a licença KEY do Incognito nos repositório
Subistituir [ 00X-IPC100-0000-0000-0000-0000 ] por [ 08X-IPC100-A2CB-BD66-F932-B0D4 ]
```Shell
vi /etc/yum.repos.d/incognitoDHCP8.repo
```
## Instalar o Serviço DHCP
```SHell
yum install DHCPSvc -y
```
## Instalar o JIMC.
```Shell
yum install incognito-ui-java-management-console8 -y
```
## Iniciando os serviços
```Shell
/etc/init.d/ipcmdrd.safe start
```
## Iniciar o JIMC.
```Shell
systemctl start incognito-ui-java-management-console8
```
## Instalar o DHCPcli
```Shell
yum install DHCPCLI -y
```
## Interface Java
	http://<bcc-service-hostname>:8080/jimc

# Configuração Kafka 
Configuração do kafka como servidor de mensagens dentro da interface Java, substituindo o grupo multcast

Em criação
