Podemos começar a analisar mais detalhadamente os dados para identificar destinos específicos agora que temos algumas informações sobre nosso objetivo. O [Sistema de nomes de domínio](https://en.wikipedia.org/wiki/Domain_Name_System) (`DNS`) é um excelente lugar para procurar esse tipo de informação. Mas primeiro, vamos dar uma olhada no que é o DNS.

## O que é DNS?

O DNS é a lista telefônica da Internet. Nomes de domínio, como `hackthebox.com` e `inlanefreight.com` permitem que as pessoas acessem conteúdo na Internet. Protocolo da Internet (Endereços `IP` ) são usados para se comunicar entre navegadores da web. O DNS converte nomes de domínio em endereços IP, permitindo que os navegadores acessem recursos na Internet.

Cada dispositivo conectado à Internet possui um endereço IP exclusivo que outras máquinas usam para localizá-lo. Os servidores DNS minimizam a necessidade de as pessoas aprenderem endereços IP como `104.17.42.72` em `IPv4` ou endereços IP alfanuméricos modernos mais sofisticados, como `2606:4700::6811:2b48` em `IPv6`. Quando um usuário digita `www.facebook.com` no navegador da web, deve ocorrer uma tradução entre o que o usuário digita e o endereço IP necessário para alcançar o `www.facebook.com` página da web.

Algumas das vantagens do uso do DNS são:

- Ele permite que nomes sejam usados em vez de números para identificar hosts.
- É muito mais fácil lembrar um nome do que recordar um número.
- Apenas redirecionando um nome para o novo endereço numérico, um servidor pode alterar endereços numéricos sem precisar notificar todos na Internet.
- Um único nome pode se referir a vários hosts que dividem a carga de trabalho entre diferentes servidores.

Há uma hierarquia de nomes na estrutura do DNS. A raiz do sistema, ou nível mais alto, não tem nome.

Os servidores de nomes de TLDs, os domínios de nível superior, podem ser comparados a uma única prateleira de livros em uma biblioteca. A última parte de um nome de host é hospedada por esse servidor de nomes, que é o estágio seguinte na busca por um endereço IP específico ( em `www.facebook.com`, o servidor TLD é `com`). A maioria dos TLDs foi delegada a gerentes de países individuais, que recebem códigos do [Tabela ISO-3166-1](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes). Eles são conhecidos como domínios de nível superior com código de país ou ccTLDs gerenciados por uma agência das Nações Unidas.

Há também um pequeno número de domínios de nível superior "genéricos" ( gTLDs ) que não estão associados a um país ou região específica. Os gerentes de TLD receberam a responsabilidade por procedimentos e políticas para a atribuição de nomes de domínio de segundo nível ( SLDs ) e hierarquias de nomes de nível inferior, de acordo com o conselho de política especificado na ISO-3166-1.

Um gerente para cada nação organiza domínios de código de país. Esses gerentes fornecem um serviço público em nome da comunidade da Internet. Registros de recursos são os resultados das consultas DNS e têm a seguinte estrutura:

|   |   |
|---|---|
|`Resource Record`|Um nome de domínio, geralmente um nome de domínio totalmente qualificado, é a primeira parte de um Registro de Recursos. Se você não usar um nome de domínio totalmente qualificado, o nome da zona em que o registro está localizado será anexado ao final do nome.|
|`TTL`|Em segundos, o tempo de vida (`TTL`) é padronizado para o valor mínimo especificado no registro SOA.|
|`Record Class`|Internet, Hesíodo ou Caos|
|`Start Of Authority` (`SOA`)|Deve ser o primeiro em um arquivo de zona porque indica o início de uma zona. Cada zona pode ter apenas um `SOA` e, adicionalmente, contém os valores da zona, como um número de série e vários intervalos de expiração.|
|`Name Servers` (`NS`)|O banco de dados distribuído é vinculado por registros `NS` . Eles são responsáveis pelo servidor de nomes autorizado de uma zona e pela autoridade de uma zona filho para um servidor de nomes.|
|`IPv4 Addresses` (`A`)|O registro A é apenas um mapeamento entre um nome de host e um endereço IP. 'Zonas 'forward' são aquelas com registros `A`.|
|`Pointer` (`PTR`)|O registro PTR é um mapeamento entre um endereço IP e um nome de host. 'Zonas 'reversas' são aquelas que têm registros `PTR`.|
|`Canonical Name` (`CNAME`)|Um nome de host de alias é mapeado para `A` gravar um nome do host usando o registro `CNAME`.|
|`Mail Exchange` (`MX`)|O registro `MX` identifica um host que aceita e-mails para um host específico. Um valor de prioridade foi atribuído ao host especificado. Vários registros MX podem existir no mesmo host e uma lista priorizada é feita consistindo nos registros de um host específico.|

## Nslookup & DIG

Agora que temos um entendimento claro do que é o DNS, vamos dar uma olhada no `Nslookup` utilitário de linha de comando. Vamos supor que um cliente nos solicitou para realizar um teste de penetração externa. Portanto, primeiro precisamos nos familiarizar com sua infraestrutura e identificar quais hosts são acessíveis ao público. Podemos descobrir isso usando diferentes tipos de solicitações de DNS. Com `Nslookup`, podemos procurar servidores de nomes de domínio na Internet e solicitar informações sobre hosts e domínios. Embora a ferramenta tenha dois modos, interativo e não interativo, focaremos principalmente no módulo não interativo.

Podemos consultar o registro `A`  apenas enviando um nome de domínio. Mas também podemos usar o `-query` parâmetro para pesquisar registros de recursos específicos. Alguns exemplos são:

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ nslookup $TARGET

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
Name:	facebook.com
Address: 31.13.92.36
Name:	facebook.com
Address: 2a03:2880:f11c:8083:face:b00c:0:25de
```

Também podemos especificar um servidor de nomes, se necessário, adicionando `@<nameserver/IP>` para o comando. Ao contrário de nslookup, `DIG` mostra-nos mais algumas informações que podem ser importantes.

```shell-session
casluxd@htb[/htb]$ dig facebook.com @1.1.1.1

; <<>> DiG 9.16.1-Ubuntu <<>> facebook.com @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58899
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;facebook.com.                  IN      A

;; ANSWER SECTION:
facebook.com.           169     IN      A       31.13.92.36

;; Query time: 20 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mo Okt 18 16:03:17 CEST 2021
;; MSG SIZE  rcvd: 57
```

A entrada começa com o nome de domínio completo, incluindo o ponto final. A entrada pode ser mantida no cache por `169` segundos antes que as informações sejam solicitadas novamente. A classe é compreensivelmente a Internet (`IN`).

#### Consulta: Um registro para um subdomínio

```shell-session
casluxd@htb[/htb]$ export TARGET=www.facebook.com
casluxd@htb[/htb]$ nslookup -query=A $TARGET

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
www.facebook.com	canonical name = star-mini.c10r.facebook.com.
Name:	star-mini.c10r.facebook.com
Address: 31.13.92.36
```

```shell-session
casluxd@htb[/htb]$ dig a www.facebook.com @1.1.1.1

; <<>> DiG 9.16.1-Ubuntu <<>> a www.facebook.com @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15596
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;www.facebook.com.              IN      A

;; ANSWER SECTION:
www.facebook.com.       3585    IN      CNAME   star-mini.c10r.facebook.com.
star-mini.c10r.facebook.com. 45 IN      A       31.13.92.36

;; Query time: 16 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mo Okt 18 16:11:48 CEST 2021
;; MSG SIZE  rcvd: 90
```

#### Consultoria: registros PTR para um endereço IP

```shell-session
casluxd@htb[/htb]$ nslookup -query=PTR 31.13.92.36

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
36.92.13.31.in-addr.arpa	name = edge-star-mini-shv-01-frt3.facebook.com.

Authoritative answers can be found from:
```

```shell-session
casluxd@htb[/htb]$ dig -x 31.13.92.36 @1.1.1.1

; <<>> DiG 9.16.1-Ubuntu <<>> -x 31.13.92.36 @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51730
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;36.92.13.31.in-addr.arpa.      IN      PTR

;; ANSWER SECTION:
36.92.13.31.in-addr.arpa. 1028  IN      PTR     edge-star-mini-shv-01-frt3.facebook.com.

;; Query time: 16 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mo Okt 18 16:14:20 CEST 2021
;; MSG SIZE  rcvd: 106
```

#### Consulta: ANY Registro Existente

Neste exemplo, estamos usando o Google como exemplo, em vez do Facebook, pois o último não respondeu à nossa consulta.

```shell-session
casluxd@htb[/htb]$ export TARGET="google.com"
casluxd@htb[/htb]$ nslookup -query=ANY $TARGET

Server:		10.100.0.1
Address:	10.100.0.1#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.16.142
Name:	google.com
Address: 2a00:1450:4001:808::200e
google.com	text = "docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com	text = "docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com	text = "v=spf1 include:_spf.google.com ~all"
google.com	text = "MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com	text = "globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
google.com	text = "apple-domain-verification=30afIBcvSuDV2PLX"
google.com	text = "google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com	text = "facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
google.com	text = "google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com	nameserver = ns3.google.com.
google.com	nameserver = ns2.google.com.
google.com	nameserver = ns1.google.com.
google.com	nameserver = ns4.google.com.
google.com	mail exchanger = 10 aspmx.l.google.com.
google.com	mail exchanger = 40 alt3.aspmx.l.google.com.
google.com	mail exchanger = 20 alt1.aspmx.l.google.com.
google.com	mail exchanger = 30 alt2.aspmx.l.google.com.
google.com	mail exchanger = 50 alt4.aspmx.l.google.com.
google.com
	origin = ns1.google.com
	mail addr = dns-admin.google.com
	serial = 398195569
	refresh = 900
	retry = 900
	expire = 1800
	minimum = 60
google.com	rdata_257 = 0 issue "pki.goog"

Authoritative answers can be found from:
```

```shell-session
casluxd@htb[/htb]$ dig any google.com @8.8.8.8

; <<>> DiG 9.16.1-Ubuntu <<>> any google.com @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 49154
;; flags: qr rd ra; QUERY: 1, ANSWER: 22, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      ANY

;; ANSWER SECTION:
google.com.             249     IN      A       142.250.184.206
google.com.             249     IN      AAAA    2a00:1450:4001:830::200e
google.com.             549     IN      MX      10 aspmx.l.google.com.
google.com.             3549    IN      TXT     "apple-domain-verification=30afIBcvSuDV2PLX"
google.com.             3549    IN      TXT     "facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
google.com.             549     IN      MX      20 alt1.aspmx.l.google.com.
google.com.             3549    IN      TXT     "docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com.             3549    IN      TXT     "v=spf1 include:_spf.google.com ~all"
google.com.             3549    IN      TXT     "globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
google.com.             3549    IN      TXT     "google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com.             9       IN      SOA     ns1.google.com. dns-admin.google.com. 403730046 900 900 1800 60
google.com.             21549   IN      NS      ns1.google.com.
google.com.             21549   IN      NS      ns3.google.com.
google.com.             549     IN      MX      50 alt4.aspmx.l.google.com.
google.com.             3549    IN      TXT     "docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com.             549     IN      MX      30 alt2.aspmx.l.google.com.
google.com.             21549   IN      NS      ns2.google.com.
google.com.             21549   IN      NS      ns4.google.com.
google.com.             549     IN      MX      40 alt3.aspmx.l.google.com.
google.com.             3549    IN      TXT     "MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com.             3549    IN      TXT     "google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com.             21549   IN      CAA     0 issue "pki.goog"

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mo Okt 18 16:15:22 CEST 2021
;; MSG SIZE  rcvd: 922
```

O mais recente [RFC8482](https://tools.ietf.org/html/rfc8482) especificou que Os pedidos de DNS `ANY` são abolidos. Portanto, podemos não receber uma resposta à nossa solicitação `ANY` ao servidor DNS ou faça uma referência ao referido RFC8482.

```shell-session
casluxd@htb[/htb]$ dig any cloudflare.com @8.8.8.8

; <<>> DiG 9.16.1-Ubuntu <<>> any cloudflare.com @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 22509
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;cloudflare.com.                        IN      ANY

;; ANSWER SECTION:
cloudflare.com.         2747    IN      HINFO   "RFC8482" ""
cloudflare.com.         2747    IN      RRSIG   HINFO 13 2 3789 20211019145905 20211017125905 34505 cloudflare.com. 4/Bq8xUN96SrOhuH0bj2W6s2pXRdv5L5NWsgyTAGLAjEwwEF4y4TQuXo yGtvD3B13jr5KhdXo1VtrLLMy4OR8Q==

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mo Okt 18 16:16:27 CEST 2021
;; MSG SIZE  rcvd: 174
```

#### Consulta: TXT Records

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ nslookup -query=TXT $TARGET

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
facebook.com	text = "v=spf1 redirect=_spf.facebook.com"
facebook.com	text = "google-site-verification=A2WZWCNQHrGV_TWwKh6KHY90tY0SHZo_RnyMJoDaG0s"
facebook.com	text = "google-site-verification=wdH5DTJTc9AYNwVunSVFeK0hYDGUIEOGb-RReU6pJlY"

Authoritative answers can be found from:
```

```shell-session
casluxd@htb[/htb]$ dig txt facebook.com @1.1.1.1

; <<>> DiG 9.16.1-Ubuntu <<>> txt facebook.com @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 63771
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;facebook.com.                  IN      TXT

;; ANSWER SECTION:
facebook.com.           86400   IN      TXT     "v=spf1 redirect=_spf.facebook.com"
facebook.com.           7200    IN      TXT     "google-site-verification=A2WZWCNQHrGV_TWwKh6KHY90tY0SHZo_RnyMJoDaG0s"
facebook.com.           7200    IN      TXT     "google-site-verification=wdH5DTJTc9AYNwVunSVFeK0hYDGUIEOGb-RReU6pJlY"

;; Query time: 24 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mo Okt 18 16:17:46 CEST 2021
;; MSG SIZE  rcvd: 249
```

#### Consulta: MX Records

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ nslookup -query=MX $TARGET

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
facebook.com	mail exchanger = 10 smtpin.vvv.facebook.com.

Authoritative answers can be found from:
```

```shell-session
casluxd@htb[/htb]$ dig mx facebook.com @1.1.1.1

; <<>> DiG 9.16.1-Ubuntu <<>> mx facebook.com @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 9392
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;facebook.com.                  IN      MX

;; ANSWER SECTION:
facebook.com.           3600    IN      MX      10 smtpin.vvv.facebook.com.

;; Query time: 40 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mo Okt 18 16:18:22 CEST 2021
;; MSG SIZE  rcvd: 68
```

Até agora, nos reunimos registros `A`, `NS`, `MX`, e `CNAME` com o comandos `nslookup` e `dig` . As organizações recebem endereços IP na Internet, mas nem sempre são seus proprietários. Eles podem confiar em `ISP`s e hospedagem fornece que arrendam blocos de rede menores para eles.

Podemos combinar alguns dos resultados coletados via nslookup com o banco de dados whois para determinar se nossa organização de destino usa provedores de hospedagem. Essa combinação se parece com o seguinte exemplo:

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ nslookup $TARGET

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
Name:	facebook.com
Address: 157.240.199.35
Name:	facebook.com
Address: 2a03:2880:f15e:83:face:b00c:0:25de
```

```shell-session
casluxd@htb[/htb]$ whois 157.240.199.35

NetRange:       157.240.0.0 - 157.240.255.255
CIDR:           157.240.0.0/16
NetName:        THEFA-3
NetHandle:      NET-157-240-0-0-1
Parent:         NET157 (NET-157-0-0-0-0)
NetType:        Direct Assignment
OriginAS:
Organization:   Facebook, Inc. (THEFA-3)
RegDate:        2015-05-14
Updated:        2015-05-14
Ref:            https://rdap.arin.net/registry/ip/157.240.0.0



OrgName:        Facebook, Inc.
OrgId:          THEFA-3
Address:        1601 Willow Rd.
City:           Menlo Park
StateProv:      CA
PostalCode:     94025
Country:        US
RegDate:        2004-08-11
Updated:        2012-04-17
Ref:            https://rdap.arin.net/registry/entity/THEFA-3


OrgAbuseHandle: OPERA82-ARIN
OrgAbuseName:   Operations
OrgAbusePhone:  +1-650-543-4800
OrgAbuseEmail:  domain@facebook.com
OrgAbuseRef:    https://rdap.arin.net/registry/entity/OPERA82-ARIN

OrgTechHandle: OPERA82-ARIN
OrgTechName:   Operations
OrgTechPhone:  +1-650-543-4800
OrgTechEmail:  domain@facebook.com
OrgTechRef:    https://rdap.arin.net/registry/entity/OPERA82-ARIN
```
