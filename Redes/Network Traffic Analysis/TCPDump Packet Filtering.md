
O Tcpdump fornece uma maneira robusta e eficiente de analisar os dados incluídos em nossas capturas por meio de filtros de pacotes. Esta seção examinará esses filtros e terá uma ideia de como eles modificam a saída de nossa captura.

# Opções de filtragem e sintaxe avançada

A utilização de opções de filtragem mais avançadas, como as listadas abaixo, nos permitirá reduzir o tráfego impresso na saída ou enviado ao arquivo. Ao reduzir a quantidade de informações que capturamos e gravamos no disco, podemos ajudar a reduzir o espaço necessário para gravar o arquivo e ajudar o buffer a processar os dados mais rapidamente. Os filtros podem ser úteis quando combinados com as opções de sintaxe tcpdump padrão. Podemos capturar tão amplamente quanto desejarmos, ou ser superespecíficos apenas para capturar pacotes de um host específico, ou mesmo com um bit específico no cabeçalho TCP definido como ativado. É altamente recomendável explorar os filtros mais avançados e encontrar diferentes combinações.

Esses filtros e operadores avançados não são uma lista exaustiva. Eles foram escolhidos porque são os mais usados ​​e nos colocam em funcionamento rapidamente. Quando implementados, esses filtros inspecionarão todos os pacotes capturados e procurarão os valores fornecidos no cabeçalho do protocolo para corresponder.

## Filtros TCPDump úteis

|**Filter**|**Result**|
|---|---|
|host|`host` will filter visible traffic to show anything involving the designated host. Bi-directional|
|src / dest|`src` and `dest` are modifiers. We can use them to designate a source or destination host or port.|
|net|`net` will show us any traffic sourcing from or destined to the network designated. It uses / notation.|
|proto|will filter for a specific protocol type. (ether, TCP, UDP, and ICMP as examples)|
|port|`port` is bi-directional. It will show any traffic with the specified port as the source or destination.|
|portrange|`portrange` allows us to specify a range of ports. (0-1024)|
|less / greater "< >"|`less` and `greater` can be used to look for a packet or protocol option of a specific size.|
|and / &&|`and` `&&` can be used to concatenate two different filters together. for example, src host AND port.|
|or|`or` allows for a match on either of two conditions. It does not have to meet both. It can be tricky.|
|not|`not` is a modifier saying anything but x. For example, not UDP.|

Com esses filtros, podemos filtrar o tráfego de rede na maioria das propriedades para facilitar a análise. Vejamos alguns exemplos desses filtros e como eles ficam quando os usamos. Ao usar o filtro de host, qualquer IP inserido será verificado no campo IP de origem ou destino. Isso pode ser visto na saída abaixo.

```shell-session
casluxd@htb[/htb]$ ### Syntax: host [IP]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 host 172.16.146.2

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:50:53.072536 IP 172.16.146.2.48738 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3400465007:3400465044, ack 254421756, win 501, options [nop,nop,TS val 220968655 ecr 80852594], length 37
14:50:53.108740 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227143181:4227143273, ack 1980233980, win 21975, length 92
14:50:53.173084 IP 172.67.1.1.https > 172.16.146.2.55606: Flags [.], ack 92, win 69, length 0
14:50:53.175017 IP 172.16.146.2.35744 > 172.16.146.1.domain: 55991+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:50:53.175714 IP 172.16.146.1.domain > 172.16.146.2.35744: 55991 1/0/0 PTR ec2-52-31-199-148.eu-west-1.compute.amazonaws.com. (107) 
```

Esse filtro geralmente é usado quando queremos examinar apenas um host ou servidor específico. Com isso, podemos identificar com quem esse host ou servidor se comunica e de que forma. Com base em nossas configurações de rede, entenderemos se esta conexão é legítima. Se a comunicação parecer estranha, podemos usar outros filtros e opções para visualizar o conteúdo com mais detalhes. Além dos hosts individuais, também podemos definir o host de origem e o host de destino. Também podemos definir redes inteiras e suas portas.

