Podemos executar enumeração de subdomínio ativa investigando a infraestrutura gerenciada pela organização de destino ou pelos servidores DNS de terceiros que identificamos anteriormente. Nesse caso, a quantidade de tráfego gerado pode levar à detecção de nossas atividades de reconhecimento.

## ZoneTransfers

A transferência de zona é como um servidor DNS secundário recebe informações do servidor DNS primário e as atualiza. A abordagem mestre-escravo é usada para organizar servidores DNS em um domínio, com os escravos recebendo informações DNS atualizadas do DNS mestre. O servidor DNS mestre deve ser configurado para ativar transferências de zona de servidores DNS secundários (embora isso possa estar errado.)

Por exemplo, usaremos o [https://hackertarget.com/zone-transfer/](https://hackertarget.com/zone-transfer/) serviço e domínio `zonetransfer.me` para ter uma ideia das informações que podem ser obtidas através desta técnica.

![[Pasted image 20230702134543.png]]

Uma abordagem manual será o seguinte conjunto de comandos:

#### 1. Identificando servidores de nomes

```shell-session
casluxd@htb[/htb]$ nslookup -type=NS zonetransfer.me

Server:		10.100.0.1
Address:	10.100.0.1#53

Non-authoritative answer:
zonetransfer.me	nameserver = nsztm2.digi.ninja.
zonetransfer.me	nameserver = nsztm1.digi.ninja.
```

1. Execute a transferência de zona usando parâmetros `-type=any` e `-query=AXFR`.

#### 2. Teste para QUALQUER Transferência de Zona AXFR

```shell-session
casluxd@htb[/htb]$ nslookup -type=any -query=AXFR zonetransfer.me nsztm1.digi.ninja

Server:		nsztm1.digi.ninja
Address:	81.4.108.41#53

zonetransfer.me
	origin = nsztm1.digi.ninja
	mail addr = robin.digi.ninja
	serial = 2019100801
	refresh = 172800
	retry = 900
	expire = 1209600
	minimum = 3600
zonetransfer.me	hinfo = "Casio fx-700G" "Windows XP"
zonetransfer.me	text = "google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me	mail exchanger = 0 ASPMX.L.GOOGLE.COM.
zonetransfer.me	mail exchanger = 10 ALT1.ASPMX.L.GOOGLE.COM.
zonetransfer.me	mail exchanger = 10 ALT2.ASPMX.L.GOOGLE.COM.
zonetransfer.me	mail exchanger = 20 ASPMX2.GOOGLEMAIL.COM.
zonetransfer.me	mail exchanger = 20 ASPMX3.GOOGLEMAIL.COM.
zonetransfer.me	mail exchanger = 20 ASPMX4.GOOGLEMAIL.COM.
zonetransfer.me	mail exchanger = 20 ASPMX5.GOOGLEMAIL.COM.
Name:	zonetransfer.me
Address: 5.196.105.14
zonetransfer.me	nameserver = nsztm1.digi.ninja.
zonetransfer.me	nameserver = nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me	text = "6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me	service = 0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me	name = www.zonetransfer.me.
asfdbauthdns.zonetransfer.me	afsdb = 1 asfdbbox.zonetransfer.me.
Name:	asfdbbox.zonetransfer.me
Address: 127.0.0.1
asfdbvolume.zonetransfer.me	afsdb = 1 asfdbbox.zonetransfer.me.
Name:	canberra-office.zonetransfer.me
Address: 202.14.81.230
cmdexec.zonetransfer.me	text = "; ls"
contact.zonetransfer.me	text = "Remember to call or email Pippa on +44 123 4567890 or pippa@zonetransfer.me when making DNS changes"
Name:	dc-office.zonetransfer.me
Address: 143.228.181.132
Name:	deadbeef.zonetransfer.me
Address: dead:beaf::
dr.zonetransfer.me	loc = 53 20 56.558 N 1 38 33.526 W 0.00m 1m 10000m 10m
DZC.zonetransfer.me	text = "AbCdEfG"
email.zonetransfer.me	naptr = 1 1 "P" "E2U+email" "" email.zonetransfer.me.zonetransfer.me.
Name:	email.zonetransfer.me
Address: 74.125.206.26
Hello.zonetransfer.me	text = "Hi to Josh and all his class"
Name:	home.zonetransfer.me
Address: 127.0.0.1
Info.zonetransfer.me	text = "ZoneTransfer.me service provided by Robin Wood - robin@digi.ninja. See http://digi.ninja/projects/zonetransferme.php for more information."
internal.zonetransfer.me	nameserver = intns1.zonetransfer.me.
internal.zonetransfer.me	nameserver = intns2.zonetransfer.me.
Name:	intns1.zonetransfer.me
Address: 81.4.108.41
Name:	intns2.zonetransfer.me
Address: 167.88.42.94
Name:	office.zonetransfer.me
Address: 4.23.39.254
Name:	ipv6actnow.org.zonetransfer.me
Address: 2001:67c:2e8:11::c100:1332
Name:	owa.zonetransfer.me
Address: 207.46.197.32
robinwood.zonetransfer.me	text = "Robin Wood"
rp.zonetransfer.me	rp = robin.zonetransfer.me. robinwood.zonetransfer.me.
sip.zonetransfer.me	naptr = 2 3 "P" "E2U+sip" "!^.*$!sip:customer-service@zonetransfer.me!" .
sqli.zonetransfer.me	text = "' or 1=1 --"
sshock.zonetransfer.me	text = "() { :]}; echo ShellShocked"
staging.zonetransfer.me	canonical name = www.sydneyoperahouse.com.
Name:	alltcpportsopen.firewall.test.zonetransfer.me
Address: 127.0.0.1
testing.zonetransfer.me	canonical name = www.zonetransfer.me.
Name:	vpn.zonetransfer.me
Address: 174.36.59.154
Name:	www.zonetransfer.me
Address: 5.196.105.14
xss.zonetransfer.me	text = "'><script>alert('Boo')</script>"
zonetransfer.me
	origin = nsztm1.digi.ninja
	mail addr = robin.digi.ninja
	serial = 2019100801
	refresh = 172800
	retry = 900
	expire = 1209600
	minimum = 3600
```

Se conseguirmos executar uma transferência de zona bem-sucedida para um domínio, não há necessidade de continuar enumerando esse domínio específico, pois isso extrairá todas as informações disponíveis.

## Gobuster

O gobuster é uma ferramenta que podemos usar para realizar enumeração de subdomínios. É especialmente interessante para nós as opções de padrões, pois aprendemos algumas convenções de nomenclatura na coleta passiva de informações que podemos usar para descobrir novos subdomínios seguindo o mesmo padrão.

Podemos usar uma lista de palavras do repositório [Listas de seleção](https://github.com/danielmiessler/SecLists) junto com `gobuster`se estamos procurando palavras em padrões em vez de números. Lembre-se de que durante nossas atividades passivas de enumeração de subdomínios, encontramos um padrão `lert-api-shv-{NUMBER}-sin6.facebook.com`. Podemos usar esse padrão para descobrir subdomínios adicionais. O primeiro passo será criar um arquivo stands.txt com os padrões descobertos anteriormente, por exemplo:

#### GoBuster - standards.txt

```shell-session
lert-api-shv-{GOBUSTER}-sin6
atlas-pp-shv-{GOBUSTER}-sin6
```

O próximo passo será o lançamento `gobuster` usando o `dns` módulo, especificando as seguintes opções:

- `dns`: Inicie o módulo DNS
- `-q`: Não imprima o banner e outros ruídos.
- `-r`: Use o servidor DNS personalizado
- `-d`: Um nome de domínio de destino
- `-p`: Caminho para o arquivo de padrões
- `-w`: Caminho para a lista de palavras
- `-o`: Arquivo de saída

No nosso caso, este será o comando.

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ export NS="d.ns.facebook.com"
casluxd@htb[/htb]$ export WORDLIST="numbers.txt"
casluxd@htb[/htb]$ gobuster dns -q -r "${NS}" -d "${TARGET}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"

Found: lert-api-shv-01-sin6.facebook.com
Found: atlas-pp-shv-01-sin6.facebook.com
Found: atlas-pp-shv-02-sin6.facebook.com
Found: atlas-pp-shv-03-sin6.facebook.com
Found: lert-api-shv-03-sin6.facebook.com
Found: lert-api-shv-02-sin6.facebook.com
Found: lert-api-shv-04-sin6.facebook.com
Found: atlas-pp-shv-04-sin6.facebook.com
```

Agora podemos ver uma lista de subdomínios aparecendo enquanto o Gobuster está realizando as verificações de enumeração.