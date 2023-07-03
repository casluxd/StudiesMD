`Nmap` nos dá muitas maneiras diferentes de contornar as regras de firewalls e IDS/IPS. Esses métodos incluem a fragmentação de pacotes, o uso de chamarizes e outros que discutiremos nesta seção.

# Firewalls

Um firewall é uma medida de segurança contra tentativas de conexão não autorizadas de redes externas. Todo sistema de segurança de firewall é baseado em um componente de software que monitora o tráfego de rede entre o firewall e as conexões de dados de entrada e decide como lidar com a conexão com base nas regras definidas. Ele verifica se os pacotes de rede individuais estão sendo transmitidos, ignorados ou bloqueados. Esse mecanismo foi projetado para evitar conexões indesejadas que podem ser potencialmente perigosas.

# IDS/IPS

Assim como o firewall, o sistema de detecção de intrusão ( `IDS`) e o sistema de prevenção de intrusão ( `IPS`) também são componentes baseados em software. `IDS` verifica a rede em busca de ataques em potencial, analisa-os e relata quaisquer ataques detectados. `IPS` complementa `IDS` tomando medidas defensivas específicas se um ataque potencial deveria ter sido detectado. A análise de tais ataques é baseada em correspondência de padrões e assinaturas. Se padrões específicos forem detectados, como uma varredura de detecção de serviço, `IPS` pode impedir as tentativas de conexão pendentes.

#### Determinar firewalls e suas regras

Já sabemos que quando uma porta é mostrada como filtrada, isso pode ter vários motivos. Na maioria dos casos, os firewalls têm determinadas regras definidas para lidar com conexões específicas. Os pacotes podem ser `dropped`, ou `rejected`. Os pacotes `dropped` são ignorados e nenhuma resposta é retornada do host.

Isso é diferente para pacotes `rejected` que são retornados com um sinalizador `RST`. Esses pacotes contêm diferentes tipos de códigos de erro ICMP ou não contêm nada.

Tais erros podem ser:
- Net Unreachable
- Net Prohibited
- Host Unreachable
- Host Prohibited
- Port Unreachable
- Proto Unreachable

O método TCP ACK scan do Nmap `-sA` é muito mais difícil de filtrar para firewalls e sistemas IDS/IPS do que SYN ( `-sS`) ou Connect scans ( `sT`) normais porque eles enviam apenas um pacote TCP com apenas o sinalizador `ACK`. Quando uma porta é fechada ou aberta, o host deve responder com um sinalizador `RST`. Ao contrário das conexões de saída, todas as tentativas de conexão (com o sinalizador `SYN`) de redes externas geralmente são bloqueadas por firewalls. No entanto, os pacotes com o sinalizador `ACK` geralmente são passados ​​pelo firewall porque o firewall não pode determinar se a conexão foi estabelecida primeiro na rede externa ou na rede interna.

Se olharmos para essas varreduras, veremos como os resultados diferem.