```shell-session
casluxd@htb[/htb]$ ### Syntax: src/dst [host|net|port] [IP|Network Range|Port]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 src host 172.16.146.2
  
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:53:36.199628 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 1428378231:1428378268, ack 3778572066, win 501, options [nop,nop,TS val 221131782 ecr 80889856], length 37
14:53:36.203166 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227144035:4227144103, ack 1980235221, win 21975, length 68
14:53:36.267059 IP 172.16.146.2.36424 > 172.16.146.1.domain: 40873+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:53:36.267880 IP 172.16.146.2.51151 > 172.16.146.1.domain: 10032+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:53:36.276425 IP 172.16.146.2.46588 > 172.16.146.1.domain: 28357+ PTR? 1.1.67.172.in-addr.arpa. (41)
14:53:36.337722 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 221131920 ecr 80899875], length 0
14:53:36.338841 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 65, win 501, options [nop,nop,TS val 221131921 ecr 80899875], length 0
14:53:36.339273 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 37:68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 31
14:53:36.339334 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [F.], seq 68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 0
14:53:36.370791 IP 172.16.146.2.32972 > 172.16.146.1.domain: 3856+ PTR? 1.146.16.172.in-addr.arpa. (43)
```

Origem e destino nos permitem trabalhar com as direções de comunicação. Por exemplo, na última saída, especificamos que nosso host de origem é 172.16.146.2 e somente os pacotes enviados desse host serão interceptados. Isso pode ser feito para portas e intervalos de rede também. Um exemplo disso utilizando a porta src # seria mais ou menos assim:

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 tcp src port 80

06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP
```

Observe agora que vemos apenas um lado da conversa? Isso ocorre porque estamos filtrando na porta de origem 80 (HTTP).
Usado dessa maneira, net pegará qualquer coisa que corresponda à notação / para uma rede. No exemplo, estamos procurando por qualquer coisa destinada à rede 172.16.146.0/24.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 dest net 172.16.146.0/24

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
16:33:14.376003 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], ack 1486880537, win 316, options [nop,nop,TS val 2311579424 ecr 263866084], length 0
16:33:14.442123 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 0:385, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 385
16:33:14.442188 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 385:1803, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 1418
16:33:14.442223 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 1803:4639, ack 1, win 316, options [nop,nop,TS val 2311579494 ecr 263866084], length 2836
16:33:14.443161 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 4639:5817, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 1178
16:33:14.443199 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 5817:8653, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 2836
16:33:14.444407 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 8653:10071, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445479 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 10071:11489, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445531 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 11489:12907, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418
16:33:14.446955 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 12907:14325, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418
```

Este filtro pode utilizar o nome ou número de protocolo comum para qualquer protocolo IP, IPv6 ou Ethernet. Exemplos comuns seriam tcp[6], udp[17] ou icmp[1]. Podemos utilizar o nome comum top e o número do protocolo bottom nas saídas acima. Podemos ver que produziu a mesma saída. Podemos ver que produziu a mesma saída. Na maioria das vezes, eles são intercambiáveis, mas a utilização do proto se tornará mais útil quando você começar a dissecar uma parte específica do IP ou de outros cabeçalhos de protocolo. Isso ficará mais claro posteriormente nesta seção, quando falarmos sobre a procura de sinalizadores TCP. Podemos dar uma olhada neste recurso para obter uma lista útil(https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) que abrange números de protocolo.

```shell-session
casluxd@htb[/htb]$ ### Syntax: [tcp/udp/icmp]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 udp

06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)
```

```shell-session
casluxd@htb[/htb]$ ### Syntax: proto [protocol number]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 proto 17

06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)
```

Usando o filtro de porta, devemos ter em mente o que estamos procurando e como esse protocolo funciona. Alguns protocolos padrão como HTTP ou HTTPS usam apenas as portas 80 e 443 com o protocolo de transporte do TCP. Com isso em mente, imagine as portas como uma maneira simples de estabelecer conexões e protocolos como TCP e UDP para determinar se eles usam um método estabelecido. As portas por si só podem ser usadas para qualquer coisa, portanto, a filtragem na porta 80 mostrará todo o tráfego nesse número de porta. No entanto, se quisermos capturar todo o tráfego HTTP, utilizar a porta tcp 80 garantirá que veremos apenas o tráfego HTTP.

