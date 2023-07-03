
Tcpdump é um sniffer de pacote de linha de comando que pode capturar e interpretar quadros de dados diretamente de um arquivo ou interface de rede. Ele foi construído para uso em qualquer sistema operacional semelhante ao Unix e tinha um gêmeo do Windows chamado WinDump. É uma ferramenta poderosa e direta usada na maioria dos sistemas baseados em Unix. Não requer GUI e pode ser usado através de qualquer terminal ou conexão remota, como SSH.

No entanto, esta ferramenta pode parecer avassaladora no início devido às muitas funções e filtros diferentes que nos oferece. No entanto, uma vez que aprendemos as funções essenciais, acharemos muito mais fácil usar essa ferramenta com eficiência. Para capturar o tráfego de rede "off the wire", ele usa as bibliotecas pcap e libpcap, emparelhadas com uma interface em modo promíscuo para escutar os dados. Isso permite que o programa veja e capture pacotes provenientes ou destinados a qualquer dispositivo na rede local, não apenas os pacotes destinados a nós.

O TCPDump está disponível para a maioria dos sistemas Unix e derivados do Unix, como AIX, BSD, Linux, Solaris, e é fornecido por muitos fabricantes que já fazem parte do sistema. Devido ao acesso direto ao hardware, precisamos de root ou privilégios de administrador para executar esta ferramenta. Para nós, isso significa que teremos que utilizar o sudo para executar o TCPDump, conforme visto nos exemplos abaixo. O TCPDump geralmente vem pré-instalado na maioria dos sistemas operacionais Linux.

`Deve-se notar que o Windows tinha uma porta TCPDump chamada Windump. Suporte para windump cessou. Como alternativa, executar uma distribuição Linux como Parrot ou Ubuntu no Windows Subsystem para Linux pode ser uma maneira fácil de ter um host virtual Linux diretamente em nosso computador, permitindo o uso de TCPDump e muitas outras ferramentas construídas pelo Linux.`

# Captura de tráfego com TCPDump

Devido às muitas funções e filtros diferentes, devemos primeiro nos familiarizar com os recursos essenciais da ferramenta. Vamos discutir algumas opções básicas do TCPDump, demonstrar alguns comandos e mostrar como salvar o tráfego para arquivos PCAP e ler a partir deles.

## Opções básicas de captura

|**Switch Command**|**Result**|
|:-:|---|
|D|Will display any interfaces available to capture from.|
|i|Selects an interface to capture from. ex. -i eth0|
|n|Do not resolve hostnames.|
|nn|Do not resolve hostnames or well-known ports.|
|e|Will grab the ethernet header along with upper-layer data.|
|X|Show Contents of packets in hex and ASCII.|
|XX|Same as X, but will also specify ethernet headers. (like using Xe)|
|v, vv, vvv|Increase the verbosity of output shown and saved.|
|c|Grab a specific number of packets, then quit the program.|
|s|Defines how much of a packet to grab.|
|S|change relative sequence numbers in the capture display to absolute sequence numbers. (13248765839 instead of 101)|
|q|Print less protocol information.|
|r file.pcap|Read from a file.|
|w file.pcap|Write into a file|

### Listando todas as interfaces disponíveis

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -D