#### SYN-Scan

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:56 CEST
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:22 S ttl=53 id=22412 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:25 S ttl=50 id=62291 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:21 S ttl=58 id=38696 iplen=44  seq=4092255222 win=1024 <mss 1460>
RCVD (0.0329s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=40884 iplen=72 ]
RCVD (0.0341s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
RCVD (1.0386s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
SENT (1.1366s) TCP 10.10.14.2:57348 > 10.129.2.28:25 S ttl=44 id=6796 iplen=44  seq=4092320759 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.0053s latency).

PORT   STATE    SERVICE
21/tcp filtered ftp
22/tcp open     ssh
25/tcp filtered smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

#### ACK-Scan

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:57 CEST
SENT (0.0422s) TCP 10.10.14.2:49343 > 10.129.2.28:21 A ttl=49 id=12381 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:22 A ttl=41 id=5146 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:25 A ttl=49 id=5800 iplen=40  seq=0 win=1024
RCVD (0.1252s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=55628 iplen=68 ]
RCVD (0.1268s) TCP 10.129.2.28:22 > 10.10.14.2:49343 R ttl=64 id=0 iplen=40  seq=1660784500 win=0
SENT (1.3837s) TCP 10.10.14.2:49344 > 10.129.2.28:25 A ttl=59 id=21915 iplen=40  seq=0 win=1024
Nmap scan report for 10.129.2.28
Host is up (0.083s latency).

PORT   STATE      SERVICE
21/tcp filtered   ftp
22/tcp unfiltered ssh
25/tcp filtered   smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 21,22,25`|Verifica apenas as portas especificadas.|
|`-sS`|Executa varredura SYN em portas especificadas.|
|`-sA`|Executa varredura ACK em portas especificadas.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|

Por favor, preste atenção aos pacotes RCVD e seu sinalizador definido que recebemos de nosso alvo. Com o scan SYN ( `-sS`) nosso alvo tenta estabelecer a conexão TCP enviando um pacote de volta com os sinalizadores `SA` SYN-ACK  definidos e com o scan ACK ( `-sA`) obtemos o sinalizador `RST` porque a porta TCP 22 está aberta. Para a porta TCP 25, não recebemos nenhum pacote de volta, o que indica que os pacotes serão descartados.

## Detectar IDS/IPS

Ao contrário dos firewalls e suas regras, a detecção de sistemas IDS/IPS é muito mais difícil porque são sistemas passivos de monitoramento de tráfego. `IDS systems` examine todas as conexões entre os hosts. Se o IDS encontrar pacotes contendo os conteúdos ou especificações definidos, o administrador é notificado e toma as medidas cabíveis no pior caso.

`IPS systems` tomam medidas configuradas pelo administrador de forma independente para prevenir ataques potenciais automaticamente. É fundamental saber que IDS e IPS são aplicações diferentes e que o IPS serve como complemento do IDS.

Vários servidores privados virtuais ( `VPS`) com diferentes endereços IP são recomendados para determinar se tais sistemas estão na rede de destino durante um teste de penetração. Se o administrador detectar tal ataque potencial na rede de destino, o primeiro passo é bloquear o endereço IP de onde vem o ataque potencial. Como resultado, não poderemos mais acessar a rede usando esse endereço IP, e nosso provedor de serviços de Internet ( `ISP`) será contatado e bloqueado de todo acesso à Internet.

- `IDS systems` sozinhos geralmente estão lá para ajudar os administradores a detectar possíveis ataques em sua rede. Eles podem então decidir como lidar com essas conexões. Podemos acionar certas medidas de segurança de um administrador, por exemplo, verificando agressivamente uma única porta e seu serviço. Com base nas medidas de segurança específicas tomadas, podemos detectar se a rede possui alguns aplicativos de monitoramento ou não.
- Um método para determinar se um `IPS system` está presente na rede de destino é digitalizar a partir de um único host ( `VPS`). Se a qualquer momento este host for bloqueado e não tiver acesso à rede de destino, sabemos que o administrador tomou algumas medidas de segurança. Assim, podemos continuar nosso teste de penetração com outro `VPS`.

Consequentemente, sabemos que precisamos ser mais silenciosos com nossas varreduras e, na melhor das hipóteses, disfarçar todas as interações com a rede de destino e seus serviços.

## Decoy

Há casos em que os administradores bloqueiam sub-redes específicas de diferentes regiões em princípio. Isso impede qualquer acesso à rede de destino. Outro exemplo é quando o IPS deve nos bloquear. Por esta razão, o método de digitalização Decoy ( `-D`) é a escolha certa. Com este método, o Nmap gera vários endereços IP aleatórios inseridos no cabeçalho IP para disfarçar a origem do pacote enviado. Com este método, podemos gerar aleatoriamente ( `RND`) um número específico (por exemplo: `5`) de endereços IP separados por dois pontos (`:`). Nosso endereço IP real é colocado aleatoriamente entre os endereços IP gerados. No próximo exemplo, nosso endereço IP real é, portanto, colocado na segunda posição. Outro ponto crítico é que os decoys devem estar vivos. Caso contrário, o serviço no destino pode ficar inacessível devido a mecanismos de segurança de inundação de SYN.

#### Escanear usando decoys

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 16:14 CEST
SENT (0.0378s) TCP 102.52.161.59:59289 > 10.129.2.28:80 S ttl=42 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0378s) TCP 10.10.14.2:59289 > 10.129.2.28:80 S ttl=59 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 210.120.38.29:59289 > 10.129.2.28:80 S ttl=37 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 191.6.64.171:59289 > 10.129.2.28:80 S ttl=38 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 184.178.194.209:59289 > 10.129.2.28:80 S ttl=39 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 43.21.121.33:59289 > 10.129.2.28:80 S ttl=55 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
RCVD (0.1370s) TCP 10.129.2.28:80 > 10.10.14.2:59289 SA ttl=64 id=0 iplen=44  seq=4056111701 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.099s latency).

PORT   STATE SERVICE
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 80`|Verifica apenas as portas especificadas.|
|`-sS`|Executa varredura SYN em portas especificadas.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-D RND:5`|Gera cinco endereços IP aleatórios que indicam o IP de origem da conexão.|

Os pacotes falsificados geralmente são filtrados por ISPs e roteadores, mesmo que venham do mesmo intervalo de rede. Portanto, também podemos especificar os endereços IP de nossos servidores VPS e usá-los em combinação com a manipulação" `IP ID`" nos cabeçalhos IP para escanear o alvo.

Outro cenário seria que apenas sub-redes individuais não teriam acesso aos serviços específicos do servidor. Portanto, também podemos especificar manualmente o endereço IP de origem ( `-S`) para testar se obtemos melhores resultados com este. Decoys podem ser usados ​​para varreduras SYN, ACK, ICMP e varreduras de detecção de sistema operacional. Portanto, vamos examinar esse exemplo e determinar qual sistema operacional é mais provável.

#### Testando regra de firewall

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p445 -O

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:23 CEST
Nmap scan report for 10.129.2.28
Host is up (0.032s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.14 seconds
```

#### Digitalizar usando IP de origem diferente

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:16 CEST
Nmap scan report for 10.129.2.28
Host is up (0.010s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Linux 2.6.32 - 2.6.35 (94%), Linux 2.6.32 - 3.5 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.11 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-n`|Desativa a resolução de DNS.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-p 445`|Verifica apenas as portas especificadas.|
|`-O`|Executa varredura de detecção do sistema operacional.|
|`-S`|Varre o destino usando um endereço IP de origem diferente.|
|`10.129.2.200`|Especifica o endereço IP de origem.|
|`-e tun0`|Envia todas as solicitações por meio da interface especificada.|

## Proxy DNS

Por padrão, `Nmap` executa uma resolução de DNS reversa, a menos que especificado de outra forma, para encontrar informações mais importantes sobre nosso alvo. Essas consultas de DNS também são transmitidas na maioria dos casos porque o servidor da Web fornecido deve ser encontrado e visitado. As consultas de DNS são feitas sobre `UDP port 53`. Anteriormente, o `TCP port 53` era usado apenas para os chamados " `Zone transfers`" entre os servidores DNS ou transferência de dados maiores que 512 bytes. Cada vez mais, isso está mudando devido às expansões do IPv6 e do DNSSEC. Essas alterações fazem com que muitas solicitações de DNS sejam feitas por meio da porta TCP 53.

No entanto, `Nmap`a inda nos dá uma maneira de especificar os próprios servidores DNS ( `--dns-server <ns>,<ns>`). Esse método pode ser fundamental para nós se estivermos em uma zona desmilitarizada ( `DMZ`). Os servidores DNS da empresa costumam ser mais confiáveis ​​do que os da Internet. Assim, por exemplo, poderíamos usá-los para interagir com os hosts da rede interna. Como outro exemplo, podemos usar `TCP port 53` como fonte a porta ( `--source-port`) para nossas varreduras. Se o administrador usar o firewall para controlar esta porta e não filtrar IDS/IPS adequadamente, nossos pacotes TCP serão confiáveis ​​e repassados.

#### SYN-Scan de uma porta filtrada

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 22:50 CEST
SENT (0.0417s) TCP 10.10.14.2:33436 > 10.129.2.28:50000 S ttl=41 id=21939 iplen=44  seq=736533153 win=1024 <mss 1460>
SENT (1.0481s) TCP 10.10.14.2:33437 > 10.129.2.28:50000 S ttl=46 id=6446 iplen=44  seq=736598688 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT      STATE    SERVICE
50000/tcp filtered ibm-db2

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

#### SYN-Scan da porta DNS

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53

SENT (0.0482s) TCP 10.10.14.2:53 > 10.129.2.28:50000 S ttl=58 id=27470 iplen=44  seq=4003923435 win=1024 <mss 1460>
RCVD (0.0608s) TCP 10.129.2.28:50000 > 10.10.14.2:53 SA ttl=64 id=0 iplen=44  seq=540635485 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).

PORT      STATE SERVICE
50000/tcp open  ibm-db2
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 50000`|Verifica apenas as portas especificadas.|
|`-sS`|Executa varredura SYN em portas especificadas.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`--source-port 53`|Executa as varreduras da porta de origem especificada.|

Agora que descobrimos que o firewall aceita `TCP port 53`, é muito provável que os filtros IDS/IPS também possam estar configurados muito mais fracos do que outros. Podemos testar isso tentando conectar a esta porta usando `Netcat`.

#### Conecte à porta filtrada

```shell-session
casluxd@htb[/htb]$ ncat -nv --source-port 53 10.129.2.28 50000

Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.129.2.28:50000.
220 ProFTPd
```

# rpcclient

  
O rpcclient é uma ferramenta de linha de comando que faz parte da suíte Samba. Ele é usado para interagir com serviços e servidores remotos que utilizam o Remote Procedure Call (RPC) como protocolo de comunicação.

O RPC é um protocolo amplamente utilizado em redes para permitir a comunicação e a chamada de procedimentos entre processos em diferentes sistemas. O rpcclient permite que você se conecte a um servidor RPC e execute uma variedade de operações, como consultas, chamadas de procedimentos remotos, testes de conectividade e gerenciamento de serviços.

Aqui estão alguns casos de uso comuns do rpcclient:

1. Teste de conectividade: O rpcclient pode ser usado para testar a conectividade com um servidor RPC específico. Isso pode ser útil para verificar se um servidor está acessível e se as portas necessárias estão abertas.
2. Enumeração de serviços: O rpcclient permite listar os serviços disponíveis em um servidor RPC. Isso pode ser útil para descobrir quais serviços estão sendo executados em um sistema remoto.
3. Consulta e gerenciamento de informações do sistema: Com o rpcclient, você pode obter informações sobre o sistema remoto, como detalhes do sistema operacional, usuários, grupos, políticas de segurança, permissões de compartilhamento, configurações do Registro do Windows, entre outros.
4. Execução remota de comandos: O rpcclient permite executar comandos ou procedimentos em um sistema remoto usando a funcionalidade de chamada de procedimento remoto. Isso pode ser útil para realizar tarefas de gerenciamento e administração em sistemas remotos.

Em resumo, o rpcclient é uma ferramenta útil para interagir com servidores que utilizam o protocolo RPC. Ele fornece recursos de teste de conectividade, consulta e gerenciamento de informações do sistema, além de permitir a execução remota de comandos. O rpcclient é amplamente utilizado em ambientes que empregam o protocolo RPC, como redes Windows e sistemas baseados em Samba.