Com protocolos que usam TCP e UDP para diferentes funções, como DNS, podemos filtrar olhando para uma ou outra porta TCP/UDP 53 ou filtrar para a porta 53. Ao fazer isso, veremos qualquer tráfego utilizando essa porta, independentemente do protocolo de transporte.

```shell-session
casluxd@htb[/htb]$ ### Syntax: port [port number]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 tcp port 443

06:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [P.], seq 1:480, ack 1, win 9660, length 479: HTTP: GET /download.html HTTP/1.1
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.205385 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 6901:8281, ack 480, win 6432, length 1380: HTTP
06:17:10.295515 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [P.], seq 918691368:918692089, ack 778785668, win 8760, length 721: HTTP: GET /pagead/ads?client=ca-pub-2309191948673629&random=1084443430285&lmt=1082467020&format=468x60_as&output=html&url=http%3A%2F%2Fwww.ethereal.com%2Fdownload.html&color_bg=FFFFFF&color_text=333333&color_link=000000&color_url=666633&color_border=666633 HTTP/1.1
```

Além das portas individuais, também podemos definir faixas específicas dessas portas, que são ouvidas pelo TCPdump. Ouvir em um intervalo de portas pode ser especialmente útil quando vemos o tráfego de rede de portas que não correspondem aos serviços rodando em nossos servidores. Por exemplo, se tivermos um servidor da Web com as portas TCP 80 e 443 em execução em um determinado segmento de nossa rede e, de repente, tivermos tráfego de rede de saída da porta TCP 10000 ou outras, isso é muito suspeito.

O filtro portrange, como visto abaixo, nos permite ver tudo dentro do alcance da porta. No exemplo, vemos algum tráfego DNS junto com algumas solicitações da Web HTTP.

```shell-session
casluxd@htb[/htb]$ ### Syntax: portrange [portrange 0-65535]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 portrange 0-1024

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:10:35.092477 IP 172.16.146.1.domain > 172.16.146.2.32824: 47775 1/0/0 CNAME autopush.prod.mozaws.net. (81)
13:10:35.093217 IP 172.16.146.2.48078 > 172.16.146.1.domain: 30234+ A? ocsp.pki.goog. (31)
13:10:35.093334 IP 172.16.146.2.48078 > 172.16.146.1.domain: 32024+ AAAA? ocsp.pki.goog. (31)
13:10:35.136255 IP 172.16.146.1.domain > 172.16.146.2.48078: 32024 2/0/0 CNAME pki-goog.l.google.com., AAAA 2607:f8b0:4002:c09::5e (94)
13:10:35.137348 IP 172.16.146.1.domain > 172.16.146.2.48078: 30234 2/0/0 CNAME pki-goog.l.google.com., A 172.217.164.67 (82)
13:10:35.137989 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [S], seq 1146136517, win 64240, options [mss 1460,sackOK,TS val 1337520268 ecr 0,nop,wscale 7], length 0
13:10:35.174443 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [S.], seq 345110814, ack 1146136518, win 65535, options [mss 1430,sackOK,TS val 1000152427 ecr 1337520268,nop,wscale 8], length 0
13:10:35.174481 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [.], ack 1, win 502, options [nop,nop,TS val 1337520304 ecr 1000152427], length 0
13:10:35.174716 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [P.], seq 1:379, ack 1, win 502, options [nop,nop,TS val 1337520305 ecr 1000152427], length 378: HTTP: POST /gts1o1core HTTP/1.1
13:10:35.208007 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [.], ack 379, win 261, options [nop,nop,TS val 1000152462 ecr 1337520305], length 0
```

