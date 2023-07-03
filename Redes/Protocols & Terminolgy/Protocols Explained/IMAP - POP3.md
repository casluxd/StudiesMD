Com a ajuda do `Internet Message Access Protocol` (`IMAP`), é possível acessar e-mails de um servidor de e-mail. Ao contrário do `Post Office Protocol` (`POP3`), o IMAP permite o gerenciamento on-line de e-mails diretamente no servidor e suporta estruturas de pastas. Portanto, é um protocolo de rede para o gerenciamento on-line de emails em um servidor remoto. O protocolo é baseado no cliente-servidor e permite a sincronização de um cliente de email local com a caixa de correio no servidor, fornecendo um tipo de sistema de arquivos de rede para emails, permitindo a sincronização sem problemas em vários clientes independentes. O POP3, por outro lado, não possui a mesma funcionalidade que o IMAP e fornece apenas listagem, recuperação e exclusão de emails como funções no servidor de email. Portanto, protocolos como o IMAP devem ser usados para funcionalidades adicionais, como caixas de correio hierárquicas diretamente no servidor de correio, acesso a várias caixas de correio durante uma sessão e pré-seleção de emails.

Os clientes acessam essas estruturas on-line e podem criar cópias locais. Mesmo em vários clientes, isso resulta em um banco de dados uniforme. Os e-mails permanecem no servidor até serem excluídos. O IMAP é baseado em texto e possui funções estendidas, como navegar por e-mails diretamente no servidor. Também é possível que vários usuários acessem o servidor de email simultaneamente. Sem uma conexão ativa com o servidor, é impossível gerenciar e-mails. No entanto, alguns clientes oferecem um modo offline com uma cópia local da caixa de correio. O cliente sincroniza todas as alterações locais offline quando uma conexão é restabelecida.

O cliente estabelece a conexão com o servidor via porta `143`. Para comunicação, ele usa comandos baseados em texto em formato `ASCII`. Vários comandos podem ser enviados em sucessão sem aguardar a confirmação do servidor. Confirmações posteriores do servidor podem ser atribuídas aos comandos individuais usando os identificadores enviados junto com os comandos. Imediatamente após a conexão ser estabelecida, o usuário é autenticado pelo nome de usuário e senha no servidor. O acesso à caixa de correio desejada só é possível após a autenticação bem-sucedida.

O SMTP geralmente é usado para enviar e-mails. Ao copiar e-mails enviados para uma pasta IMAP, todos os clientes têm acesso a todos os e-mails enviados, independentemente do computador do qual foram enviados. Outra vantagem do Protocolo de Acesso a Mensagens da Internet é criar pastas pessoais e estruturas de pastas na caixa de correio. Esse recurso torna a caixa de correio mais clara e fácil de gerenciar. No entanto, o requisito de espaço de armazenamento no servidor de email aumenta.

Sem outras medidas, o IMAP funciona sem criptografia e transmite comandos, emails ou nomes de usuário e senhas em texto sem formatação. Muitos servidores de email exigem o estabelecimento de uma sessão IMAP criptografada para garantir maior segurança no tráfego de email e impedir o acesso não autorizado às caixas de correio. SSL / TLS é geralmente usado para esse fim. Dependendo do método e da implementação utilizados, a conexão criptografada usa a porta padrão `143` ou uma porta alternativa, como `993`.

## Configuração padrão

O IMAP e o POP3 têm um grande número de opções de configuração, dificultando o mergulho profundo em cada componente com mais detalhes. Se você deseja examinar essas configurações de protocolo mais profundamente, recomendamos a criação de uma VM localmente e instale os dois pacotes `dovecot-imapd`, e `dovecot-pop3d` usando `apt` e brinque com as configurações e experimente.

