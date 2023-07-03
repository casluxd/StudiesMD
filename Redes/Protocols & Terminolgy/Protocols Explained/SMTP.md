O `Simple Mail Transfer Protocol` (`SMTP`) é um protocolo para enviar emails em uma rede IP. Pode ser usado entre um cliente de email e um servidor de correio de saída ou entre dois servidores SMTP. O SMTP geralmente é combinado com os protocolos IMAP ou POP3, que podem buscar e enviar e-mails. Em princípio, é um protocolo baseado no cliente-servidor, embora o SMTP possa ser usado entre um cliente e um servidor e entre dois servidores SMTP. Nesse caso, um servidor atua efetivamente como um cliente.

Por padrão, os servidores SMTP aceitam solicitações de conexão na porta `25`. No entanto, servidores SMTP mais recentes também usam outras portas, como porta TCP `587`. Essa porta é usada para receber email de usuários / servidores autenticados, geralmente usando o comando STARTTLS para alternar a conexão de texto sem formatação existente para uma conexão criptografada. Os dados de autenticação são protegidos e não são mais visíveis em texto sem formatação pela rede. No início da conexão, a autenticação ocorre quando o cliente confirma sua identidade com um nome de usuário e senha. Os e-mails podem ser transmitidos. Para esse fim, o cliente envia ao remetente do servidor e aos endereços dos destinatários, o conteúdo do email e outras informações e parâmetros. Após a transmissão do email, a conexão é encerrada novamente. O servidor de email começa a enviar o email para outro servidor SMTP.

O SMTP funciona sem criptografia sem medidas adicionais e transmite todos os comandos, dados ou informações de autenticação em texto sem formatação. Para impedir a leitura não autorizada de dados, o SMTP é usado em conjunto com a criptografia SSL / TLS. Sob certas circunstâncias, um servidor usa uma porta diferente da porta TCP padrão `25` para a conexão criptografada, por exemplo, porta TCP `465`.

Uma função essencial de um servidor SMTP é impedir o spam usando mecanismos de autenticação que permitem que apenas usuários autorizados enviem e-mails. Para esse fim, a maioria dos servidores SMTP modernos suporta a extensão de protocolo ESMTP com SMTP-Auth. Depois de enviar seu e-mail, o cliente SMTP, também conhecido como `Mail User Agent` (`MUA`), converte-o em um cabeçalho e um corpo e carrega ambos no servidor SMTP. Isso tem o chamado `Mail Transfer Agent` (`MTA`), a base de software para enviar e receber e-mails. O MTA verifica o tamanho e o spam do e-mail e depois o armazena. Para aliviar o MTA, é ocasionalmente precedido por um `Mail Submission Agent` (`MSA`), que verifica a validade, ou seja, a origem do email. Este `MSA` também é chamado servidor `Relay`. Estes são muito importantes mais tarde, como os chamados `Open Relay Attack` podem ser realizado em muitos servidores SMTP devido a uma configuração incorreta. Discutiremos esse ataque e como identificar o ponto fraco um pouco mais tarde. O MTA procura no DNS o endereço IP do servidor de email do destinatário.

Na chegada ao servidor SMTP de destino, os pacotes de dados são remontados para formar um email completo. De lá, o `Mail delivery agent` (`MDA`) o transfere para a caixa de correio do destinatário.

|Cliente (`MUA`)|`➞`|Agente de envio (`MSA`)|`➞`|Open Relay (`MTA`)|`➞`|Agente de entrega de correio (`MDA`)|`➞`|Caixa de correio (`POP3`/`IMAP`)|
|---|---|---|---|---|---|---|---|---|

Mas o SMTP tem duas desvantagens inerentes ao protocolo de rede.