Em seguida, procuramos qualquer pacote com menos de 64 bytes. Na saída a seguir, podemos ver que, para essa captura, esses pacotes consistiam principalmente em pacotes SYN, FIN ou KeepAlive. Less e graeater pode ser um conjunto modificador útil. Por exemplo, digamos que estamos procurando capturar o tráfego que inclui uma transferência de arquivo ou um conjunto de arquivos. Sabemos que esses arquivos serão maiores do que o tráfego normal. Para demonstrar, podemos utilizar greater 500 (alternativamente '>500'), que nos mostrará apenas pacotes com tamanho maior que 500 bytes. Isso removerá todos os pacotes extras da exibição com a qual sabemos que ainda não estamos preocupados.

```shell-session
casluxd@htb[/htb]$ ### Syntax: less/greater [size in bytes]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 less 64

06:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.325558 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 8281, win 9660, length 0
06:17:10.806249 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 11041, win 9660, length 0
06:17:10.956465 IP 216.239.59.99.http > dialin-145-254-160-237.pools.arcor-ip.net.3371: Flags [.], ack 918692089, win 31460, length 0
06:17:11.126710 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 12421, win 9660, length 0
06:17:11.266912 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:11.527286 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 13801, win 9660, length 0
06:17:11.667488 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 16561, win 9660, length 0
06:17:11.807689 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 17941, win 9660, length 0
06:17:12.088092 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:12.328438 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18365, win 9236, length 0
06:17:25.216971 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [F.], seq 18365, ack 480, win 6432, length 0
06:17:25.216971 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18366, win 9236, length 0
06:17:37.374452 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [F.], seq 480, ack 18366, win 9236, length 0
06:17:37.704928 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 481, win 6432, length 0
```

Acima foi um excelente exemplo de como usar less. Podemos utilizar o modificador greater 500 para mostrar apenas pacotes com 500 ou mais bytes. Ele voltou com uma resposta única no ASCII. Podemos contar o que aconteceu aqui?

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 greater 500

21:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 401695766:401696254, ack 2579866052, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF) #4: Tue Oct 12 20:42:32 CDT 1999

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.
```

AND como modificador nos mostrará qualquer coisa que atenda aos dois requisitos definidos. Por exemplo, o `host 10.12.1.122 and tcp port 80 `procurarão qualquer coisa do host de origem e conterão o tráfego TCP ou UDP da porta 80. Ambos os critérios devem ser atendidos para que o filtro capture o pacote. Podemos ver isso em ação acima. Aqui utilizamos o `host 192.168.0.1 and port 23 `como filtro. Portanto, veremos apenas o tráfego desse host específico, que é apenas o tráfego da porta 23.

```shell-session
casluxd@htb[/htb]$ ### Syntax: and [requirement]
casluxd@htb[/htb]$ sudo tcpdump -i eth0 host 192.168.0.1 and port 23