1.eth0 [Up, Running, Connected]
2.any (Pseudo-device that captures on all interfaces) [Up, Running]
3.lo [Up, Running, Loopback]
4.bluetooth0 (Bluetooth adapter number 0) [Wireless, Association status unknown]
5.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
6.nflog (Linux netfilter log (NFLOG) interface) [none]
7.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
8.dbus-system (D-Bus system bus) [none]
9.dbus-session (D-Bus session bus) [none
```

O comando acima chama tcpdump usando privilégios sudo e lista as interfaces de rede utilizáveis. Podemos escolher uma dessas interfaces de rede e dizer ao tcpdump quais interfaces ele deve escutar.

### Escolhendo uma interface para capturar

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10:58:33.719241 IP 172.16.146.2.55260 > 172.67.1.1.https: Flags [P.], seq 1953742992:1953743073, ack 2034210498, win 501, length 81
10:58:33.747853 IP 172.67.1.1.https > 172.16.146.2.55260: Flags [.], ack 81, win 158, length 0
10:58:33.750393 IP 172.16.146.2.52195 > 172.16.146.1.domain: 7579+ PTR? 1.1.67.172.in-addr.arpa. (41)
```

Neste terminal, estamos chamando o tcpdump e selecionando a interface eth0 para capturar o tráfego. Assim que emitirmos o comando, o tcpdump começará a farejar o tráfego e verá os primeiros pacotes na interface. Ao emitir as opções -nn como visto abaixo, dizemos ao TCPDump para abster-se de resolver endereços IP e números de porta para seus nomes de host e nomes de porta comuns. Nesta representação, o último octeto é a porta de/para a qual vai a conexão.

#### Desabilitando resolução de nome

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 -nn

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:02:35.580449 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [P.], seq 988167196:988167233, ack 1512376150, win 501, options [nop,nop,TS val 214282239 ecr 77421665], length 37
11:02:35.588695 IP 172.16.146.2.55272 > 172.67.1.1.443: Flags [P.], seq 940648841:940648916, ack 4248406693, win 501, length 75
11:02:35.654368 IP 172.67.1.1.443 > 172.16.146.2.55272: Flags [.], ack 75, win 70, length 0
11:02:35.728889 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [P.], seq 1:34, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 33
11:02:35.728988 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [.], ack 34, win 501, options [nop,nop,TS val 214282388 ecr 77434740], length 0
11:02:35.729073 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [P.], seq 34:65, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 31
11:02:35.729081 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [.], ack 65, win 501, options [nop,nop,TS val 214282388 ecr 77434740], length 0
11:02:35.729348 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [F.], seq 65, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 0
```

Ao utilizar a opção -e, estamos incumbindo o tcpdump de incluir os cabeçalhos ethernet na saída da captura junto com seu conteúdo regular. Podemos ver isso funcionando examinando a saída. Normalmente, o primeiro e o segundo campos consistem no carimbo de data/hora e, em seguida, no início do cabeçalho IP. Agora ele consiste em Timestamp e o endereço MAC de origem do host.

#### Mostrar o Header Ethernet

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 -e

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:05:45.982115 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 103: 172.16.146.2.57142 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 922951468:922951505, ack 1842875143, win 501, options [nop,nop,TS val 1368272062 ecr 65637925], length 37
11:05:45.989652 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 129: 172.16.146.2.55272 > 172.67.1.1.https: Flags [P.], seq 940656124:940656199, ack 4248413119, win 501, length 75
11:05:46.047731 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 85: 172.16.146.2.54006 > 172.16.146.1.domain: 31772+ PTR? 207.22.80.99.in-addr.arpa. (43)
11:05:46.049134 8a:66:5a:11:8d:64 (oui Unknown) > 00:0c:29:97:52:65 (oui Unknown), ethertype IPv4 (0x0800), length 147: 172.16.146.1.domain > 172.16.146.2.54006: 31772 1/0/0 PTR ec2-99-80-22-207.eu-west-1.compute.amazonaws.com. (105)
```

Emitindo a opção -X, podemos ver o pacote um pouco mais claro agora. Obtemos uma saída ASCII à direita para interpretar qualquer coisa em texto não criptografado que corresponda à saída hexadecimal à esquerda.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 -X

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:10:34.972248 IP 172.16.146.2.57170 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 2612172989:2612173026, ack 3165195759, win 501, options [nop,nop,TS val 1368561052 ecr 65712142], length 37
    0x0000:  4500 0059 4352 4000 4006 3f1b ac10 9202  E..YCR@.@.?.....
    0x0010:  6350 16cf df52 01bb 9bb2 98bd bca9 0def  cP...R..........
    0x0020:  8018 01f5 b87d 0000 0101 080a 5192 959c  .....}......Q...
    0x0030:  03ea b00e 1703 0300 2000 0000 0000 0000  ................
    0x0040:  0adb 84ac 34b4 910a 0fb4 2f49 9865 eb45  ....4...../I.e.E
    0x0050:  883c eafd 8266 3e23 88                   .<...f>#.
11:10:34.984582 IP 172.16.146.2.38732 > 172.16.146.1.domain: 22938+ A? app.hackthebox.eu. (35)
    0x0000:  4500 003f 2e6b 4000 4011 901e ac10 9202  E..?.k@.@.......
    0x0010:  ac10 9201 974c 0035 002b 7c61 599a 0100  .....L.5.+|aY...
    0x0020:  0001 0000 0000 0000 0361 7070 0a68 6163  .........app.hac
    0x0030:  6b74 6865 626f 7802 6575 0000 0100 01    kthebox.eu.....
11:10:35.055497 IP 172.16.146.2.43116 > 172.16.146.1.domain: 6524+ PTR? 207.22.80.99.in-addr.arpa. (43)
    0x0000:  4500 0047 2e72 4000 4011 900f ac10 9202  E..G.r@.@.......
    0x0010:  ac10 9201 a86c 0035 0033 7c69 197c 0100  .....l.5.3|i.|..
    0x0020:  0001 0000 0000 0000 0332 3037 0232 3202  .........207.22.
    0x0030:  3830 0239 3907 696e 2d61 6464 7204 6172  80.99.in-addr.ar
    0x0040:  7061 0000 0c00 01                        pa.....