1. A primeira é que o envio de um email usando SMTP não retorna uma confirmação de entrega utilizável. Embora as especificações do protocolo prevejam esse tipo de notificação, sua formatação não é especificada por padrão, de modo que geralmente apenas uma mensagem de erro no idioma inglês, incluindo o cabeçalho da mensagem não entregue, é retornado.
2. Os usuários não são autenticados quando uma conexão é estabelecida e, portanto, o remetente de um email não é confiável. Como resultado, os relays SMTP abertos costumam ser mal utilizados para enviar spam em massa. Os originadores usam endereços de remetente falsos arbitrários para esse fim para não serem rastreados ( falsificação de email ). Hoje, muitas técnicas de segurança diferentes são usadas para evitar o uso indevido de servidores SMTP. Por exemplo, e-mails suspeitos são rejeitados ou movidos para a pasta de spam ( de quarentena ). Por exemplo, responsáveis por isso são o protocolo de identificação [DomainKeys](http://dkim.org/) (`DKIM`), o [Estrutura da política de remetente](https://dmarcian.com/what-is-spf/) (`SPF`).

Para esse fim, uma extensão para SMTP foi desenvolvida chamada `Extended SMTP` (`ESMTP`). Quando as pessoas falam sobre SMTP em geral, geralmente significam ESMTP. O ESMTP usa TLS, o que é feito após o `EHLO` comando enviando `STARTTLS`. Isso inicializa a conexão SMTP protegida por SSL e, a partir deste momento, toda a conexão é criptografada e, portanto, mais ou menos segura. Agora [AUTH PLAIN](https://www.samlogic.net/articles/smtp-commands-reference-auth.htm) a extensão para autenticação também pode ser usada com segurança.

## Configuração padrão

Cada servidor SMTP pode ser configurado de várias maneiras, assim como todos os outros serviços. No entanto, existem diferenças porque o servidor SMTP é responsável apenas pelo envio e encaminhamento de emails.

```shell-session
casluxd@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

O envio e a comunicação também são feitos por comandos especiais que fazem com que o servidor SMTP faça o que o usuário exige.

|**Comando**|**Descrição**|
|---|---|
|`AUTH PLAIN`|AUTH é uma extensão de serviço usada para autenticar o cliente.|
|`HELO`|O cliente faz login com o nome do computador e, assim, inicia a sessão.|
|`MAIL FROM`|O cliente nomeia o remetente do email.|
|`RCPT TO`|O cliente nomeia o destinatário do email.|
|`DATA`|O cliente inicia a transmissão do email.|
|`RSET`|O cliente interrompe a transmissão iniciada, mas mantém a conexão entre cliente e servidor.|
|`VRFY`|O cliente verifica se uma caixa de correio está disponível para transferência de mensagens.|
|`EXPN`|O cliente também verifica se uma caixa de correio está disponível para mensagens com este comando.|
|`NOOP`|O cliente solicita uma resposta do servidor para impedir a desconexão devido ao tempo limite.|
|`QUIT`|O cliente encerra a sessão.|

Para interagir com o servidor SMTP, podemos usar o `telnet` ferramenta para inicializar uma conexão TCP com o servidor SMTP. A inicialização real da sessão é feita com o comando mencionado acima, `HELO` ou `EHLO`.

#### Telnet - HELO / EHLO

```shell-session
casluxd@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

O comando `VRFY` pode ser usado para enumerar usuários existentes no sistema. No entanto, isso nem sempre funciona. Dependendo de como o servidor SMTP está configurado, o servidor SMTP pode emitir `code 252` e confirme a existência de um usuário que não existe no sistema. Uma lista de todos os códigos de resposta SMTP pode ser encontrada [aqui](https://serversmtp.com/smtp-error/).

#### Telnet - VRFY

```shell-session
casluxd@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Portanto, nunca se deve confiar inteiramente nos resultados de ferramentas automáticas. Afinal, eles executam comandos pré-configurados, mas nenhuma das funções indica explicitamente como o administrador configura o servidor testado.

Às vezes, podemos ter que trabalhar com um proxy da web. Também podemos fazer com que esse proxy da web se conecte ao servidor SMTP. O comando que enviaríamos seria algo assim: `CONNECT 10.129.14.128:25 HTTP/1.0`

Todos os comandos que inserimos na linha de comando para enviar um email que conhecemos de todos os programas de clientes de email, como Thunderbird, Gmail, Outlook e muitos outros. Nós especificamos o `subject`, para quem o email deve ir, CC, BCC e as informações que queremos compartilhar com outras pessoas. Obviamente, o mesmo funciona na linha de comando.

```shell-session
casluxd@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server


EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING


MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok


RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok


DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB


QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```

O cabeçalho do correio é o transportador de uma grande quantidade de informações interessantes em um email. Entre outras coisas, fornece informações sobre o remetente e o destinatário, a hora do envio e chegada, as estações pelas quais o email passou, o conteúdo e o formato da mensagem, e o remetente e destinatário.

Algumas dessas informações são obrigatórias, como informações do remetente e quando o email foi criado. Outras informações são opcionais. No entanto, o cabeçalho do email não contém nenhuma informação necessária para a entrega técnica. É transmitido como parte do protocolo de transmissão. O remetente e o destinatário podem acessar o cabeçalho de um email, embora não seja visível à primeira vista. A estrutura de um cabeçalho de email é definida por [RFC5322](https://datatracker.ietf.org/doc/html/rfc5322).

## Configurações perigosas

Para impedir que os emails enviados sejam filtrados por filtros de spam e não cheguem ao destinatário, o remetente pode usar um servidor de retransmissão em que o destinatário confia. É um servidor SMTP conhecido e verificado por todos os outros. Como regra, o remetente deve se autenticar no servidor de retransmissão antes de usá-lo.

Freqüentemente, os administradores não têm uma visão geral de quais intervalos de IP eles precisam permitir. Isso resulta em uma configuração incorreta do servidor SMTP que ainda frequentemente encontramos em testes de penetração externos e internos. Portanto, eles permitem que todos os endereços IP não causem erros no tráfego de email e, portanto, não perturbem ou interrompam acidentalmente a comunicação com clientes potenciais e atuais.

```shell-session
mynetworks = 0.0.0.0/0
```

Com essa configuração, esse servidor SMTP pode enviar e-mails falsos e, assim, inicializar a comunicação entre várias partes. Outra possibilidade de ataque seria falsificar o email e lê-lo.

## Pegando o serviço

Os scripts Nmap padrão incluem `smtp-commands`, que usa o `EHLO` comando para listar todos os comandos possíveis que podem ser executados no servidor SMTP de destino.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -sC -sV -p25

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.09 seconds
```

No entanto, também podemos usar o [smtp-open-relay](https://nmap.org/nsedoc/scripts/smtp-open-relay.html) Script NSE para identificar o servidor SMTP de destino como um relé aberto usando 16 testes diferentes. Se também imprimirmos a saída da digitalização em detalhes, também poderemos ver quais testes o script está sendo executado.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```