21:12:38.387203 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [S], seq 2579865836, win 32120, options [mss 1460,sackOK,TS val 10233636 ecr 0,nop,wscale 0], length 0
21:12:38.389728 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [S.], seq 401695549, ack 2579865837, win 17376, options [mss 1448,nop,wscale 0,nop,nop,TS val 2467372 ecr 10233636], length 0
21:12:38.389775 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 0
21:12:38.391363 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [P.], seq 1:28, ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 27 [telnet DO SUPPRESS GO AHEAD, WILL TERMINAL TYPE, WILL NAWS, WILL TSPEED, WILL LFLOW, WILL LINEMODE, WILL NEW-ENVIRON, DO STATUS, WILL XDISPLOC]
21:12:38.537538 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 1:4, ack 28, win 17349, options [nop,nop,TS val 2467372 ecr 10233636], length 3 [telnet DO AUTHENTICATION]
```

Os outros modificadores OR e NOT nos fornecem uma maneira de especificar várias condições ou negar algo. Vamos brincar um pouco com isso agora. O que notamos sobre esta saída?

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:39:51.224071 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 72, length 64
14:39:51.251635 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 72, length 64
14:39:51.329340 IP 172.16.146.2.39003 > 172.16.146.1.domain: 8231+ PTR? 8.8.8.8.in-addr.arpa. (38)
14:39:51.330334 IP 172.16.146.1.domain > 172.16.146.2.39003: 8231 1/0/0 PTR dns.google. (62)
14:39:51.330613 IP 172.16.146.2.50633 > 172.16.146.1.domain: 65266+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:39:51.331461 IP 172.16.146.1.domain > 172.16.146.2.50633: 65266 NXDomain* 0/0/0 (43)
14:39:51.399970 IP 172.16.146.2.54742 > 72.21.91.29.http: Flags [.], ack 358742210, win 501, options [nop,nop,TS val 1924174236 ecr 1068405332], length 0
14:39:51.420559 IP 72.21.91.29.http > 172.16.146.2.54742: Flags [.], ack 1, win 131, options [nop,nop,TS val 1068415563 ecr 1924143536], length 0
14:39:51.432107 IP 172.16.146.2.41928 > 172.16.146.1.domain: 7232+ PTR? 1.146.16.172.in-addr.arpa. (43)
14:39:51.432795 IP 172.16.146.1.domain > 172.16.146.2.41928: 7232 NXDomain* 0/0/0 (43)
14:39:51.433048 IP 172.16.146.2.47075 > 172.16.146.1.domain: 18932+ PTR? 29.91.21.72.in-addr.arpa. (42)
14:39:51.434374 IP 172.16.146.1.domain > 172.16.146.2.47075: 18932 NXDomain 0/1/0 (113)
14:39:52.225941 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 73, length 64
14:39:52.252523 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 73, length 64
14:39:52.683881 IP 172.16.146.2.47004 > 151.139.128.14.http: Flags [.], ack 2006616877, win 501, options [nop,nop,TS val 1585722998 ecr 2103316650], length 0
14:39:52.712283 IP 151.139.128.14.http > 172.16.146.2.47004: Flags [.], ack 1, win 507, options [nop,nop,TS val 2103326900 ecr 1585692473], length 0
```

Temos uma mistura de diferentes origens e destinos, juntamente com vários tipos de protocolo. Se fôssemos usar o modificador OR (alternativamente ||), poderíamos solicitar o tráfego de um host específico ou apenas o tráfego ICMP como exemplo. Vamos executá-lo novamente e adicionar um OR.

```shell-session
casluxd@htb[/htb]$ ### Syntax: or/|| [requirement]
casluxd@htb[/htb]$ sudo tcpdump -r sus.pcap icmp or host 172.16.146.1

reading from file sus.pcap, link-type EN10MB (Ethernet), snapshot length 262144
14:54:03.659163 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 21, length 64
14:54:03.691278 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 21, length 64
14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:04.661179 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 22, length 64
14:54:04.687120 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 22, length 64
14:54:05.663097 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 23, length 64
14:54:05.686092 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 23, length 64
14:54:06.664174 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 24, length 64
14:54:06.697469 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 24, length 64
14:54:07.666273 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 25, length 64
14:54:07.701475 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 25, length 64
14:54:08.668364 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 26, length 64
14:54:08.694948 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 26, length 64
14:54:09.670523 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 27, length 64
14:54:09.694974 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 27, length 64
14:54:10.672858 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 28, length 64
14:54:10.697834 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 28, length 64
```

Nosso tráfego parece um pouco diferente agora. Isso ocorre porque muitos pacotes correspondem à variável ICMP, enquanto alguns correspondem à variável do host. Portanto, nesta saída, podemos ver algum tráfego ARP e tráfego ICMP. O filtro funcionou desde que 172.16.146.2 correspondeu à outra variável e apareceu como um host no campo de origem ou destino. Agora, o que acontece se utilizarmos o modificador NOT (alternativamente!)

