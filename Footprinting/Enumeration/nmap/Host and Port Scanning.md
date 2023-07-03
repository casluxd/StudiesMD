É essencial entender como funciona a ferramenta que usamos e como ela executa e processa as diferentes funções. Só entenderemos os resultados se soubermos o que significam e como são obtidos. Portanto, vamos dar uma olhada e analisar alguns dos métodos de digitalização. Depois de descobrirmos que nosso alvo está vivo, queremos obter uma imagem mais precisa do sistema. As informações de que precisamos incluem:
- Portas abertas e seus serviços
- Versões de serviço
- Informações de que os serviços prestados
- Sistema operacional

Há um total de 6 estados diferentes para uma porta escaneada que podemos obter:
- open: Isso indica que a conexão com a porta digitalizada foi estabelecida. Essas conexões podem ser conexões TCP, datagramas UDP, bem como associações SCTP.
- closed: Quando a porta é mostrada como fechada, o protocolo TCP indica que o pacote que recebemos de volta contém um sinalizador `RST`. Este método de varredura também pode ser usado para determinar se nosso alvo está vivo ou não.
- filtered: O Nmap não pode identificar corretamente se a porta escaneada está aberta ou fechada porque nenhuma resposta é retornada do destino para a porta ou recebemos um código de erro do destino.
- unfiltered: Este estado de uma porta ocorre apenas durante a varredura **TCP-ACK** e significa que a porta está acessível, mas não pode ser determinado se está aberta ou fechada.
- open|filtered: Se não obtivermos uma resposta para uma porta específica, `Nmap`irá configurá-la para esse estado. Isso indica que um firewall ou filtro de pacotes pode proteger a porta.
- closed|filtered: Esse estado ocorre apenas nas varreduras **de IP ID ociosas** e indica que foi impossível determinar se a porta escaneada está fechada ou filtrada por um firewall.

## Descobrindo portas TCP abertas

Por padrão, `Nmap` verifica as 1.000 principais portas TCP com a verificação SYN ( `-sS`). Essa varredura SYN é definida apenas como padrão quando a executamos como root devido às permissões de soquete necessárias para criar pacotes TCP brutos. Caso contrário, a varredura TCP ( `-sT`) é executada por padrão. Isso significa que, se não definirmos portas e métodos de varredura, esses parâmetros serão definidos automaticamente. Podemos definir as portas uma a uma ( `-p 22,25,80,139,445`), por intervalo ( `-p 22-445`), pelas principais portas ( `--top-ports=10`) do banco de dados `Nmap` que foram assinadas como mais frequentes, verificando todas as portas ( `-p-`), mas também definindo uma verificação rápida de portas, que contém as principais 100 portas ( `-F`).

#### Verificando as 10 principais portas TCP

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 --top-ports=10 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`--top-ports=10`|Varre as portas principais especificadas que foram definidas como mais frequentes.|

Vemos que verificamos apenas as 10 principais portas TCP de nosso destino e `Nmap` exibimos seu estado de acordo. Se rastrearmos os pacotes `Nmap` enviados, veremos o sinalizador `RST` no `TCP port 21` qual nosso destino nos envia de volta. Para ter uma visão clara da varredura SYN, desabilitamos as solicitações de eco ICMP ( `-Pn`), resolução de DNS ( `-n`) e varredura de ping ARP ( `--disable-arp-ping`).

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:39 CEST
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44  seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40  seq=0 win=0
Nmap scan report for 10.11.1.28
Host is up (0.014s latency).

