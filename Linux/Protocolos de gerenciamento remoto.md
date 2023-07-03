No mundo das distribuições Linux, existem muitas maneiras de gerenciar os servidores remotamente. Por exemplo, vamos imaginar que estamos em um dos muitos locais e um de nossos funcionários que acabou de ir a um cliente em outra cidade precisa de nossa ajuda por causa de um erro que ele não pode resolver. A solução eficiente de problemas parecerá difícil na maioria dos casos, por isso é benéfico se soubermos acessar o sistema remoto para gerenciá-lo.

Esses aplicativos e serviços podem ser encontrados em quase todos os servidores da rede pública. Economiza tempo, pois não precisamos estar fisicamente presentes no servidor, e o ambiente de trabalho ainda parece o mesmo. Esses protocolos e aplicativos para gerenciamento remoto de sistemas são um alvo interessante por esses motivos. Se a configuração estiver incorreta, nós, como testadores de penetração, podemos até obter rapidamente acesso ao sistema remoto. Portanto, devemos nos familiarizar com os protocolos, servidores e aplicativos mais importantes para esse fim.

## SSH

[Shell seguro](https://en.wikipedia.org/wiki/Secure_Shell) (`SSH`) permite que dois computadores estabeleçam uma conexão criptografada e direta dentro de uma rede possivelmente insegura na porta padrão `TCP 22`. Isso é necessário para impedir que terceiros interceptem o fluxo de dados e, assim, interceptem dados confidenciais. O servidor SSH também pode ser configurado para permitir apenas conexões de clientes específicos. Uma vantagem do SSH é que o protocolo é executado em todos os sistemas operacionais comuns. Como é originalmente um aplicativo Unix, também é implementado nativamente em todas as distribuições Linux e MacOS. O SSH também pode ser usado no Windows, desde que instalemos um programa apropriado. O conhecido [SSH do OpenBSD](https://www.openssh.com/) (`OpenSSH`O servidor ) nas distribuições Linux é um fork de código aberto do original e comercial `SSH` servidor do SSH Communication Security. Consequentemente, existem dois protocolos concorrentes: `SSH-1` e `SSH-2`.

`SSH-2`, também conhecido como SSH versão 2, é um protocolo mais avançado que o SSH versão 1 em criptografia, velocidade, estabilidade e segurança. Por exemplo, `SSH-1` é vulnerável a `MITM` ataques, enquanto SSH-2 não é.

Podemos imaginar que queremos gerenciar um host remoto. Isso pode ser feito através da linha de comando ou da GUI. Além disso, também podemos usar o protocolo SSH para enviar comandos para o sistema desejado, transferir arquivos ou fazer o encaminhamento de porta. Portanto, precisamos nos conectar a ele usando o protocolo SSH e nos autenticar a ele. No total, o OpenSSH possui seis métodos de autenticação diferentes:

1. Autenticação de senha
2. Autenticação por chave pública
3. Autenticação baseada em host
4. Autenticação do teclado
5. Autenticação de resposta a desafios
6. Autenticação GSSAPI

Vamos dar uma olhada e discutir um dos métodos de autenticação mais usados. Além disso, podemos aprender mais sobre os outros métodos de autenticação [aqui](https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/) entre outros.

#### Autenticação de chave pública

Em uma primeira etapa, o servidor e o cliente SSH se autenticam. O servidor envia um certificado ao cliente para verificar se é o servidor correto. Somente quando o contato é estabelecido pela primeira vez, existe o risco de terceiros se interporem entre os dois participantes e, assim, interceptarem a conexão. Como o certificado em si também é criptografado, ele não pode ser imitado. Depois que o cliente souber o certificado correto, ninguém mais poderá fingir entrar em contato através do servidor correspondente.

Após a autenticação do servidor, no entanto, o cliente também deve provar ao servidor que possui autorização de acesso. No entanto, o servidor SSH já possui o valor de hash criptografado do conjunto de senhas para o usuário desejado. Como resultado, os usuários precisam inserir a senha sempre que efetuam logon em outro servidor durante a mesma sessão. Por esse motivo, uma opção alternativa para autenticação do lado do cliente é o uso de um par de chaves públicas e chave privada.

A chave privada é criada individualmente para o próprio computador do usuário e protegida com uma senha que deve ser maior que uma senha típica. A chave privada é armazenada exclusivamente em nosso próprio computador e sempre permanece em segredo. Se queremos estabelecer uma conexão SSH, primeiro inserimos a senha e, assim, abrimos o acesso à chave privada.

As chaves públicas também são armazenadas no servidor. O servidor cria um problema criptográfico com a chave pública do cliente e a envia ao cliente. O cliente, por sua vez, descriptografa o problema com sua própria chave privada, envia de volta a solução e, portanto, informa ao servidor que ele pode estabelecer uma conexão legítima. Durante uma sessão, os usuários precisam inserir a senha apenas uma vez para se conectar a qualquer número de servidores. No final da sessão, os usuários efetuam logout de suas máquinas locais, garantindo que nenhum terceiro que obtenha acesso físico à máquina local possa se conectar ao servidor.

## Configuração padrão

O [sshd_config](https://www.ssh.com/academy/ssh/sshd_config) O arquivo, responsável pelo servidor OpenSSH, possui apenas algumas das configurações configuradas por padrão. No entanto, a configuração padrão inclui o encaminhamento X11, que continha uma vulnerabilidade de injeção de comando na versão 7.2p1 do OpenSSH em 2016. No entanto, não precisamos de uma GUI para gerenciar nossos servidores.

```shell-session
casluxd@htb[/htb]$ cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```

A maioria das configurações neste arquivo de configuração é comentada e requer configuração manual.

---

## Configurações perigosas

Apesar de o protocolo SSH ser um dos protocolos mais seguros disponíveis hoje, algumas configurações incorretas ainda podem tornar o servidor SSH vulnerável a ataques fáceis de executar. Vamos dar uma olhada nas seguintes configurações:

|**Configurando**|**Descrição**|
|---|---|
|`PasswordAuthentication yes`|Permite autenticação baseada em senha.|
|`PermitEmptyPasswords yes`|Permite o uso de senhas vazias.|
|`PermitRootLogin yes`|Permite fazer login como usuário raiz.|
|`Protocol 1`|Usa uma versão desatualizada da criptografia.|
|`X11Forwarding yes`|Permite encaminhamento X11 para aplicativos GUI.|
|`AllowTcpForwarding yes`|Permite o encaminhamento de portas TCP.|
|`PermitTunnel`|Permite encapsulamento.|
|`DebianBanner yes`|Exibe um banner específico ao fazer login.|

Permitindo password authentication allows us to brute-force a known username for possible passwords. Many different methods can be used to guess the passwords of users. For this purpose, specific` os padrões `geralmente são usados para alterar as senhas mais usadas e, assustadoramente, corrigi-las. Isso ocorre porque nós, humanos, somos preguiçosos e não queremos lembrar senhas complexas e complicadas. Portanto, criamos senhas que podemos lembrar facilmente, e isso leva ao fato de que, por exemplo, números ou caracteres são adicionados apenas no final da senha. Acreditando que a senha é segura, os padrões mencionados são usados para adivinhar com precisão esses "ajustes" dessas senhas. No entanto, algumas instruções e [guias de endurecimento](https://www.ssh-audit.com/hardening_guides.html) pode ser usado para endurecer nossos servidores SSH.

## Footprinting

Uma das ferramentas que podemos usar para imprimir a impressão digital no servidor SSH é [ssh-audit](https://github.com/jtesta/ssh-audit). Ele verifica a configuração do lado do cliente e do servidor e mostra algumas informações gerais e quais algoritmos de criptografia ainda são usados pelo cliente e pelo servidor. Obviamente, isso poderia ser explorado atacando o servidor ou o cliente no nível enigmático posteriormente.

#### Auditoria SSH

```shell-session
casluxd@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
casluxd@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
                                            `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
                                            `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
                                            `- [warn] using weak random number generator could reveal the key
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
...SNIP...
```

A primeira coisa que podemos ver nas primeiras linhas da saída é o banner que revela a versão do servidor OpenSSH. As versões anteriores tinham algumas vulnerabilidades, como [CVE-2020-14145](https://www.cvedetails.com/cve/CVE-2020-14145/), o que permitiu ao atacante a capacidade de Man-In-The-Middle e atacar a tentativa inicial de conexão. A saída detalhada da configuração de conexão com o servidor OpenSSH também pode fornecer informações importantes, como quais métodos de autenticação o servidor pode usar.

#### Alterar método de autenticação

```shell-session
casluxd@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config 
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```

Para possíveis ataques de força bruta, podemos especificar o método de autenticação com a opção de cliente SSH `PreferredAuthentications`.

```shell-session
casluxd@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```

Mesmo com esse serviço óbvio e seguro, recomendamos configurar nosso próprio servidor OpenSSH em nossa VM, experimentar e nos familiarizar com as diferentes configurações e opções.

Podemos encontrar vários banners para o servidor SSH durante nossos testes de penetração. Por padrão, os banners começam com a versão do protocolo que pode ser aplicada e depois a versão do próprio servidor. Por exemplo, com `SSH-1.99-OpenSSH_3.9p1`, sabemos que podemos usar as versões de protocolo SSH-1 e SSH-2 e estamos lidando com o servidor OpenSSH versão 3.9p1. Por outro lado, para um banner com `SSH-2.0-OpenSSH_8.2p1`, estamos lidando com uma versão 8.2p1 do OpenSSH que aceita apenas a versão do protocolo SSH-2.

## Rsync

[Rsync](https://linux.die.net/man/1/rsync) é uma ferramenta rápida e eficiente para copiar arquivos local e remotamente. Ele pode ser usado para copiar arquivos localmente em uma determinada máquina e de / para hosts remotos. É altamente versátil e conhecido por seu algoritmo de transferência delta. Esse algoritmo reduz a quantidade de dados transmitidos pela rede quando uma versão do arquivo já existe no host de destino. Isso é feito enviando apenas as diferenças entre os arquivos de origem e a versão mais antiga dos arquivos que residem no servidor de destino. É frequentemente usado para backups e espelhamento. Ele encontra arquivos que precisam ser transferidos observando arquivos que foram alterados em tamanho ou na última vez modificada. Por padrão, ele usa porta `873` e pode ser configurado para usar o SSH para transferências seguras de arquivos, pegando carona em cima de uma conexão estabelecida com o servidor SSH.

Este [guia](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync) cobre algumas das maneiras pelas quais o Rsync pode ser abusado, principalmente listando o conteúdo de uma pasta compartilhada em um servidor de destino e recuperando arquivos. Às vezes, isso pode ser feito sem autenticação. Outras vezes, precisaremos de credenciais. Se você encontrar credenciais durante um pentest e encontrar o Rsync em um host interno ( ou externo ), sempre vale a pena verificar a reutilização da senha, pois você poderá retirar alguns arquivos confidenciais que podem ser usados para obter acesso remoto ao destino.

Vamos fazer um pouco de pegada rápida. Podemos ver que o Rsync está em uso usando o protocolo 31.

```shell-session
casluxd@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```

#### Sondagem de ações acessíveis

Em seguida, podemos investigar um pouco o serviço para ver o que podemos obter acesso.

```shell-session
casluxd@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

#### Enumerando uma ação aberta

Aqui podemos ver uma ação chamada `dev`, e podemos enumerá-lo ainda mais.

```shell-session
casluxd@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

A partir da saída acima, podemos ver alguns arquivos interessantes que podem valer a pena ser retirados para investigar mais. Também podemos ver que um diretório que provavelmente contém chaves SSH está acessível. A partir daqui, poderíamos sincronizar todos os arquivos com o host de ataque com o comando `rsync -av rsync://127.0.0.1/dev`. Se o Rsync estiver configurado para usar o SSH para transferir arquivos, poderemos modificar nossos comandos para incluir o `-e ssh` bandeira ou `-e "ssh -p2222"` se uma porta não padrão estiver em uso para SSH. Este [guia](https://phoenixnap.com/kb/how-to-rsync-over-ssh) é útil para entender a sintaxe do uso de Rsync sobre SSH.

## Serviços R

Os serviços R são um conjunto de serviços hospedados para ativar o acesso remoto ou emitir comandos entre hosts Unix por TCP / IP. Inicialmente desenvolvido pelo Computer Systems Research Group (`CSRG`) na Universidade da Califórnia, Berkeley, `r-services` eram o padrão de fato para acesso remoto entre sistemas operacionais Unix até serem substituídos pelo Secure Shell (`SSH`Protocolos e comandos ) devido a falhas de segurança inerentes incorporadas a eles. Muito parecido `telnet`, os serviços r transmitem informações de cliente para servidor ( e vice-versa. ) pela rede em um formato não criptografado, possibilitando que os invasores interceptem o tráfego de rede ( senhas, informações de login etc.) realizando man-in-the-middle (`MITM`Ataques ).

`R-services` abranger as portas `512`, `513`, e `514` e só são acessíveis através de um conjunto de programas conhecidos como `r-commands`. Eles são mais comumente usados por sistemas operacionais comerciais, como Solaris, HP-UX e AIX. Embora menos comum hoje em dia, nós os encontramos de tempos em tempos durante nossos testes de penetração interna, por isso vale a pena entender como abordá-los.

O [Comandos R](https://en.wikipedia.org/wiki/Berkeley_r-commands) O conjunto consiste nos seguintes programas:

- rcp (`remote copy`)
- rexec (`remote execution`)
- rlogin (`remote login`)
- rsh (`remote shell`)
- rstat
- hora da ruptura
- rwho (`remote who`)

Cada comando tem sua funcionalidade pretendida; no entanto, cobriremos apenas os mais abusados `r-commands`. A tabela abaixo fornecerá uma visão geral rápida dos comandos mais frequentemente abusados, incluindo o daemon de serviço com o qual eles interagem, sobre qual método de porta e transporte ao qual eles podem ser acessados, e uma breve descrição de cada um.

|**Comando**|**Service Daemon**|**Porto**|**Protocolo de Transporte**|**Descrição**|
|---|---|---|---|---|
|`rcp`|`rshd`|514|TCP|Copie um arquivo ou diretório bidirecionalmente do sistema local para o sistema remoto ( ou vice-versa ) ou de um sistema remoto para outro. Funciona como o `cp` comando no Linux, mas fornece `no warning to the user for overwriting existing files on a system`.|
|`rsh`|`rshd`|514|TCP|Abre um shell em uma máquina remota sem um procedimento de login. Está relacionado às entradas confiáveis no `/etc/hosts.equiv` e `.rhosts` arquivos para validação.|
|`rexec`|`rexecd`|512|TCP|Permite que um usuário execute comandos shell em uma máquina remota. Requer autenticação através do uso de um `username` e `password` através de um soquete de rede não criptografado. A autenticação é substituída pelas entradas confiáveis no `/etc/hosts.equiv` e `.rhosts` arquivos.|
|`rlogin`|`rlogind`|513|TCP|Permite que um usuário efetue login em um host remoto pela rede. Funciona de maneira semelhante a `telnet` mas só pode se conectar a hosts semelhantes ao Unix. A autenticação é substituída pelas entradas confiáveis no `/etc/hosts.equiv` e `.rhosts` arquivos.|

O arquivo /etc/hosts.equiv contém uma lista de hosts confiáveis e é usado para conceder acesso a outros sistemas na rede. Quando os usuários de um desses hosts tentam acessar o sistema, eles recebem acesso automaticamente sem autenticação adicional.

#### /etc/hosts.equiv

```shell-session
casluxd@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

Agora que temos um entendimento básico de `r-commands`, vamos fazer algumas pegadas rápidas usando `Nmap` para determinar se todas as portas necessárias estão abertas.

## Footprinting de serviços R

```shell-session
casluxd@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```

#### Controle de acesso e relacionamentos confiáveis

A principal preocupação para `r-services`, e uma das principais razões `SSH` foi introduzido para substituí-lo, são os problemas inerentes ao controle de acesso para esses protocolos. Os serviços R contam com informações confiáveis enviadas do cliente remoto para a máquina host na qual estão tentando se autenticar. Por padrão, esses serviços utilizam [Módulos de autenticação conectáveis ( PAM )](https://web.mit.edu/rhel-doc/5/RHEL-5-manual/Deployment_Guide-en-US/ch-pam.html) para autenticação do usuário em um sistema remoto; no entanto, eles também ignoram essa autenticação através do uso do `/etc/hosts.equiv` e `.rhosts` arquivos no sistema. O `hosts.equiv` e `.rhosts` os arquivos contêm uma lista de hosts (`IPs` ou `Hostnames`) e usuários que são `trusted` pelo host local quando uma tentativa de conexão é feita usando `r-commands`. As entradas em qualquer arquivo podem aparecer da seguinte maneira:

**Nota:** O `hosts.equiv` o arquivo é reconhecido como a configuração global referente a todos os usuários em um sistema, enquanto `.rhosts` fornece uma configuração por usuário.

#### Arquivo .rhosts de amostra

```shell-session
casluxd@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```

Como podemos ver neste exemplo, os dois arquivos seguem a sintaxe específica de `<username> <ip address>` ou `<username> <hostname>` pares. Além disso, o `+` o modificador pode ser usado nesses arquivos como um curinga para especificar qualquer coisa. Neste exemplo, o `+` modificador permite que qualquer usuário externo acesse r-commands a partir do `htb-student` conta de usuário através do host com o endereço IP `10.0.17.10`.

As configurações incorretas em qualquer um desses arquivos podem permitir que um invasor se autentique como outro usuário sem credenciais, com o potencial de obter a execução do código. Agora que entendemos como podemos potencialmente abusar de configurações incorretas nesses arquivos, tente tentar fazer login em um host de destino usando `rlogin`.

```shell-session
casluxd@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```

Nós efetuamos login com sucesso em `htb-student` conta no host remoto devido às configurações incorretas no `.rhosts` Arquivo. Depois de fazer login com sucesso, também podemos abusar do `rwho` comando para listar todas as sessões interativas na rede local enviando solicitações para a porta UDP 513.

```shell-session
casluxd@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25       
```

A partir dessas informações, podemos ver que o `htb-student` atualmente o usuário está autenticado no `workstn01` anfitrião, enquanto o `root` usuário é autenticado no `web01` anfitrião. Podemos usar isso em nosso proveito ao definir possíveis nomes de usuário a serem usados durante novos ataques a hosts pela rede. No entanto, o `rwho` O daemon transmite periodicamente informações sobre usuários conectados, portanto, pode ser benéfico assistir ao tráfego da rede.

#### Listando usuários autenticados usando Rusers

Fornecer informações adicionais em conjunto com `rwho`, podemos emitir o `rusers` comando. Isso nos dará uma conta mais detalhada de todos os usuários conectados pela rede, incluindo informações como o nome de usuário, o nome do host da máquina acessada, TTY em que o usuário está logado, a data e a hora em que o usuário fez login, a quantidade de tempo desde que o usuário digitou no teclado, e o host remoto do qual eles efetuaram login ( se aplicável ).

```shell-session
casluxd@htb[/htb]$ rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25
```

Como podemos ver, os serviços R são usados com menos frequência atualmente devido a suas falhas de segurança inerentes e à disponibilidade de protocolos mais seguros, como SSH. Para ser um profissional de segurança da informação completo, precisamos ter um entendimento amplo e profundo de muitos sistemas, aplicativos, protocolos etc. Portanto, arquive esse conhecimento sobre os serviços R, porque você nunca sabe quando pode encontrá-los.

## Pensamentos finais

Os serviços de gerenciamento remoto podem nos fornecer um tesouro de dados e geralmente são abusados por acesso não autorizado por meio de credenciais fracas / padrão ou reutilização de senha. Devemos sempre investigar esses serviços com o máximo de informações possível e não deixar pedra sobre pedra, especialmente quando compilamos uma lista de credenciais de outras partes da rede de destino.