```shell-session
casluxd@htb[/htb]$ ### Syntax: not/! [requirement]
casluxd@htb[/htb]$ sudo tcpdump -r sus.pcap not icmp

14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:16.541657 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3569937476:3569937513, ack 2948818703, win 501, options [nop,nop,TS val 713252991 ecr 12282469], length 37
14:54:16.568659 IP 172.16.146.2.53329 > 172.16.146.1.domain: 24866+ A? app.hackthebox.eu. (35)
14:54:16.616032 IP 172.16.146.1.domain > 172.16.146.2.53329: 24866 3/0/0 A 172.67.1.1, A 104.20.66.68, A 104.20.55.68 (83)
14:54:16.616396 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [S], seq 2697802378, win 64240, options [mss 1460,sackOK,TS val 533261003 ecr 0,nop,wscale 7], length 0
14:54:16.637895 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [S.], seq 752000032, ack 2697802379, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
14:54:16.637937 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 1, win 502, length 0
14:54:16.644551 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [P.], seq 1:514, ack 1, win 502, length 513
14:54:16.667236 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [.], ack 514, win 66, length 0
14:54:16.668307 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [P.], seq 1:2766, ack 514, win 66, length 2765
14:54:16.668319 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 2766, win 496, length 0
14:54:16.670536 IP ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.55592: Flags [P.], seq 1:34, ack 37, win 114, options [nop,nop,TS val 12294021 ecr 713252991], length 33
14:54:16.670559 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 713253120 ecr 12294021], length 0
```

Parece muito diferente agora. Vemos apenas algum tráfego ARP e, em seguida, vemos algum tráfego HTTPS que não obtínhamos antes. Isso ocorre porque negamos a exibição de qualquer tráfego ICMP usando não icmp.

## Filtros de pré-captura VS. Processamento pós-captura

Ao utilizar filtros, podemos aplicá-los diretamente na captura ou aplicá-los ao ler um arquivo de captura. Ao aplicá-los à captura, ele eliminará qualquer tráfego que não corresponda ao filtro. Isso reduzirá a quantidade de dados nas capturas e potencialmente limpará o tráfego que podemos precisar mais tarde, portanto, use-os apenas quando estiver procurando por algo específico, como solucionar um problema de conectividade de rede. Ao aplicar o filtro para capturar, lemos de um arquivo e o filtro analisará o arquivo e removerá qualquer coisa da saída do nosso terminal que não corresponda ao filtro especificado. Usar um filtro dessa maneira pode nos ajudar a investigar enquanto salva dados valiosos em potencial nas capturas. Isso não alterará permanentemente o arquivo de captura e, para alterar ou limpar o filtro de nossa saída, será necessário executar novamente nosso comando com uma alteração na sintaxe.

## Dicas e truques de interpretação

Usar a opção -S exibirá números de sequência absolutos, que podem ser extremamente longos. Normalmente, o tcpdump exibe números de sequência relativos, que são mais fáceis de rastrear e ler. No entanto, se procurarmos esses valores em outra ferramenta ou log, só encontraremos o pacote com base em números de sequência absolutos. Por exemplo, 13245768092588 para 100.

As opções -v, -X e -e podem ajudá-lo a aumentar a quantidade de dados capturados, enquanto as opções -c, -n, -s, -S e -q podem ajudar a reduzir e modificar a quantidade de dados gravados e visto. 

Muitas opções úteis que podem ser usadas, mas nem sempre são diretamente valiosas para todos, são as opções -A e -l. A mostrará apenas o texto ASCII após a linha do pacote, em vez de ASCII e Hex. L dirá ao tcpdump para enviar pacotes em um modo diferente. L irá alinhar o buffer em vez de agrupar e empurrar em pedaços. Ele nos permite enviar a saída diretamente para outra ferramenta, como grep, usando um pipe |.

```shell-session
casluxd@htb[/htb]$sudo tcpdump -Ar telnet.pcap

21:12:43.528695 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 157:217, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234022], length 60
E..p;...@..p..............c.......C........
.%.6..(.Last login: Sat Nov 27 20:11:43 on ttyp2 from bam.zing.org

21:12:43.546441 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 217, win 32120, options [nop,nop,TS val 10234152 ecr 2467382], length 0
E..4FP@.@.s...................d...}x.......
..)(.%.6
21:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 217:705, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF) #4: Tue Oct 12 20:42:32 CDT 1999

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.


21:12:43.566442 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 705, win 32120, options [nop,nop,TS val 10234154 ecr 2467382], length 0
E..4FQ@.@.s...................e...}x.0.....
..)*.%.6
```

Observe como ele tem os valores ASCII mostrados abaixo de cada linha de saída devido ao uso de -A. Isso pode ser útil ao procurar rapidamente algo legível por humanos na saída.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -Ar http.cap -l | grep 'mailto:*'

