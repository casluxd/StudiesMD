As informações de domínio são um componente essencial de qualquer teste de penetração, e não se trata apenas dos subdomínios, mas de toda a presença na Internet. Portanto, coletamos informações e tentamos entender a funcionalidade da empresa e quais tecnologias e estruturas são necessárias para que os serviços sejam oferecidos com sucesso e eficiência.

Esse tipo de informação é coletado passivamente sem verificações diretas e ativas. Em outras palavras, permanecemos ocultos e navegamos como "clientes" ou "visitantes" para evitar conexões diretas com a empresa que poderiam nos expor. As seções relevantes do OSINT são apenas uma pequena parte de quão profundo o OSINT vai e descrevem apenas algumas das muitas maneiras de obter informações dessa maneira. 

No entanto, quando coletando informações `passivamente`, podemos usar serviços de terceiros para entender melhor a empresa. No entanto, a primeira coisa que devemos fazer é examinar as informações da empresa no `site principal`. Em seguida, devemos ler os textos, tendo em mente quais tecnologias e estruturas são necessárias para esses serviços.

Por exemplo, muitas empresas de TI oferecem serviços de desenvolvimento de aplicativos, IoT, hospedagem, ciência de dados e segurança de TI, dependendo do setor. Se encontrarmos um serviço com o qual pouco tivemos a ver antes, faz sentido e é necessário lidar com isso e descobrir em que atividades consiste e quais oportunidades estão disponíveis. Esses serviços também nos fornecem uma boa visão geral de como a empresa pode ser estruturada.

Por exemplo, esta parte é a combinação entre o `primeiro princípio` e o `segundo princípio` de enumeração. Prestamos atenção ao que `nós vemos` e ao que `nós não vemos`. Vemos os serviços, mas não a funcionalidade deles. No entanto, os serviços estão sujeitos a certos aspectos técnicos necessários para prestar um serviço. Portanto, adotamos a visão do desenvolvedor e analisamos a coisa toda do ponto de vista deles. Esse ponto de vista nos permite obter muitas informações técnicas sobre a funcionalidade.

## Presença Online

Depois de termos um entendimento básico da empresa e de seus serviços, podemos ter uma primeira impressão de sua presença na Internet. Vamos supor que uma empresa de médio porte nos contratou para testar toda a infraestrutura de uma perspectiva de caixa preta. Isso significa que recebemos apenas um escopo de metas e devemos obter todas as informações adicionais.

`Nota: Lembre-se de que os exemplos abaixo diferem dos exercícios práticos e não fornecerão os mesmos resultados. No entanto, os exemplos são baseados em testes de penetração real e ilustram como e quais informações podem ser obtidas.`

O primeiro ponto de presença na Internet pode ser o `SSL certificate` do site principal da empresa que podemos examinar. Freqüentemente, esse certificado inclui mais do que apenas um subdomínio, e isso significa que o certificado é usado para vários domínios, e estes provavelmente ainda estão ativos.

![[Pasted image 20230618161632.png]]

