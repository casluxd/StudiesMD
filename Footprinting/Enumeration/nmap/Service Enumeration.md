Para nós, é essencial determinar o aplicativo e sua versão com a maior precisão possível. Podemos usar essas informações para verificar vulnerabilidades conhecidas e analisar o código-fonte dessa versão, se a encontrarmos. Um número de versão exato nos permite procurar um exploit mais preciso que se encaixe no serviço e no sistema operacional de nosso alvo.

## Detecção da versão do serviço

É recomendável executar primeiro uma verificação rápida de portas, o que nos dá uma pequena visão geral das portas disponíveis. Isso causa significativamente menos tráfego, o que é vantajoso para nós porque, caso contrário, podemos ser descobertos e bloqueados pelos mecanismos de segurança. Podemos lidar com isso primeiro e executar uma verificação de porta em segundo plano, que mostra todas as portas abertas ( `-p-`). Podemos usar a verificação de versão para verificar as portas específicas de serviços e suas versões ( `-sV`).

Uma verificação de porta completa leva muito tempo. Para visualizar o status da varredura, podemos pressionar `[Space Bar]` durante a varredura, o que fará com `Nmap` que nos mostre o status da varredura.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:44 CEST
[Space Bar]
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 3.64% done; ETC: 19:45 (0:00:53 remaining)
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p-`|Verifica todas as portas.|
|`-sV`|Executa a detecção de versão do serviço em portas especificadas.|

Outra opção ( `--stats-every=5s`) que podemos utilizar é definir em quais períodos de tempo o status deve ser mostrado. Aqui podemos especificar o número de segundos ( `s`) ou minutos ( `m`), após os quais queremos obter o status.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV --stats-every=5s

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:46 CEST
Stats: 0:00:05 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 13.91% done; ETC: 19:49 (0:00:31 remaining)
Stats: 0:00:10 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 39.57% done; ETC: 19:48 (0:00:15 remaining)
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p-`|Verifica todas as portas.|
|`-sV`|Executa a detecção de versão do serviço em portas especificadas.|
|`--stats-every=5s`|Mostra o progresso da verificação a cada 5 segundos.|

Também podemos aumentar o `verbosity level`( `-v`/ `-vv`), que nos mostrará as portas abertas diretamente quando `Nmap` as detectar.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -v 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:03 CEST
NSE: Loaded 45 scripts for scanning.
Initiating ARP Ping Scan at 20:03
Scanning 10.129.2.28 [1 port]
Completed ARP Ping Scan at 20:03, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:03
Completed Parallel DNS resolution of 1 host. at 20:03, 0.02s elapsed
Initiating SYN Stealth Scan at 20:03
Scanning 10.129.2.28 [65535 ports]
Discovered open port 995/tcp on 10.129.2.28
Discovered open port 80/tcp on 10.129.2.28
Discovered open port 993/tcp on 10.129.2.28
Discovered open port 143/tcp on 10.129.2.28
Discovered open port 25/tcp on 10.129.2.28
Discovered open port 110/tcp on 10.129.2.28
Discovered open port 22/tcp on 10.129.2.28
<SNIP>
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p-`|Verifica todas as portas.|
|`-sV`|Executa a detecção de versão do serviço em portas especificadas.|
|`-v`|Aumenta o detalhamento da verificação, que exibe informações mais detalhadas.|

## Pegando Banner

Uma vez concluída a verificação, veremos todas as portas TCP com o serviço correspondente e suas versões ativas no sistema.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:00 CEST
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).
Not shown: 65525 closed ports
PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open     smtp         Postfix smtpd
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
110/tcp   open     pop3         Dovecot pop3d
139/tcp   filtered netbios-ssn
143/tcp   open     imap         Dovecot imapd (Ubuntu)
445/tcp   filtered microsoft-ds
993/tcp   open     ssl/imap     Dovecot imapd (Ubuntu)
995/tcp   open     ssl/pop3     Dovecot pop3d
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p-`|Verifica todas as portas.|
|`-sV`|Executa a detecção de versão do serviço em portas especificadas.|

Principalmente, `Nmap` examina os banners das portas digitalizadas e os imprime. Se não conseguir identificar as versões por meio dos banners, `Nmap` tenta identificá-las por meio de um sistema de correspondência baseado em assinatura, mas isso aumenta significativamente a duração da verificação. Uma desvantagem dos resultados `Nmap` apresentados é que a varredura automática pode perder algumas informações porque às vezes `Nmap` não sabe como lidar com isso. Vejamos um exemplo disso.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 20:10 CEST
<SNIP>
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
Service scan match (Probe NULL matched with NULL line 3104): 10.129.2.28:25 is smtp.  Version: |Postfix smtpd|||
NSOCK INFO [0.4200s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
Nmap scan report for 10.129.2.28
Host is up (0.076s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
```

|**Opções de digitalização**|**Descrição**|
|---|---|
|`10.129.2.28`|Examina o destino especificado.|
|`-p-`|Verifica todas as portas.|
|`-sV`|Executa a detecção de versão do serviço em portas especificadas.|
|`-Pn`|Desativa as solicitações de eco ICMP.|
|`-n`|Desativa a resolução de DNS.|
|`--disable-arp-ping`|Desativa o ping ARP.|
|`--packet-trace`|Mostra todos os pacotes enviados e recebidos.|

Se observarmos os resultados de `Nmap`, podemos ver o status da porta, o nome do serviço e o nome do host. No entanto, vamos olhar para esta linha aqui:
- `NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..`

Então vemos que o servidor SMTP em nosso destino nos forneceu mais informações do que `Nmap` nos mostrou. Porque aqui vemos que é a distribuição Linux `Ubuntu`. Isso acontece porque, após um handshake de três vias bem-sucedido, o servidor geralmente envia um banner para identificação. Isso serve para que o cliente saiba com qual serviço está trabalhando. No nível da rede, isso acontece com um sinalizador `PSH` no cabeçalho TCP. No entanto, pode acontecer que alguns serviços não forneçam imediatamente essas informações. Também é possível remover ou manipular os banners dos respectivos serviços. Se nos conectarmos manualmente ao servidor SMTP usando `nc`, pegarmos o banner e interceptarmos o tráfego de rede usando `tcpdump`, podemos ver o que `Nmap` não nos foi mostrado.

#### TcpdumpName

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
```

```shell-session
casluxd@htb[/htb]$  nc -nv 10.129.2.28 25

Connection to 10.129.2.28 port 25 [tcp/*] succeeded!
220 inlane ESMTP Postfix (Ubuntu)
```

#### Tcpdump - Tráfego Interceptado

```shell-session
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 1, win 2058, options [nop,nop,TS val 331260304 ecr 1800383922], length 0
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 36, win 2058, options [nop,nop,TS val 331260368 ecr 1800383985], length 0
```

As três primeiras linhas nos mostram o three-way handshake.

1. `[SYN]`: `18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], <SNIP>`
2. `SYN-ACK: 18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], <SNIP> ` 
3. `ACK: 18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP>`

Depois disso, o servidor SMTP de destino nos envia um pacote TCP com os sinalizadores `PSH` e `ACK`, onde `PSH` informa que o servidor de destino está enviando dados para nós e `ACK` simultaneamente nos informa que todos os dados necessários foram enviados.

4. `[PSH-ACK]: 18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], <SNIP>`

O último pacote TCP que enviamos confirma o recebimento dos dados com um `ACK`.

5. `[ACK]: 18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP> `