reading from file http.cap, link-type EN10MB (Ethernet), snapshot length 65535
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>
  <a href="mailto:free-support[AT]thewrittenword.com">free-support[AT]thewrittenword.com</a>
  <a href="mailto:ethereal-users[AT]ethereal.com">ethereal-users[AT]ethereal.com</a>
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>
```

Usar -l dessa maneira nos permitiu examinar a captura rapidamente e buscar palavras-chave ou formatação que suspeitávamos que pudessem estar lá. Nesse caso, usamos o -l para passar a saída para grep e procurar por qualquer instância da frase `mailto:*.` Isso nos mostra todas as linhas com nossa pesquisa, e podemos ver os resultados acima. Usar modificadores e redirecionar a saída pode ser uma maneira rápida de rastrear sites em busca de endereços de e-mail, padrões de nomenclatura e muito mais. 

Podemos cavar o quanto quisermos nos pacotes que capturamos. Requer um pouco de conhecimento de como os protocolos são estruturados, no entanto. Por exemplo, se quiséssemos ver apenas pacotes com o sinalizador TCP SYN definido, poderíamos usar o seguinte comando:

```shell-session
casluxd@htb[/htb]$ tcpdump -i eth0 'tcp[13] &2 != 0'
```

Isso está contando até o 13º byte na estrutura e olhando para o 2º bit. Se for definido como 1 ou ON, o sinalizador SYN é definido.

```shell-session
casluxd@htb[/htb]$ sudo tcpdump -i eth0 'tcp[13] &2 != 0'

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
15:18:14.630993 IP 172.16.146.2.56244 > 172.67.1.1.https: Flags [S], seq 122498858, win 64240, options [mss 1460,sackOK,TS val 534699017 ecr 0,nop,wscale 7], length 0
15:18:14.654698 IP 172.67.1.1.https > 172.16.146.2.56244: Flags [S.], seq 3728841459, ack 122498859, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.017464 IP 172.16.146.2.60202 > a23-54-168-81.deploy.static.akamaitechnologies.com.https: Flags [S], seq 777468939, win 64240, options [mss 1460,sackOK,TS val 1348555130 ecr 0,nop,wscale 7], length 0
15:18:15.021329 IP 172.16.146.2.49652 > 104.16.88.20.https: Flags [S], seq 1954080833, win 64240, options [mss 1460,sackOK,TS val 274098564 ecr 0,nop,wscale 7], length 0
15:18:15.022640 IP 172.16.146.2.45214 > 104.18.22.52.https: Flags [S], seq 1072203471, win 64240, options [mss 1460,sackOK,TS val 1445124063 ecr 0,nop,wscale 7], length 0
15:18:15.042399 IP 104.18.22.52.https > 172.16.146.2.45214: Flags [S.], seq 215464563, ack 1072203472, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.043646 IP a23-54-168-81.deploy.static.akamaitechnologies.com.https > 172.16.146.2.60202: Flags [S.], seq 1390108870, ack 777468940, win 28960, options [mss 1460,sackOK,TS val 3405787409 ecr 1348555130,nop,wscale 7], length 0
15:18:15.044764 IP 104.16.88.20.https > 172.16.146.2.49652: Flags [S.], seq 2086758283, ack 1954080834, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:16.131983 IP 172.16.146.2.45684 > ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https: Flags [S], seq 4017793011, win 64240, options [mss 1460,sackOK,TS val 933634389 ecr 0,nop,wscale 7], length 0
15:18:16.261855 IP ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.45684: Flags [S.], seq 106675091, ack 4017793012, win 26847, options [mss 1460,sackOK,TS val 12653884 ecr 933634389,nop,wscale 8], length 0
```

Nossos resultados incluem apenas pacotes com o sinalizador TCP SYN definido como vimos acima.

O TCPDump pode ser uma ferramenta poderosa se entendermos nossa rede e como os hosts interagem uns com os outros. Reserve um tempo para entender as estruturas típicas de cabeçalho de protocolo para identificar a anomalia quando chegar a hora. Aqui estão alguns links para aprofundar nossos estudos sobre protocolos padrão e suas estruturas. Com exceção do link da Wikipédia, cada link deve nos levar diretamente ao RFC que estabelece o padrão para cada um.

1. `-i` ou `--interface <interface>`: Especifica a interface de rede que será usada para capturar o tráfego. Por exemplo, `-i eth0` capturará o tráfego na interface de rede chamada "eth0".
2. `-n` ou `--numeric`: Exibe endereços IP e números de porta em formato numérico, em vez de tentar resolvê-los em nomes de host ou serviços.
3. `-c <count>` ou `--count <count>`: Define o número máximo de pacotes a serem capturados antes de encerrar a execução do tcpdump.
4. `-s <snaplen>` ou `--snapshot-length <snaplen>`: Define o comprimento máximo de captura de pacotes em bytes. Isso permite que você limite a quantidade de dados capturados para cada pacote.
5. `-w <file>` ou `--write <file>`: Especifica o arquivo de saída onde os pacotes capturados serão gravados. Você pode usar essa opção para salvar os pacotes em um arquivo para análise posterior.
6. `-r <file>` ou `--read <file>`: Lê pacotes de um arquivo de captura (pcap) em vez de capturar em tempo real. Essa opção permite analisar arquivos pcap existentes usando o tcpdump.
7. `tcp`, `udp`, `icmp`, entre outros: Você pode usar os nomes de protocolo como argumentos para filtrar pacotes com base no protocolo específico. Por exemplo, `tcp` filtrará apenas pacotes TCP.
8. `host <hostname>`: Filtra pacotes que envolvem o endereço IP correspondente ao nome de host fornecido.
9. `port <port>`: Filtra pacotes que envolvem a porta especificada.
10. `-e` ou `--print-ether`: Imprime informações adicionais sobre as camadas Ethernet, como endereços MAC de origem e destino.
11. `-v` ou `--verbose`: Exibe informações detalhadas sobre cada pacote capturado, incluindo cabeçalhos de protocolo e dados.
12. `-X` ou `--print-data`: Imprime os dados hexadecimais e ASCII de cada pacote capturado.
13. `-A` ou `--print-ascii`: Exibe os dados ASCII de cada pacote capturado.
14. `-q` ou `--quiet`: Execução silenciosa, exibindo apenas informações essenciais.
15. `-tttt` ou `--timestamp-precision <precision>`: Exibe os registros de data e hora com alta precisão. A precisão pode ser "us" (microssegundos) ou "ns" (nanossegundos).
16. `-l` ou `--line-buffered`: Realiza a saída em buffer de linha, exibindo cada pacote capturado imediatamente em vez de aguardar o buffer ser preenchido.
17. `-B <buffer_size>` ou `--buffer-size <buffer_size>`: Define o tamanho do buffer em bytes. Isso afeta a quantidade de memória usada pelo tcpdump para armazenar pacotes capturados.
18. `src <source>` ou `dst <destination>`: Filtra pacotes com base no endereço IP de origem ou destino.
19. `net <network>`: Filtra pacotes com base na rede de origem ou destino, usando a notação CIDR.
20. `tcp[tcpflags] <flags>`: Filtra pacotes com base em flags TCP específicos. Por exemplo, `tcp[tcpflags] & tcp-syn != 0` filtra pacotes SYN.



#### Protocol RFC Links

|**Link**|**Description**|
|---|---|
|[IP Protocol](https://tools.ietf.org/html/rfc791)|`RFC 791` describes IP and its functionality.|
|[ICMP Protocol](https://tools.ietf.org/html/rfc792)|`RFC 792` describes ICMP and its functionality.|
|[TCP Protocol](https://tools.ietf.org/html/rfc793)|`RFC 793` describes the TCP protocol and how it functions.|
|[UDP Protocol](https://tools.ietf.org/html/rfc768)|`RFC 768` describes UDP and how it operates.|
|[RFC Quick Links](https://en.wikipedia.org/wiki/List_of_RFCs#Topical_list)|This Wikipedia article contains a large list of protocols tied to the RFC that explains their implementation.|