PORT   STATE  SERVICE
21/tcp closed ftp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 21`|Varre apenas a porta especificada.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|

Podemos ver na linha SENT que nós ( `10.10.14.2`) enviamos um pacote TCP com o sinalizador `SYN` ( `S`) para nosso alvo ( `10.129.2.28`). Na próxima linha RCVD, podemos ver que o alvo responde com um pacote TCP contendo os sinalizadores `RST` e `ACK`( `RA`). Sinalizadores `RST` e `ACK` são usados ​​para confirmar o recebimento do pacote TCP ( `ACK`) e para encerrar a sessão TCP ( `RST`).

|**Mensagem**|**Descrição**|
|---|---|
|`SENT (0.0429s)`|Indica a operação SENT do Nmap, que envia um pacote para o destino.|
|`TCP`|Mostra o protocolo que está sendo usado para interagir com a porta de destino.|
|`10.10.14.2:63090 >`|Representa nosso endereço IPv4 e a porta de origem, que será utilizada pelo Nmap para enviar os pacotes.|
|`10.129.2.28:21`|Mostra o endereço IPv4 de destino e a porta de destino.|
|`S`|Flag SYN do pacote TCP enviado.|
|`ttl=56 id=57322 iplen=44 seq=1699105818 win=1024 mss 1460`|Parâmetros de cabeçalho TCP adicionais.|

#### Resposta

|**Mensagem**|**Descrição**|
|---|---|
|`RCVD (0.0573s)`|Indica um pacote recebido do destino.|
|`TCP`|Mostra o protocolo que está sendo usado.|
|`10.129.2.28:21 >`|Representa o endereço IPv4 de destino e a porta de origem, que será usada para responder.|
|`10.10.14.2:63090`|Mostra nosso endereço IPv4 e a porta que será respondida.|
|`RA`|Flags RST e ACK do pacote TCP enviado.|
|`ttl=64 id=0 iplen=40 seq=0 win=0`|Parâmetros de cabeçalho TCP adicionais.|

O Nmap [TCP Connect Scan](https://nmap.org/book/scan-methods-connect-scan.html) ( `-sT`) usa o handshake de três vias TCP para determinar se uma porta específica em um host de destino está aberta ou fechada. A varredura envia um pacote `SYN` para a porta de destino e aguarda uma resposta. É considerado aberto se a porta de destino responder com um pacote `SYN-ACK` e fechado se responder com um pacote `RST`.

A varredura `Connect` é útil porque é a maneira mais precisa de determinar o estado de uma porta e também é a mais furtiva. Ao contrário de outros tipos de varredura, como a varredura SYN, a varredura Connect não deixa nenhuma conexão inacabada ou pacotes não enviados no host de destino, o que torna menos provável que seja detectado por sistemas de detecção de intrusão (IDS) ou sistemas de prevenção de intrusão (IPS). ). É útil quando queremos mapear a rede e não queremos perturbar os serviços executados por trás dela, causando assim um impacto mínimo e às vezes considerado um método de varredura mais educado.

Também é útil quando o host de destino possui um firewall pessoal que elimina os pacotes de entrada, mas permite a saída de pacotes. Nesse caso, uma verificação de conexão pode ignorar o firewall e determinar com precisão o estado das portas de destino. No entanto, é importante observar que o escaneamento Connect é mais lento do que outros tipos de escaneamento porque exige que o escaneador espere por uma resposta do destino após cada pacote enviado, o que pode levar algum tempo se o alvo estiver ocupado ou não responder.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:26 CET
CONN (0.0385s) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (0.0396s) TCP localhost > 10.129.2.28:443 => Connected
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds
```

## Portas filtradas

Quando uma porta é mostrada como filtrada, pode haver vários motivos. Na maioria dos casos, os firewalls têm determinadas regras definidas para lidar com conexões específicas. Os pacotes podem ser `dropped`, ou `rejected`. Quando um pacote é descartado, `Nmap` não recebe resposta do nosso destino e, por padrão, a taxa de repetição ( `--max-retries`) é definida como 1. Isso significa que `Nmap`reenviará a solicitação à porta de destino para determinar se o pacote anterior não foi maltratado acidentalmente.

Vejamos um exemplo onde o firewall `drops` envia os pacotes TCP que enviamos para a varredura de portas. Portanto, verificamos a porta TCP **139** , que já foi mostrada como filtrada. Para poder rastrear como nossos pacotes enviados são tratados, desativamos as solicitações de eco ICMP ( `-Pn`), resolução de DNS ( `-n`) e varredura de ping ARP ( `--disable-arp-ping`) novamente.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -Pn

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:45 CEST
SENT (0.0381s) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ttl=47 id=14523 iplen=44  seq=4175236769 win=1024 <mss 1460>
SENT (1.0411s) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ttl=45 id=7372 iplen=44  seq=4175171232 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT    STATE    SERVICE
139/tcp filtered netbios-ssn
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 139`|Varre apenas a porta especificada.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`-Pn`|Desativa as solicitações de eco ICMP.|

Vemos na última varredura que `Nmap` enviou dois pacotes TCP com o sinalizador SYN. Pela duração ( `2.06s`) da varredura, podemos reconhecer que ela demorou muito mais que as anteriores ( `~0.05s`). O caso é diferente se o firewall rejeitar os pacotes. Para isso, olhamos para a porta TCP `445`, que é tratada de acordo com tal regra do firewall.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -Pn

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:55 CEST
SENT (0.0388s) TCP 10.129.2.28:52472 > 10.129.2.28:445 S ttl=49 id=21763 iplen=44  seq=1418633433 win=1024 <mss 1460>
RCVD (0.0487s) ICMP [10.129.2.28 > 10.129.2.28 Port 445 unreachable (type=3/code=3) ] IP [ttl=64 id=20998 iplen=72 ]
Nmap scan report for 10.129.2.28
Host is up (0.0099s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p 445`|Varre apenas a porta especificada.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`-Pn`|Desativa as solicitações de eco ICMP.|

Como resposta, recebemos uma resposta `ICMP` com `type 3` e `error code 3`, que indica que o host desejado está inacessível. No entanto, se soubermos que o host está ativo, podemos assumir fortemente que o firewall nesta porta está rejeitando os pacotes, e teremos que dar uma olhada mais de perto nesta porta mais tarde.

## Descobrindo portas UDP abertas

Alguns administradores de sistema às vezes se esquecem de filtrar as portas UDP além das portas TCP. Uma vez que `UDP` é um `stateless protocol` e não requer um handshake de três vias como o TCP. Não recebemos nenhum reconhecimento. Consequentemente, o timeout é muito maior, tornando o inteiro `UDP scan`( `-sU`) muito mais lento que o `TCP scan`( `-sS`).