Na documentação de Dovecot, podemos encontrar opções individuais de [configurações principais](https://doc.dovecot.org/settings/core/) e [configuração de serviço](https://doc.dovecot.org/configuration_manual/service_configuration/) que podem ser utilizadas para nossos experimentos. No entanto, vejamos a lista de comandos e veja como podemos interagir e nos comunicar diretamente com o IMAP e o POP3 usando a linha de comando.

#### Comandos IMAP

|**Comando**|**Descrição**|
|---|---|
|`1 LOGIN username password`|Login do usuário.|
|`1 LIST "" *`|Lista todos os diretórios.|
|`1 CREATE "INBOX"`|Cria uma caixa de correio com um nome especificado.|
|`1 DELETE "INBOX"`|Exclui uma caixa de correio.|
|`1 RENAME "ToRead" "Important"`|Renomea uma caixa de correio.|
|`1 LSUB "" *`|Retorna um subconjunto de nomes do conjunto de nomes que o Usuário declarou como sendo `active` ou `subscribed`.|
|`1 SELECT INBOX`|Seleciona uma caixa de correio para que as mensagens na caixa de correio possam ser acessadas.|
|`1 UNSELECT INBOX`|Sai da caixa de correio selecionada.|
|`1 FETCH <ID> all`|Recupera dados associados a uma mensagem na caixa de correio.|
|`1 CLOSE`|Remove todas as mensagens com o `Deleted` sinalizador definido.|
|`1 LOGOUT`|Fecha a conexão com o servidor IMAP.|

#### Comandos POP3

|**Comando**|**Descrição**|
|---|---|
|`USER username`|Identifica o usuário.|
|`PASS password`|Autenticação do usuário usando sua senha.|
|`STAT`|Solicita o número de emails salvos do servidor.|
|`LIST`|Solicita ao servidor o número e o tamanho de todos os emails.|
|`RETR id`|Solicita ao servidor que entregue o email solicitado por ID.|
|`DELE id`|Solicita ao servidor que exclua o email solicitado por ID.|
|`CAPA`|Solicita ao servidor que exiba os recursos do servidor.|
|`RSET`|Solicita ao servidor que redefina as informações transmitidas.|
|`QUIT`|Fecha a conexão com o servidor POP3.|

## Configurações perigosas

No entanto, as opções de configuração configuradas incorretamente podem nos permitir obter mais informações, como depurar os comandos executados no serviço ou fazer login como anônimo, semelhante ao serviço FTP. A maioria das empresas usa provedores de email de terceiros, como Google, Microsoft e muitos outros. No entanto, algumas empresas ainda usam seus próprios servidores de correio por muitos motivos diferentes. Uma dessas razões é manter a privacidade que eles querem manter em suas próprias mãos. Muitos erros de configuração podem ser cometidos pelos administradores, o que, na pior das hipóteses, nos permitirá ler todos os e-mails enviados e recebidos, que podem até conter informações confidenciais ou confidenciais. Algumas dessas opções de configuração incluem:

|**Configurando**|**Descrição**|
|---|---|
|`auth_debug`|Permite todo o log de depuração de autenticação.|
|`auth_debug_passwords`|Essa configuração ajusta a verbosidade do log, as senhas enviadas e o esquema é registrado.|
|`auth_verbose`|Registra tentativas de autenticação malsucedidas e seus motivos.|
|`auth_verbose_passwords`|As senhas usadas para autenticação são registradas e também podem ser truncadas.|
|`auth_anonymous_username`|Isso especifica o nome de usuário a ser usado ao fazer login com o mecanismo ANONYMOUS SASL.|

## Pegando o serviço

Por padrão, portas `110`, `143`, `993`, e `995` são usados para IMAP e POP3. As duas portas mais altas usam `TLS/SSL` para criptografar a comunicação entre cliente e servidor. Usando o Nmap, podemos verificar o servidor para essas portas. A verificação retornará as informações correspondentes, como as que vemos abaixo, se o servidor usar um certificado SSL incorporado.

#### Nmap

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00026s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
143/tcp open  imap     Dovecot imapd
|_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
```

Por exemplo, a partir da saída, podemos ver que o nome comum é `mail1.inlanefreight.htb`, e o servidor de email pertence à organização `Inlanefreight`, localizado na Califórnia. Os recursos exibidos nos mostram os comandos disponíveis no servidor e para o serviço na porta correspondente.

Se descobrirmos com êxito as credenciais de acesso de um dos funcionários, um invasor poderá fazer login no servidor de correio e ler ou até enviar as mensagens individuais.

#### CURL

```shell-session
casluxd@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd

* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```


Se também usarmos o `verbose` (Opção `-v` ), veremos como a conexão é feita. A partir disso, podemos ver a versão do TLS usada para criptografia, mais detalhes do certificado SSL e até o banner, que geralmente contém a versão do servidor de correio.

```shell-session
casluxd@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v

*   Trying 10.129.14.128:993...
* TCP_NODELAY set
* Connected to 10.129.14.128 (10.129.14.128) port 993 (#0)
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* Server certificate:
*  subject: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  start date: Sep 19 19:44:58 2021 GMT
*  expire date: Jul  4 19:44:58 2295 GMT
*  issuer: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
< * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
> A001 CAPABILITY
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN
< A001 OK Pre-login capabilities listed, post-login capabilities have more.
> A002 AUTHENTICATE PLAIN AGNyeTBsMXQzADEyMzQ=
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE
< A002 OK Logged in
> A003 LIST "" *
< * LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." Important
< * LIST (\HasNoChildren) "." INBOX
* LIST (\HasNoChildren) "." INBOX
< A003 OK List completed (0.001 + 0.000 secs).
* Connection #0 to host 10.129.14.128 left intact
```

Para interagir com o servidor IMAP ou POP3 por SSL, podemos usar `openssl`, bem como `ncat`. Os comandos para isso seriam assim:

#### OpenSSL - Interação criptografada TLS POP3

```shell-session
casluxd@htb[/htb]$ openssl s_client -connect 10.129.14.128:pop3s

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 3CC39A7F2928B252EF2FFA5462140B1A0A74B29D4708AA8DE1515BB4033D92C2
    Session-ID-ctx:
    Resumption PSK: 68419D933B5FEBD878FF1BA399A926813BEA3652555E05F0EC75D65819A263AA25FA672F8974C37F6446446BB7EA83F9
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - d7 86 ac 7e f3 f4 95 35-88 40 a5 b5 d6 a6 41 e4   ...~...5.@....A.
    0010 - 96 6c e6 12 4f 50 ce 72-36 25 df e1 72 d9 23 94   .l..OP.r6%..r.#.
    0020 - cc 29 90 08 58 1b 57 ab-db a8 6b f7 8f 31 5b ad   .)..X.W...k..1[.
    0030 - 47 94 f4 67 58 1f 96 d9-ca ca 56 f9 7a 12 f6 6d   G..gX.....V.z..m
    0040 - 43 b9 b6 68 de db b2 47-4f 9f 48 14 40 45 8f 89   C..h...GO.H.@E..
    0050 - fa 19 35 9c 6d 3c a1 46-5c a2 65 ab 87 a4 fd 5e   ..5.m<.F\.e....^
    0060 - a2 95 25 d4 43 b8 71 70-40 6c fe 6f 0e d1 a0 38   ..%.C.qp@l.o...8
    0070 - 6e bd 73 91 ed 05 89 83-f5 3e d9 2a e0 2e 96 f8   n.s......>.*....
    0080 - 99 f0 50 15 e0 1b 66 db-7c 9f 10 80 4a a1 8b 24   ..P...f.|...J..$
    0090 - bb 00 03 d4 93 2b d9 95-64 44 5b c2 6b 2e 01 b5   .....+..dD[.k...
    00a0 - e8 1b f4 a4 98 a7 7a 7d-0a 80 cc 0a ad fe 6e b3   ......z}......n.
    00b0 - 0a d6 50 5d fd 9a b4 5c-28 a4 c9 36 e4 7d 2a 1e   ..P]...\(..6.}*.

    Start Time: 1632081313
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
+OK HTB-Academy POP3 Server
```

#### OpenSSL - IMAP de interação criptografada TLS

```shell-session
casluxd@htb[/htb]$ openssl s_client -connect 10.129.14.128:imaps

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 2B7148CD1B7B92BA123E06E22831FCD3B365A5EA06B2CDEF1A5F397177130699
    Session-ID-ctx:
    Resumption PSK: 4D9F082C6660646C39135F9996DDA2C199C4F7E75D65FA5303F4A0B274D78CC5BD3416C8AF50B31A34EC022B619CC633
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 68 3b b6 68 ff 85 95 7c-8a 8a 16 b2 97 1c 72 24   h;.h...|......r$
    0010 - 62 a7 84 ff c3 24 ab 99-de 45 60 26 e7 04 4a 7d   b....$...E`&..J}
    0020 - bc 6e 06 a0 ff f7 d7 41-b5 1b 49 9c 9f 36 40 8d   .n.....A..I..6@.
    0030 - 93 35 ed d9 eb 1f 14 d7-a5 f6 3f c8 52 fb 9f 29   .5........?.R..)
    0040 - 89 8d de e6 46 95 b3 32-48 80 19 bc 46 36 cb eb   ....F..2H...F6..
    0050 - 35 79 54 4c 57 f8 ee 55-06 e3 59 7f 5e 64 85 b0   5yTLW..U..Y.^d..
    0060 - f3 a4 8c a6 b6 47 e4 59-ee c9 ab 54 a4 ab 8c 01   .....G.Y...T....
    0070 - 56 bb b9 bb 3b f6 96 74-16 c9 66 e2 6c 28 c6 12   V...;..t..f.l(..
    0080 - 34 c7 63 6b ff 71 16 7f-91 69 dc 38 7a 47 46 ec   4.ck.q...i.8zGF.
    0090 - 67 b7 a2 90 8b 31 58 a0-4f 57 30 6a b6 2e 3a 21   g....1X.OW0j..:!
    00a0 - 54 c7 ba f0 a9 74 13 11-d5 d1 ec cc ea f9 54 7d   T....t........T}
    00b0 - 46 a6 33 ed 5d 24 ed b0-20 63 43 d8 8f 14 4d 62   F.3.]$.. cC...Mb

    Start Time: 1632081604
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
```

Depois de iniciarmos uma conexão e fazer login no servidor de email de destino, podemos usar os comandos acima para trabalhar e navegar no servidor. Queremos ressaltar que a configuração de nosso próprio servidor de correio, a pesquisa para ele, e os experimentos que podemos fazer em conjunto com outros membros da comunidade nos fornecerão o know-how para entender a comunicação que está ocorrendo e quais opções de configuração são responsáveis por isso.

Na seção SMTP, encontramos o usuário `robin`. Outro membro de nossa equipe conseguiu descobrir que o usuário também usa seu nome de usuário como senha (`robin`:`robin`). Podemos usar essas credenciais e experimentá-las para interagir com os serviços IMAP / POP3.