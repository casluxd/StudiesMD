O `File Transfer Protocol` (`FTP`) é um dos protocolos mais antigos da Internet. O FTP é executado dentro da camada de aplicação da pilha de protocolos TCP / IP. Assim, está na mesma camada que `HTTP` ou `POP`. Esses protocolos também funcionam com o suporte de navegadores ou clientes de email para executar seus serviços. Existem também programas FTP especiais para o File Transfer Protocol.

Imaginemos que queremos enviar arquivos locais para um servidor e baixar outros arquivos usando o protocolo [FTP](https://datatracker.ietf.org/doc/html/rfc959). Em uma conexão FTP, dois canais são abertos. Primeiro, o cliente e o servidor estabelecem um canal de controle através `TCP port 21`. O cliente envia comandos para o servidor e o servidor retorna códigos de status. Em seguida, os dois participantes da comunicação podem estabelecer o canal de dados via `TCP port 20`. Esse canal é usado exclusivamente para transmissão de dados e o protocolo procura erros durante esse processo. Se uma conexão for interrompida durante a transmissão, o transporte poderá ser retomado após o contato restabelecido.

É feita uma distinção entre FTP `ativa` e `passiva`. Na variante ativa, o cliente estabelece a conexão conforme descrito na porta TCP 21 e, portanto, informa o servidor através da porta do lado do cliente que o servidor pode transmitir suas respostas. No entanto, se um firewall proteger o cliente, o servidor não poderá responder porque todas as conexões externas estão bloqueadas. Para esse fim, o `passive mode` foi desenvolvido. Aqui, o servidor anuncia uma porta através da qual o cliente pode estabelecer o canal de dados. Como o cliente inicia a conexão nesse método, o firewall não bloqueia a transferência.

O FTP sabe diferente [comandos](https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/) e códigos de status. Nem todos esses comandos são implementados de forma consistente no servidor. Por exemplo, o lado do cliente instrui o lado do servidor a carregar ou baixar arquivos, organizar diretórios ou excluir arquivos. O servidor responde em cada caso com um código de status que indica se o comando foi implementado com sucesso. Uma lista de possíveis códigos de status pode ser encontrada [aqui](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes).

Normalmente, precisamos de credenciais para usar o FTP em um servidor. Também precisamos saber que o FTP é um protocolo `clear-text` que às vezes pode ser farejado se as condições na rede estiverem corretas. No entanto, há também a possibilidade de um servidor oferecer `anonymous FTP`. O operador do servidor permite que qualquer usuário faça upload ou faça o download de arquivos via FTP sem usar uma senha. Como existem riscos de segurança associados a esse servidor FTP público, as opções para usuários geralmente são limitadas.

## TFTP

`Trivial File Transfer Protocol` (`TFTP`) é mais simples que o FTP e realiza transferências de arquivos entre os processos do cliente e do servidor. No entanto `não pode` fornecer autenticação do usuário e outros recursos valiosos suportados pelo FTP. Além disso, enquanto o FTP usa TCP, o TFTP usa `UDP`, tornando-o um protocolo não confiável e fazendo com que ele use a recuperação da camada de aplicativo assistida por UDP.

Isso se reflete, por exemplo, no fato de que o TFTP, diferentemente do FTP, não requer a autenticação do usuário. Ele não suporta login protegido por meio de senhas e define limites de acesso com base apenas nas permissões de leitura e gravação de um arquivo no sistema operacional. Praticamente, isso leva o TFTP a operar exclusivamente em diretórios e com arquivos que foram compartilhados com todos os usuários e podem ser lidos e gravados globalmente. Devido à falta de segurança, o TFTP, diferentemente do FTP, só pode ser usado em redes locais e protegidas.

Vamos dar uma olhada em alguns comandos de `TFTP`:

|**Comandos**|**Descrição**|
|---|---|
|`connect`|Define o host remoto e, opcionalmente, a porta, para transferências de arquivos.|
|`get`|Transfere um arquivo ou conjunto de arquivos do host remoto para o host local.|
|`put`|Transfere um arquivo ou conjunto de arquivos do host local para o host remoto.|
|`quit`|Saídas tftp.|
|`status`|Mostra o status atual do tftp, incluindo o modo de transferência atual ( ascii ou binário ), status da conexão, valor de tempo limite e assim por diante.|
|`verbose`|Ativa o modo detalhado, que exibe informações adicionais durante a transferência de arquivos, ativada ou desativada.|

Ao contrário do cliente FTP, `TFTP` não possui funcionalidade de listagem de diretórios.

## Configuração padrão

Um dos servidores FTP mais usados em distribuições baseadas em Linux é [vsFTPd](https://security.appspot.com/vsftpd.html). A configuração padrão do vsFTPd pode ser encontrada em `/etc/vsftpd.conf`, e algumas configurações já estão predefinidas por padrão. É altamente recomendável instalar o servidor vsFTPd em uma VM e dar uma olhada mais de perto nessa configuração.

```shell-session
casluxd@htb[/htb]$ sudo apt install vsftpd 
```

O servidor vsFTPd é apenas um dos poucos servidores FTP disponíveis para nós. Existem muitas alternativas diferentes, que também trazem, entre outras coisas, muito mais funções e opções de configuração. Usaremos o servidor vsFTPd porque é uma excelente maneira de mostrar as possibilidades de configuração de um servidor FTP de uma maneira simples e fácil de entender, sem entrar nos detalhes de as páginas de manual. Se olharmos para o arquivo de configuração do vsFTPd, veremos muitas opções e configurações que são comentadas ou comentadas. No entanto, o arquivo de configuração não contém todas as configurações possíveis que podem ser feitas. Os existentes e os ausentes podem ser encontrados no [página de manual](http://vsftpd.beasts.org/vsftpd_conf.html).

#### Arquivo de configuração vsFTPd

|**Configurando**|**Descrição**|
|---|---|
|`listen=NO`|Fugir do inetd ou como um daemon independente?|
|`listen_ipv6=YES`|Ouça no IPv6 ?|
|`anonymous_enable=NO`|Ativar acesso anônimo?|
|`local_enable=YES`|Permitir que usuários locais façam login?|
|`dirmessage_enable=YES`|Exibir mensagens de diretório ativas quando os usuários acessam determinados diretórios?|
|`use_localtime=YES`|Usar hora local?|
|`xferlog_enable=YES`|Ativar log de uploads / downloads?|
|`connect_from_port_20=YES`|Conecte-se da porta 20?|
|`secure_chroot_dir=/var/run/vsftpd/empty`|Nome de um diretório vazio|
|`pam_service_name=vsftpd`|Essa sequência é o nome do serviço PAM que o vsftpd usará.|
|`rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`|As três últimas opções especificam o local do certificado RSA a ser usado para conexões criptografadas SSL.|
|`rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key`||
|`ssl_enable=NO`|

Além disso, há um arquivo chamado `/etc/ftpusers` que também precisamos prestar atenção, pois esse arquivo é usado para negar o acesso de determinados usuários ao serviço FTP. No exemplo a seguir, os usuários `guest`, `john`, e `kevin` não é permitido fazer login no serviço FTP, mesmo que existam no sistema Linux.

```shell-session
casluxd@htb[/htb]$ cat /etc/ftpusers

guest
john
kevin
```

## Configurações perigosas

Existem muitas configurações diferentes relacionadas à segurança que podemos fazer em cada servidor FTP. Eles podem ter vários propósitos, como testar conexões através dos firewalls, rotas de teste e mecanismos de autenticação. Um desses mecanismos de autenticação é o `anonymous` Usuário. Isso geralmente é usado para permitir que todos na rede interna compartilhem arquivos e dados sem acessar os computadores uns dos outros. Com vsFTPd, o [configurações opcionais](http://vsftpd.beasts.org/vsftpd_conf.html) que pode ser adicionado ao arquivo de configuração do login anônimo, assim:

|**Configurando**|**Descrição**|
|---|---|
|`anonymous_enable=YES`|Permitindo login anônimo?|
|`anon_upload_enable=YES`|Permitindo que o anônimo faça upload de arquivos?|
|`anon_mkdir_write_enable=YES`|Permitindo que o anônimo crie novos diretórios?|
|`no_anon_password=YES`|Não peça senha anônima?|
|`anon_root=/home/username/ftp`|Diretório para anônimo.|
|`write_enable=YES`|Permitir o uso de comandos FTP: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE e SITE?|

Com o cliente FTP padrão (`ftp`), podemos acessar o servidor FTP de acordo e fazer login com o usuário anônimo se as configurações mostradas acima tiverem sido usadas. O uso da conta anônima pode ocorrer em ambientes e infraestruturas internas, onde todos os participantes são conhecidos. O acesso a esse tipo de serviço pode ser definido temporariamente ou com a configuração para acelerar a troca de arquivos.

Assim que nos conectamos ao servidor vsFTPd, o `response code 220` é exibido com o banner do servidor FTP. Muitas vezes, este banner contém a descrição do `service` e até o `version` disso. Ele também nos diz que tipo de sistema é o servidor FTP. Uma das configurações mais comuns dos servidores FTP é permitir `anonymous` acesso, que não requer credenciais legítimas, mas fornece acesso a alguns arquivos. Mesmo que não possamos baixá-los, às vezes basta listar o conteúdo para gerar mais idéias e anotar informações que nos ajudarão em outra abordagem.

#### Login anônimo

```shell-session
casluxd@htb[/htb]$ ftp 10.129.14.136

Connected to 10.129.14.136.
220 "Welcome to the HTB Academy vsFTP service."
Name (10.129.14.136:cry0l1t3): anonymous

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.


ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```

No entanto, para obter a primeira visão geral das configurações do servidor, podemos usar o seguinte comando:

```shell-session
ftp> status

Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off
```

Alguns comandos devem ser usados ocasionalmente, pois isso fará com que o servidor nos mostre mais informações que podemos usar para nossos propósitos. Esses comandos incluem `debug` e `trace`.

```shell-session
ftp> debug

Debugging on (debug=1).


ftp> trace

Packet tracing on.


ftp> ls

---> PORT 10,10,14,4,188,195
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```

|**Configurando**|**Descrição**|
|---|---|
|`dirmessage_enable=YES`|Mostrar uma mensagem quando eles entrarem em um novo diretório?|
|`chown_uploads=YES`|Alterar a propriedade dos arquivos enviados anonimamente?|
|`chown_username=username`|Usuário que possui arquivos enviados anonimamente.|
|`local_enable=YES`|Ativar usuários locais a fazer login?|
|`chroot_local_user=YES`|Colocar usuários locais em seu diretório pessoal?|
|`chroot_list_enable=YES`|Use uma lista de usuários locais que serão colocados em seu diretório pessoal?|

|**Configurando**|**Descrição**|
|---|---|
|`hide_ids=YES`|Todas as informações de usuário e grupo nas listagens de diretórios serão exibidas como "ftp".|
|`ls_recurse_enable=YES`|Permite o uso de listagens de recurse.|

No exemplo a seguir, podemos ver que se o `hide_ids=YES` a configuração está presente, a representação UID e GUID do serviço será substituída, dificultando a identificação de quais direitos esses arquivos são gravados e carregados.

#### Ocultando IDs - SIM

```shell-session
ftp> ls

---> TYPE A
200 Switching to ASCII mode.
ftp: setsockopt (ignored): Permission denied
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

Essa configuração é um recurso de segurança para impedir que nomes de usuário locais sejam revelados. Com os nomes de usuário, poderíamos atacar serviços como FTP e SSH e muitos outros com um ataque de força bruta em teoria. No entanto, na realidade, [fail2ban](https://en.wikipedia.org/wiki/Fail2ban) as soluções agora são uma implementação padrão de qualquer infraestrutura que registre o endereço IP e bloqueie todo o acesso à infraestrutura após um certo número de tentativas de login com falha.

Outra configuração útil que podemos usar para nossos propósitos é a `ls_recurse_enable=YES`. Isso geralmente é definido no servidor vsFTPd para ter uma melhor visão geral da estrutura do diretório FTP, pois permite ver todo o conteúdo visível de uma só vez.

```shell-session
ftp> ls -R

---> PORT 10,10,14,4,222,149
200 PORT command successful. Consider using PASV.
---> LIST -R
150 Here comes the directory listing.
.:
-rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt

./Clients:
drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight

./Clients/HackTheBox:
-rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx
-rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx
-rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf
-rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt

./Clients/Inlanefreight:
-rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx
-rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx
-rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt
-rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx

./Documents:
-rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx
-rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx
-rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf

./Employees:
226 Directory send OK.
```

`Downloading` os arquivos desse servidor FTP são um dos principais recursos, bem como `uploading` arquivos criados por nós. Isso nos permite, por exemplo, usar vulnerabilidades LFI para fazer o host executar comandos do sistema. Além dos arquivos, podemos visualizar, baixar e inspecionar. Também são possíveis ataques com os logs FTP, levando a `Remote Command Execution` (`RCE`). Isso se aplica aos serviços de FTP e a todos aqueles que podemos detectar durante nossa fase de enumeração.

```shell-session
ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx
drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees
-rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt
226 Directory send OK.


ftp> get Important\ Notes.txt

local: Important Notes.txt remote: Important Notes.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for Important Notes.txt (41 bytes).
226 Transfer complete.
41 bytes received in 0.00 secs (606.6525 kB/s)


ftp> exit

221 Goodbye.
```

```shell-session
casluxd@htb[/htb]$ ls | grep Notes.txt

'Important Notes.txt'
```

Também podemos baixar todos os arquivos e pastas aos quais temos acesso de uma só vez. Isso é especialmente útil se o servidor FTP tiver muitos arquivos diferentes em uma estrutura de pastas maior. No entanto, isso pode causar alarmes, porque ninguém da empresa geralmente deseja baixar todos os arquivos e conteúdos de uma só vez.

```shell-session
casluxd@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136

--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/                                         
           => ‘10.129.14.136/.listing’                                                                     
Connecting to 10.129.14.136:21... connected.                                                               
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> PORT ... done.    ==> LIST ... done.                                                                 
12.12.1.136/.listing           [ <=>                                  ]     466  --.-KB/s    in 0s       
                                                                                                         
2021-09-19 14:45:58 (65,8 MB/s) - ‘10.129.14.136/.listing’ saved [466]                                     
--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/Calendar.pptx   
           => ‘10.129.14.136/Calendar.pptx’                                       
==> CWD not required.                                                           
==> SIZE Calendar.pptx ... done.                                                                                                                            
==> PORT ... done.    ==> RETR Calendar.pptx ... done.       

...SNIP...

2021-09-19 14:45:58 (48,3 MB/s) - ‘10.129.14.136/Employees/.listing’ saved [119]

FINISHED --2021-09-19 14:45:58--
Total wall clock time: 0,03s
Downloaded: 15 files, 1,7K in 0,001s (3,02 MB/s)
```

Depois de baixar todos os arquivos, `wget` criará um diretório com o nome do endereço IP do nosso destino. Todos os arquivos baixados são armazenados lá, que podemos inspecionar localmente.

```shell-session
casluxd@htb[/htb]$ tree .

.
└── 10.129.14.136
    ├── Calendar.pptx
    ├── Clients
    │   └── Inlanefreight
    │       ├── appointments.xlsx
    │       ├── contract.docx
    │       ├── meetings.txt
    │       └── proposal.pptx
    ├── Documents
    │   ├── appointments-template.xlsx
    │   ├── contract-template.docx
    │   └── contract-template.pdf
    ├── Employees
    └── Important Notes.txt

5 directories, 9 files
```

Em seguida, podemos verificar se temos as permissões para fazer upload de arquivos no servidor FTP. Especialmente com servidores da web, é comum que os arquivos sejam sincronizados e os desenvolvedores tenham acesso rápido aos arquivos. O FTP é frequentemente usado para esse fim e, na maioria das vezes, erros de configuração são encontrados em servidores que os administradores acham que não são descobertos. A atitude de que os componentes internos da rede não podem ser acessados de fora significa que o endurecimento dos sistemas internos geralmente é negligenciado e leva a configurações incorretas.

A capacidade de fazer upload de arquivos para o servidor FTP conectado a um servidor da Web aumenta a probabilidade de obter acesso direto ao servidor da web e até um shell reverso que nos permite executar comandos do sistema e talvez até aumente nossos privilégios.

```shell-session
casluxd@htb[/htb]$ touch testupload.txt
```

Com o `PUT` comando, podemos carregar arquivos na pasta atual para o servidor FTP.

```shell-session
ftp> put testupload.txt 

local: testupload.txt remote: testupload.txt
---> PORT 10,10,14,4,184,33
200 PORT command successful. Consider using PASV.
---> STOR testupload.txt
150 Ok to send data.
226 Transfer complete.


ftp> ls

---> TYPE A
200 Switching to ASCII mode.
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 1002     133             0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

## Pegando o serviço

A pegada usando vários scanners de rede também é uma abordagem prática e generalizada. Essas ferramentas facilitam a identificação de serviços diferentes, mesmo que não estejam acessíveis em portas padrão. Uma das ferramentas mais amplamente usadas para esse fim é o Nmap. Nmap também traz o [Mecanismo de script de mapa](https://nmap.org/book/nse.html) (`NSE`), um conjunto de muitos scripts diferentes escritos para serviços específicos. Mais informações sobre os recursos do Nmap e NSE podem ser encontradas no [Enumeração de rede com Nmap](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap) módulo. Podemos atualizar esse banco de dados de scripts NSE com o comando mostrado.

#### Scripts FTP do nmap

```shell-session
casluxd@htb[/htb]$ sudo nmap --script-updatedb

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST
NSE: Updating rule database.
NSE: Script Database updated successfully.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds
```

Todos os scripts NSE estão localizados no Pwnbox em `/usr/share/nmap/scripts/`, mas em nossos sistemas, podemos encontrá-los usando um comando simples em nosso sistema.

```shell-session
casluxd@htb[/htb]$ find / -type f -name ftp* 2>/dev/null | grep scripts

/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-brute.nse
```

Como já sabemos, o servidor FTP geralmente é executado na porta TCP padrão 21, que podemos verificar usando o Nmap. Também usamos a verificação de versão (`-sV`), varredura agressiva (`-A`) e a verificação padrão do script (`-sC`) contra o nosso alvo `10.129.14.136`.

```shell-session
casluxd@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST
Nmap scan report for 10.129.14.136
Host is up (0.00013s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable]
| drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable]
| -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable]
|_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
```

A verificação padrão do script é baseada nas impressões digitais, respostas e portas padrão dos serviços. Depois que o Nmap detectou o serviço, ele executa os scripts marcados um após o outro, fornecendo informações diferentes. Por exemplo, o [ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html) O script NSE verifica se o servidor FTP permite acesso anônimo. Nesse caso, o conteúdo do diretório raiz do FTP é renderizado para o usuário anônimo.

O `ftp-syst`, por exemplo, executa o `STAT` comando, que exibe informações sobre o status do servidor FTP. Isso inclui configurações e a versão do servidor FTP. O Nmap também fornece a capacidade de rastrear o progresso dos scripts NSE no nível da rede se usarmos o `--script-trace` opção em nossas digitalizações. Isso nos permite ver quais comandos o Nmap envia, quais portas são usadas e quais respostas recebemos do servidor digitalizado.

```shell-session
casluxd@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST                                                                                                                                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]             
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #1 [10.129.14.136:21] (timeout: 7000ms) EID 42
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #3 [10.129.14.136:21] (timeout: 7000ms) EID 58
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #4 [10.129.14.136:21] (timeout: 11000ms) EID 66
NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.
```

O histórico da verificação mostra que quatro varreduras paralelas diferentes estão sendo executadas em relação ao serviço, com vários tempos limite. Para os scripts NSE, vemos que nossa máquina local usa outras portas de saída (`54226`, `54228`, `54230`, `54232`) e inicia primeiro a conexão com o `CONNECT` comando. Desde a primeira resposta do servidor, podemos ver que estamos recebendo o banner do servidor para o nosso segundo script NSE (`54228`) do servidor FTP de destino. Se necessário, é claro que podemos usar outros aplicativos, como `netcat` ou `telnet` para interagir com o servidor FTP.

#### Interação de serviço

```shell-session
casluxd@htb[/htb]$ nc -nv 10.129.14.136 21
```

```shell-session
casluxd@htb[/htb]$ telnet 10.129.14.136 21
```

Parece um pouco diferente se o servidor FTP for executado com criptografia TLS / SSL. Porque então precisamos de um cliente que possa lidar com TLS / SSL. Para isso, podemos usar o cliente `openssl` e se comunique com o servidor FTP. A coisa boa de usar `openssl` é que podemos ver o certificado SSL, o que também pode ser útil.

```shell-session
casluxd@htb[/htb]$ openssl s_client -connect 10.129.14.136:21 -starttls ftp

CONNECTED(00000003)                                                                                      
Can't use SSL_get_servername                        
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1

depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify return:1
---                                                 
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
 
 i:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
---
 
Server certificate

-----BEGIN CERTIFICATE-----

MIIENTCCAx2gAwIBAgIUD+SlFZAWzX5yLs2q3ZcfdsRQqMYwDQYJKoZIhvcNAQEL
...SNIP...
```

Isso ocorre porque o certificado SSL nos permite reconhecer o `hostname`, por exemplo, e na maioria dos casos também um `email address` para a organização ou empresa. Além disso, se a empresa possui vários locais em todo o mundo, também podem ser criados certificados para locais específicos, que também podem ser identificados usando o certificado SSL.