Outra fonte para encontrar mais subdomínios é [crt.sh](https://crt.sh/). Esta fonte é [Transparência do certificado](https://en.wikipedia.org/wiki/Certificate_Transparency) toras. Transparência de certificado é um processo que visa permitir a verificação de certificados digitais emitidos para conexões criptografadas da Internet. O padrão ([RFC 6962](https://tools.ietf.org/html/rfc6962)) prevê o registro de todos os certificados digitais emitidos por uma autoridade de certificação em logs à prova de auditoria. O objetivo é permitir a detecção de certificados falsos ou maliciosamente emitidos para um domínio. Provedores de certificados SSL como [Let's Encrypt](https://letsencrypt.org/) compartilhe isso com a interface da web [crt.sh](https://crt.sh/), que armazena as novas entradas no banco de dados a serem acessadas posteriormente.

![[Pasted image 20230618161837.png]]

Também podemos produzir os resultados no formato JSON.

```shell-session
casluxd@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .

[
  {
    "issuer_ca_id": 23451835427,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 50815783237226155,
    "entry_timestamp": "2021-08-21T06:00:17.173",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe9017d6de5eda90"
  },
  {
    "issuer_ca_id": 6864563267,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 5081529377,
    "entry_timestamp": "2021-08-21T06:00:16.932",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe90104e271c98a90"
  },
  {
    "issuer_ca_id": 113123452,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "smartfactory.inlanefreight.com",
    "name_value": "smartfactory.inlanefreight.com",
    "id": 4941235512141012357,
    "entry_timestamp": "2021-07-27T00:32:48.071",
    "not_before": "2021-07-26T23:32:47",
    "not_after": "2021-10-24T23:32:45",
    "serial_number": "044bac5fcc4d59329ecbbe9043dd9d5d0878"
  },
  { ... SNIP ...
```

Se necessário, também podemos filtrá-los pelos subdomínios únicos.

```shell-session
casluxd@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

account.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
data.ttn.inlanefreight.com
*.inlanefreight.com
inlanefreight.com
integrations.ttn.inlanefreight.com
iot.inlanefreight.com
mails.inlanefreight.com
marina.inlanefreight.com
marina-live.inlanefreight.com
matomo.inlanefreight.com
next.inlanefreight.com
noc.ttn.inlanefreight.com
preview.inlanefreight.com
shop.inlanefreight.com
smartfactory.inlanefreight.com
ttn.inlanefreight.com
vx.inlanefreight.com
www.inlanefreight.com
```

Em seguida, podemos identificar os hosts diretamente acessíveis pela Internet e não hospedados por terceiros. Isso ocorre porque não temos permissão para testar os hosts sem a permissão de terceiros.

```shell-session
casluxd@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Depois de ver quais hosts podem ser investigados mais, podemos gerar uma lista de endereços IP com um pequeno ajuste no `cut` comando e execute-os através `Shodan`.

[Shodan](https://www.shodan.io/) pode ser usado para encontrar dispositivos e sistemas permanentemente conectados à Internet, como `Internet of Things` (`IoT`). Ele pesquisa na Internet por portas TCP / IP abertas e filtra os sistemas de acordo com termos e critérios específicos. Por exemplo, abra portas HTTP ou HTTPS e outras portas do servidor para `FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`, ou `SIP` são revistados. Como resultado, podemos encontrar dispositivos e sistemas, como `surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights` e `traffic controllers`, e vários componentes de rede.

#### Shodan - Lista IP

```shell-session
casluxd@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
casluxd@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done

10.129.24.93
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T09:02:11.370085
Number of open ports:    2

Ports:
     80/tcp nginx 
    443/tcp nginx 
	
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
				
10.129.27.22
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T15:39:55.446281
Number of open ports:    8

Ports:
     25/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
     53/tcp  
     53/udp  
     80/tcp Apache httpd 
     81/tcp Apache httpd 
    110/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2
    111/tcp  
    443/tcp Apache httpd 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
                Fingerprint:   RFC3526/Oakley Group 14
    444/tcp  
		
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
```

Lembramos do IP `10.129.27.22` (`matomo.inlanefreight.com`) para investigações ativas posteriores que queremos realizar. Agora, podemos exibir todos os registros DNS disponíveis, onde podemos encontrar mais hosts.

#### Registros DNS

```shell-session
casluxd@htb[/htb]$ dig any inlanefreight.com

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52058
;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;inlanefreight.com.             IN      ANY

;; ANSWER SECTION:
inlanefreight.com.      300     IN      A       10.129.27.33
inlanefreight.com.      300     IN      A       10.129.95.250
inlanefreight.com.      3600    IN      MX      1 aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      10 aspmx2.googlemail.com.
inlanefreight.com.      3600    IN      MX      10 aspmx3.googlemail.com.
inlanefreight.com.      3600    IN      MX      5 alt1.aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      5 alt2.aspmx.l.google.com.
inlanefreight.com.      21600   IN      NS      ns.inwx.net.
inlanefreight.com.      21600   IN      NS      ns2.inwx.net.
inlanefreight.com.      21600   IN      NS      ns3.inwx.eu.
inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"
inlanefreight.com.      21600   IN      TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
inlanefreight.com.      300     IN      TXT     "google-site-verification=bow47-er9LdgoUeah"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=gZsCG-BINLopf4hr2"
inlanefreight.com.      3600    IN      TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
inlanefreight.com.      300     IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
inlanefreight.com.      21600   IN      SOA     ns.inwx.net. hostmaster.inwx.net. 2021072600 10800 3600 604800 3600

;; Query time: 332 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Mi Sep 01 18:27:22 CEST 2021
;; MSG SIZE  rcvd: 940
```

Vejamos o que aprendemos aqui e voltemos aos nossos princípios. Vemos um registro IP, alguns servidores de correio, alguns servidores DNS, registros TXT e um registro SOA.

- registros A: reconhecemos os endereços IP que apontam para um domínio específico ( sub ) através do registro A. Aqui vemos apenas um que já conhecemos.
- registros MX: os registros do servidor de correio nos mostram qual servidor de correio é responsável pelo gerenciamento dos emails da empresa. Como isso é tratado pelo google no nosso caso, devemos observar isso e ignorá-lo por enquanto.
- registros NS: esses tipos de registros mostram quais servidores de nomes são usados para resolver os endereços FQDN para IP. A maioria dos provedores de hospedagem usa seus próprios servidores de nomes, facilitando a identificação do provedor de hospedagem.
- registros TXT: esse tipo de registro geralmente contém chaves de verificação para diferentes provedores terceirizados e outros aspectos de segurança do DNS, como [SPF](https://datatracker.ietf.org/doc/html/rfc7208), [DMARC](https://datatracker.ietf.org/doc/html/rfc7489), e [DKIM](https://datatracker.ietf.org/doc/html/rfc6376), responsáveis por verificar e confirmar a origem dos e-mails enviados. Aqui já podemos ver algumas informações valiosas se olharmos mais de perto os resultados.

```shell-session
...SNIP... TXT     "MS=ms92346782372"
...SNIP... TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
...SNIP... TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
...SNIP... TXT     "google-site-verification=bow47-er9LdgoUeah"
...SNIP... TXT     "google-site-verification=gZsCG-BINLopf4hr2"
...SNIP... TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
...SNIP... TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
```

O que pudemos ver até agora foram entradas no servidor DNS, que à primeira vista não pareciam muito interessantes (, exceto nos endereços IP adicionais ). No entanto, não pudemos ver os provedores terceirizados por trás das entradas mostradas à primeira vista. A principal informação que podemos ver agora é:

- ATLASSIAN, GOOGLE GMAIL, LOGMELN, MAILGUN, OUTLOOK, INWX, 10.129.248.8, 10.129.27.2, 10.72.82.106
- Por exemplo, [Atlassian](https://www.atlassian.com/) afirma que a empresa usa essa solução para desenvolvimento e colaboração de software. Se não estivermos familiarizados com esta plataforma, podemos experimentá-la gratuitamente para se familiarizar com ela.
- [Google Gmail](https://www.google.com/gmail/) indica que o Google é usado para gerenciamento de email. Portanto, também pode sugerir que poderíamos acessar pastas ou arquivos abertos do GDrive com um link.
- [LogMeIn](https://www.logmein.com/) é um local central que regula e gerencia o acesso remoto em muitos níveis diferentes. No entanto, a centralização de tais operações é uma faca de dois gumes. Se o acesso como administrador a esta plataforma for obtido ( por exemplo, através da reutilização de senha ), também será possível acessar completamente todos os sistemas e informações.
- [Mailgun](https://www.mailgun.com/) oferece várias APIs de email, relés SMTP e webhooks com os quais os emails podem ser gerenciados. Isso nos diz para manter os olhos abertos para interfaces de API que podemos testar para várias vulnerabilidades, como solicitações IDOR, SSRF, POST, PUT e muitos outros ataques.
- [Outlook](https://outlook.live.com/owa/) é outro indicador para gerenciamento de documentos. As empresas costumam usar o Office 365 com o OneDrive e recursos da nuvem, como blob do Azure e armazenamento de arquivos. O armazenamento de arquivos do Azure pode ser muito interessante porque funciona com o protocolo SMB.
- A última coisa que vemos é [INWX](https://www.inwx.com/en). Esta empresa parece ser um provedor de hospedagem onde os domínios podem ser comprados e registrados. O registro TXT com o valor "MS" é frequentemente usado para confirmar o domínio. Na maioria dos casos, é semelhante ao nome de usuário ou ID usado para fazer login na plataforma de gerenciamento.