```

Preste atenção ao nível de detalhe na saída acima. Notaremos que temos informações sobre as opções de cabeçalho IP, como tempo de vida, deslocamento e outros sinalizadores e mais detalhes nos protocolos da camada superior. Abaixo, estamos combinando os switches para criar a saída ao nosso gosto.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 -nnvXX

tcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:13:59.149599 IP (tos 0x0, ttl 64, id 24075, offset 0, flags [DF], proto TCP (6), length 89)
    172.16.146.2.42454 > 54.77.251.34.443: Flags [P.], cksum 0x6fce (incorrect -> 0xb042), seq 671020720:671020757, ack 3699222968, win 501, options [nop,nop,TS val 1154433101 ecr 1116647414], length 37
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  0059 5e0b 4000 4006 6d11 ac10 9202 364d  .Y^.@.@.m.....6M
    0x0020:  fb22 a5d6 01bb 27fe f6b0 dc7d a9b8 8018  ."....'....}....
    0x0030:  01f5 6fce 0000 0101 080a 44cf 404d 428e  ..o.......D.@MB.
    0x0040:  aff6 1703 0300 2000 0000 0000 0000 09bb  ................
    0x0050:  38d9 d89a 2d70 73d5 a01e 9df7 2c48 5b8a  8...-ps.....,H[.
    0x0060:  d64d 8e42 2ccc 43                        .M.B,.C
11:13:59.157113 IP (tos 0x0, ttl 64, id 31823, offset 0, flags [DF], proto UDP (17), length 63)
    172.16.146.2.55351 > 172.16.146.1.53: 26460+ A? app.hackthebox.eu. (35)
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  003f 7c4f 4000 4011 423a ac10 9202 ac10  .?|O@.@.B:......
    0x0020:  9201 d837 0035 002b 7c61 675c 0100 0001  ...7.5.+|ag\....
    0x0030:  0000 0000 0000 0361 7070 0a68 6163 6b74  .......app.hackt
    0x0040:  6865 626f 7802 6575 0000 0100 01         hebox.eu.....
11:13:59.158029 IP (tos 0x0, ttl 64, id 20784, offset 0, flags [none], proto UDP (17), length 111)
    172.16.146.1.53 > 172.16.146.2.55351: 26460 3/0/0 app.hackthebox.eu. A 104.20.55.68, app.hackthebox.eu. A 172.67.1.1, app.hackthebox.eu. A 104.20.66.68 (83)
    0x0000:  000c 2997 5265 8a66 5a11 8d64 0800 4500  ..).Re.fZ..d..E.
    0x0010:  006f 5130 0000 4011 ad29 ac10 9201 ac10  .oQ0..@..)......
    0x0020:  9202 0035 d837 005b 9d2e 675c 8180 0001  ...5.7.[..g\....
    0x0030:  0003 0000 0000 0361 7070 0a68 6163 6b74  .......app.hackt
    0x0040:  6865 626f 7802 6575 0000 0100 01c0 0c00  hebox.eu........
    0x0050:  0100 0100 0000 ab00 0468 1437 44c0 0c00  .........h.7D...
    0x0060:  0100 0100 0000 ab00 04ac 4301 01c0 0c00  ..........C.....
    0x0070:  0100 0100 0000 ab00 0468 1442 44         .........h.BD
11:13:59.158335 IP (tos 0x0, ttl 64, id 20242, offset 0, flags [DF], proto TCP (6), length 60)
    172.16.146.2.55416 > 172.67.1.1.443: Flags [S], cksum 0xeb85 (incorrect -> 0x72f7), seq 3766489491, win 64240, options [mss 1460,sackOK,TS val 508232750 ecr 0,nop,wscale 7], length 0
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  003c 4f12 4000 4006 0053 ac10 9202 ac43  .<O.@.@..S.....C
    0x0020:  0101 d878 01bb e080 1193 0000 0000 a002  ...x............
    0x0030:  faf0 eb85 0000 0204 05b4 0402 080a 1e4b  ...............K
    0x0040:  042e 0000 0000 0103 0307                 ..........
```

Ao utilizar os switches, encadeá-los como no exemplo acima é a melhor prática.

## TCPDump Output