Vejamos um exemplo de como `-sU` pode ser uma varredura UDP e quais resultados ela nos dá.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -F -sU

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-F`|Escaneia as 100 principais portas.|
|`-sU`|Executa uma varredura UDP.|

Outra desvantagem disso é que muitas vezes não recebemos uma resposta porque `Nmap` envia datagramas vazios para as portas UDP verificadas e não recebemos nenhuma resposta. Portanto, não podemos determinar se o pacote UDP chegou ou não. Se a porta UDP for `open`, só obteremos uma resposta se o aplicativo estiver configurado para isso.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:15 CEST
SENT (0.0367s) UDP 10.10.14.2:55478 > 10.129.2.28:137 ttl=57 id=9122 iplen=78
RCVD (0.0398s) UDP 10.129.2.28:137 > 10.10.14.2:55478 ttl=64 id=13222 iplen=257
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.0031s latency).

PORT    STATE SERVICE    REASON
137/udp open  netbios-ns udp-response ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-sU`|Executa uma varredura UDP.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-p 137`|Varre apenas a porta especificada.|
|`--reason`|Exibe o motivo pelo qual uma porta está em um determinado estado.|

Se obtivermos uma resposta ICMP com `error code 3`(porta inacessível), saberemos que a porta é de fato `closed`.

```shell-session
asluxd@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:25 CEST
SENT (0.0445s) UDP 10.10.14.2:63825 > 10.129.2.28:100 ttl=57 id=29925 iplen=28
RCVD (0.1498s) ICMP [10.129.2.28 > 10.10.14.2 Port unreachable (type=3/code=3) ] IP [ttl=64 id=11903 iplen=56 ]
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.11s latency).

PORT    STATE  SERVICE REASON
100/udp closed unknown port-unreach ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in  0.15 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-sU`|Executa uma varredura UDP.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-p 100`|Varre apenas a porta especificada.|
|`--reason`|Exibe o motivo pelo qual uma porta está em um determinado estado.|

Para todas as outras respostas ICMP, as portas verificadas são marcadas como ( `open|filtered`).

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 138 --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:32 CEST
SENT (0.0380s) UDP 10.10.14.2:52341 > 10.129.2.28:138 ttl=50 id=65159 iplen=28
SENT (1.0392s) UDP 10.10.14.2:52342 > 10.129.2.28:138 ttl=40 id=24444 iplen=28
Nmap scan report for 10.129.2.28
Host is up, received user-set.

PORT    STATE         SERVICE     REASON
138/udp open|filtered netbios-dgm no-response
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-sU`|Executa uma varredura UDP.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-p 138`|Varre apenas a porta especificada.|
|`--reason`|Exibe o motivo pelo qual uma porta está em um determinado estado.|

Outro método útil para escanear portas é a opção `-sV` usada para obter informações adicionais disponíveis das portas abertas. Esse método pode identificar versões, nomes de serviço e detalhes sobre nosso destino.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
SENT (0.3426s) TCP 10.10.14.2:44641 > 10.129.2.28:445 S ttl=55 id=43401 iplen=44  seq=3589068008 win=1024 <mss 1460>
RCVD (0.3556s) TCP 10.129.2.28:445 > 10.10.14.2:44641 SA ttl=63 id=0 iplen=44  seq=2881527699 win=29200 <mss 1337>
NSOCK INFO [0.4980s] nsock_iod_new2(): nsock_iod_new (IOD #1)
NSOCK INFO [0.4980s] nsock_connect_tcp(): TCP connection requested to 10.129.2.28:445 (IOD #1) EID 8
NSOCK INFO [0.5130s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.2.28:445]
Service scan sending probe NULL to 10.129.2.28:445 (tcp)
NSOCK INFO [0.5130s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 6000ms) EID 18
NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: READ TIMEOUT for EID 18 [10.129.2.28:445]
Service scan sending probe SMBProgNeg to 10.129.2.28:445 (tcp)
NSOCK INFO [6.5190s] nsock_write(): Write request for 168 bytes to IOD #1 EID 27 [10.129.2.28:445]
NSOCK INFO [6.5190s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 5000ms) EID 34
NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 27 [10.129.2.28:445]
NSOCK INFO [6.5320s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 34 [10.129.2.28:445] (135 bytes)
Service scan match (Probe SMBProgNeg matched with SMBProgNeg line 13836): 10.129.2.28:445 is netbios-ssn.  Version: |Samba smbd|3.X - 4.X|workgroup: WORKGROUP|
NSOCK INFO [6.5320s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: Ubuntu

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|
|`-p 445`|Varre apenas a porta especificada.|
|`--reason`|Exibe o motivo pelo qual uma porta está em um determinado estado.|
|`-sV`|Executa uma varredura de serviço.|

Mais informações sobre as técnicas de varredura de portas podem ser encontradas em: [https://nmap.org/book/man-port-scanning-techniques.html](https://nmap.org/book/man-port-scanning-techniques.html)