Ao olhar para a saída do TCPDump, pode ser um pouco opressor. Percorrer essas opções básicas já nos mostrou várias visualizações diferentes. Vamos dedicar um minuto para dissecar essa saída e explicar o que estamos vendo. A imagem e a tabela abaixo definirão cada campo. Lembre-se de que quanto mais detalhados somos com nossos filtros, mais detalhes de cada cabeçalho são mostrados.

![[Pasted image 20230530152128.png]]

- Amarelo: O campo timestamp vem primeiro e é configurável para mostrar a hora e a data em um formato que podemos assimilar facilmente.
- Laranja: Esta seção nos dirá qual é o cabeçalho da camada superior. Em nosso exemplo, ele mostra IP. Isso nos mostrará a origem e o destino do pacote junto com o número da porta usada para conectar. Formato: IP.porta == 172.16.146.2.21
- Verde: As flags utilizadas.
- Vermelho: Sequence and Acknowledgment Numbers, Esta seção mostra a sequência e os números de confirmação usados ​​para rastrear o segmento TCP. Nosso exemplo está utilizando números baixos para assumir que a sequência relativa e os números de confirmação estão sendo exibidos.
- Azul: Protocol Options, Aqui, veremos quaisquer valores de TCP negociados estabelecidos entre o cliente e o servidor, como window size, selective acknowledgments, window scale factors e muito mais.
- Branco: Misc observa que o dissecador encontrado estará presente aqui. Como o tráfego que estamos vendo é encapsulado, podemos ver mais informações de cabeçalho para diferentes protocolos. Em nosso exemplo, podemos ver que o dissecador TCPDump reconhece o tráfego FTP dentro do encapsulamento para exibi-lo para nós.

Existem muitas outras opções e informações que podem ser mostradas. Essas informações variam de acordo com a quantidade de verbosidade habilitada.

Existe uma grande vantagem em saber como funciona uma rede e como usar os filtros que o TCPDump fornece. Com eles, podemos visualizar o tráfego de rede, analisá-lo em busca de problemas e identificar interações de rede suspeitas rapidamente. Teoricamente, podemos usar o tcpdump para criar um sistema IDS/IPS fazendo com que um script Bash analise os pacotes interceptados de acordo com um padrão específico. Podemos então definir condições para, por exemplo, banir um determinado endereço IP que enviou muitas ICMP echo requests por um determinado período.

## File Input/Output com TCPDump

Usar -w gravará nossa captura em um arquivo. Lembre-se de que, à medida que capturamos o tráfego fora do fio, podemos usar rapidamente o espaço livre em disco e ter problemas de armazenamento se não formos cuidadosos. Quanto maior o nosso segmento de rede, mais rápido usaremos o armazenamento. Utilizar as opções demonstradas acima pode ajudar a ajustar a quantidade de dados armazenados em nossos PCAPs.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 -w ~/output.pcap

tcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10 packets captured
131 packets received by filter
0 packets dropped by kernel
```

Esta captura acima irá gerar a saída para um arquivo chamado output.pcap. Ao executar o tcpdump dessa maneira, a saída não rolará nosso terminal como de costume. Toda a saída do tcpdump está sendo redirecionada para o arquivo que especificamos para a captura.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -r ~/output.pcap

reading from file /home/trey/output.pcap, link-type EN10MB (Ethernet), snapshot length 262144
11:15:40.321509 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 2751910362:2751910399, ack 946558143, win 501, options [nop,nop,TS val 1368866401 ecr 65790024], length 37
11:15:40.337302 IP 172.16.146.2.55416 > 172.67.1.1.https: Flags [P.], seq 3766493458:3766493533, ack 4098207917, win 501, length 75
11:15:40.398103 IP 172.67.1.1.https > 172.16.146.2.55416: Flags [.], ack 75, win 73, length 0
11:15:40.457416 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [.], ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 0
11:15:40.458582 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [P.], seq 34:65, ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 31
11:15:40.458599 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 1, win 501, options [nop,nop,TS val 1368866538 ecr 65799068,nop,nop,sack 1 {34:65}], length 0
11:15:40.458643 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [P.], seq 1:34, ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 33
11:15:40.458655 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 65, win 501, options [nop,nop,TS val 1368866538 ecr 65799068], length 0
11:15:40.458915 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 37:68, ack 65, win 501, options [nop,nop,TS val 1368866539 ecr 65799068], length 31
11:15:40.458964 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [F.], seq 68, ack 65, win 501, options [nop,nop,TS val 1368866539 ecr 65799068], length 0
```

Isso lerá a captura armazenada em output.pcap. Observe que está de volta a uma visão básica. Para obter informações mais detalhadas do arquivo de captura, reaplique nossas opções.