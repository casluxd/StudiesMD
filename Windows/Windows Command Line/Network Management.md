
O PowerShell expandiu nossos recursos no sistema operacional Windows ao lidar com configurações de rede, aplicativos e muito mais. Esta seção abordará como verificar suas configurações de rede, como endereços IP, configurações de adaptador e configurações de DNS. Também abordaremos como habilitar e gerenciar o acesso remoto ao host utilizando WinRM e SSH.

`Cenário: Para garantir que o host do Sr. Tanaka esteja funcionando corretamente e que possamos gerenciá-lo remotamente do escritório de TI, faremos uma verificação rápida, validaremos as configurações do host e habilitaremos o gerenciamento remoto para o host.`

# O que é rede dentro de uma rede Windows?

A rede com hosts do Windows funciona como qualquer outro host baseado em Linux ou Unix. A pilha TCP/IP, protocolos sem fio e outros aplicativos tratam a maioria dos dispositivos da mesma forma, então não há muito o que aprender que seja novo. Este módulo pressupõe que você conheça os protocolos básicos de rede e como o tráfego de rede típico atravessa a Internet.

Onde as coisas ficam um pouco diferentes está em como os hosts Windows se comunicam uns com os outros, domínios e outros hosts Linux. Abaixo, abordaremos rapidamente alguns protocolos padrão que você pode encontrar ao administrar ou testar hosts do Windows.
- SMB: O SMB fornece aos hosts do Windows a capacidade de compartilhar recursos, arquivos e uma forma padrão de autenticação entre hosts para determinar se o acesso aos recursos é permitido. Para outras distros, o SAMBA é a opção de código aberto.
- Netbios: O próprio NetBios não é diretamente um serviço ou protocolo, mas um mecanismo de conexão e conversação amplamente utilizado em redes. Era o mecanismo de transporte original para SMB, mas isso mudou desde então. Agora ele serve como um mecanismo de identificação alternativo quando o DNS falha. Também pode ser conhecido como NBT-NS (serviço de nome NetBIOS).
- LDAP: O LDAP é um protocolo de plataforma cruzada de software livre usado para autenticação e autorização com vários serviços de diretório. É assim que muitos dispositivos diferentes em redes modernas podem se comunicar com grandes serviços de estrutura de diretório, como o Active Directory.
- LLLMNR: O LLMNR fornece um serviço de resolução de nomes baseado em DNS e funciona se o DNS não estiver disponível ou funcionando. Este protocolo é um protocolo multicast e, como tal, funciona apenas em links locais (dentro de um domínio de broadcast normal, não em links da camada três).
- DNS: DNS é um padrão de nomenclatura comum usado na Internet e na maioria dos tipos de rede modernos. O DNS nos permite referenciar hosts por um nome exclusivo em vez de seu endereço IP. É assim que podemos referenciar um site por "WWW.google.com" em vez de "8.8.8.8". Internamente é assim que solicitamos recursos e acessos de uma rede.
- HTTP/HTTPS: HTTP e HTTPS são a forma insegura e segura de solicitar e utilizar recursos pela Internet. Esses protocolos são usados ​​para acessar e utilizar recursos como servidores da Web, enviar e receber dados de fontes remotas e muito mais.
- Kerberos: Kerberos é um protocolo de autenticação em nível de rede. Nos tempos modernos, é mais provável que vejamos isso ao lidar com a autenticação do Active Directory, quando os clientes solicitam tíquetes de autorização para usar os recursos do domínio.
- WinRM: WinRM É uma implementação do protocolo WS-Management. Ele pode ser usado para gerenciar as funcionalidades de hardware e software dos hosts. Ele é usado principalmente na administração de TI, mas também pode ser usado para enumeração de host e como um mecanismo de script.
- RDP: RDP é uma implementação do Windows de um protocolo de serviços de interface do usuário de rede que fornece aos usuários uma interface gráfica para acessar hosts por meio de uma conexão de rede.
- SSH: SSH é um protocolo seguro que pode ser usado para acesso seguro ao host, transferência de arquivos e comunicação geral entre hosts de rede. Ele fornece uma maneira segura de acessar hosts e serviços em redes inseguras.

Claro, esta lista não é abrangente, mas é um excelente começo geral do que normalmente veríamos ao nos comunicarmos com hosts do Windows. Agora vamos discutir acesso local versus acesso remoto.

## Local vs. Remote Access?

#### Local Access

O acesso ao host local é quando estamos diretamente no terminal utilizando seus recursos como você está agora no seu PC. Normalmente, isso não exigirá que usemos nenhum protocolo de acesso específico, exceto quando solicitamos recursos de hosts em rede ou tentamos acessar a Internet. A seguir, mostraremos alguns cmdlets e outras formas de verificar e validar as configurações de rede em nossos hosts.

#### Querying Networking Settings

Antes de mais nada, vamos validar as configurações de rede no host do Sr. Tanaka. Começaremos executando o comando IPConfig. Este não é um comando nativo do PowerShell, mas é compatível.

```powershell-session
PS C:\htb> ipconfig 

Windows IP Configuration

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : .htb
   Link-local IPv6 Address . . . . . : fe80::c5ca:594d:759d:e0c1%11
   IPv4 Address. . . . . . . . . . . : 10.129.203.105
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::250:56ff:feb9:b9fc%11
                                       10.129.0.1
```

Como podemos ver, o ipconfig nos mostrará as configurações básicas da sua interface de rede. Temos como saída os endereços IPv4/6, nosso gateway, máscaras de sub-rede e sufixo DNS, se houver um definido. Podemos gerar as configurações de rede completas anexando o modificador /all ao comando ipconfig da seguinte forma:

```powershell-session
PS C:\htb> ipconfig /all 

Windows IP Configuration

   Host Name . . . . . . . . . . . . : ICL-WIN11
   Primary Dns Suffix  . . . . . . . : greenhorn.corp
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : greenhorn.corp
                                       htb

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : .htb
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter
   Physical Address. . . . . . . . . : 00-50-56-B9-4F-CB
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : dead:beef::222(Preferred)
   Lease Obtained. . . . . . . . . . : Monday, October 17, 2022 9:40:14 AM
   Lease Expires . . . . . . . . . . : Tuesday, October 25, 2022 9:59:17 AM
   <SNIP>
   IPv4 Address. . . . . . . . . . . : 10.129.203.105(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Lease Obtained. . . . . . . . . . : Monday, October 17, 2022 9:40:13 AM
   Lease Expires . . . . . . . . . . : Tuesday, October 25, 2022 10:10:16 AM
   Default Gateway . . . . . . . . . : fe80::250:56ff:feb9:b9fc%11
                                       10.129.0.1
   DHCP Server . . . . . . . . . . . : 10.129.0.1
   DHCPv6 IAID . . . . . . . . . . . : 335564886
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2A-3D-00-D6-00-50-56-B9-4F-CB
   DNS Servers . . . . . . . . . . . : 1.1.1.1
                                       8.8.8.8
   NetBIOS over Tcpip. . . . . . . . : Enabled
   Connection-specific DNS Suffix Search List :
                                       htb

Ethernet adapter Ethernet2:

   Connection-specific DNS Suffix  . :
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter #2
   Physical Address. . . . . . . . . : 00-50-56-B9-F5-7E
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::d1fb:79d5:6d0b:41de%14(Preferred)
   IPv4 Address. . . . . . . . . . . : 172.16.5.100(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 172.16.5.1
   DHCPv6 IAID . . . . . . . . . . . : 318787670
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2A-3D-00-D6-00-50-56-B9-4F-CB
   DNS Servers . . . . . . . . . . . : 172.16.5.155
   NetBIOS over Tcpip. . . . . . . . : Enabled
```

Agora podemos ver muito mais informações do que antes. Somos apresentados a uma saída contendo vários adaptadores, configurações de host, mais detalhes sobre se nossos endereços IP foram configurados manualmente ou concessões de DHCP, quanto tempo duram essas concessões e muito mais. Então, parece que o host do Sr. Tanaka tem uma configuração de endereço IP adequada. Digno de nota, e particularmente interessante para nós como pentesters, é que esse host é dual-homed. Queremos dizer que tem várias interfaces de rede conectadas a redes separadas. Isso torna o anfitrião do Sr. Tanakas um grande alvo se estivermos procurando uma base na rede e desejamos ter uma maneira de migrar entre as redes.

Vamos ver as configurações do Arp e ver se seu host se comunicou com outras pessoas na rede. Para relembrar, o ARP é um protocolo utilizado para converter endereços IP em endereços físicos. O endereço físico é usado em níveis inferiores dos modelos OSI/TCP-IP para comunicação. Para exibir as entradas ARP atuais do host, usaremos a opção -a.

```powershell-session
PS C:\htb> arp -a

Interface: 10.129.203.105 --- 0xb
  Internet Address      Physical Address      Type
  10.129.0.1            00-50-56-b9-b9-fc     dynamic
  10.129.204.58         00-50-56-b9-5f-41     dynamic
  10.129.255.255        ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
  255.255.255.255       ff-ff-ff-ff-ff-ff     static

Interface: 172.16.5.100 --- 0xe
  Internet Address      Physical Address      Type
  172.16.5.155          00-50-56-b9-e2-30     dynamic
  172.16.5.255          ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
```

A saída de Arp -a é bem simples. Recebemos entradas de nossos adaptadores de rede sobre os hosts que ele conhece ou com os quais se comunicou recentemente. Não surpreendentemente, como esse host é relativamente novo, ele ainda não se comunicou com muitos hosts. Apenas os gateways, nosso host remoto e o host 172.16.5.155, o controlador de domínio para Greenhorn.corp. Nada louco para ser visto aqui. Agora vamos validar se nossa configuração de DNS está funcionando corretamente. Utilizaremos o nslookup, uma ferramenta de consulta DNS integrada, para tentar resolver o endereço IP/nome DNS do controlador de domínio Greenhorn.

```powershell-session
PS C:\htb> nslookup ACADEMY-ICL-DC

DNS request timed out.
    timeout was 2 seconds.
Server:  UnKnown
Address:  172.16.5.155

Name:    ACADEMY-ICL-DC.greenhorn.corp
Address:  172.16.5.155
```

Agora que validamos as configurações de DNS do Sr. Tanaka, vamos verificar as portas abertas no host. Podemos fazer isso usando netstat -an. O Netstat exibirá as conexões de rede atuais para nosso host. A opção -an imprimirá todas as conexões e portas de escuta e as colocará na forma numérica.

```powershell-session
PS C:\htb> netstat -an 

netstat -an

Active Connections

  Proto  Local Address          Foreign Address        State
  TCP    0.0.0.0:22             0.0.0.0:0              LISTENING
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING
  TCP    0.0.0.0:5985           0.0.0.0:0              LISTENING
  TCP    0.0.0.0:47001          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49671          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49673          0.0.0.0:0              LISTENING
  TCP    0.0.0.0:49674          0.0.0.0:0              LISTENING
  TCP    10.129.203.105:22      10.10.14.19:32557      ESTABLISHED
  TCP    172.16.5.100:139       0.0.0.0:0              LISTENING
  TCP    [::]:22                [::]:0                 LISTENING
  TCP    [::]:135               [::]:0                 LISTENING
  TCP    [::]:445               [::]:0                 LISTENING
  TCP    [::]:3389              [::]:0                 LISTENING
  TCP    [::]:5985              [::]:0                 LISTENING
  TCP    [::]:47001             [::]:0                 LISTENING
  TCP    [::]:49664             [::]:0                 LISTENING
  TCP    [::]:49665             [::]:0                 LISTENING
  TCP    [::]:49666             [::]:0                 LISTENING
  TCP    [::]:49667             [::]:0                 LISTENING
  TCP    [::]:49668             [::]:0                 LISTENING
  TCP    [::]:49671             [::]:0                 LISTENING
  TCP    [::]:49673             [::]:0                 LISTENING
  TCP    [::]:49674             [::]:0                 LISTENING
  UDP    0.0.0.0:123            *:*
<SNIP>
  UDP    172.16.5.100:137       *:*
  UDP    172.16.5.100:138       *:*
  UDP    172.16.5.100:1900      *:*
  UDP    172.16.5.100:54453     *:*
```

Agora, você pode precisar obter um histórico em observar o tráfego de rede ou entender as portas e protocolos padrão, ou então o acima pode parecer sem sentido. Tudo bem, no entanto. Olhando acima, podemos ver quais portas estão abertas e se temos alguma conexão ativa. A partir da saída acima, as portas abertas são todas comumente usadas em ambientes Windows e seriam esperadas. A maioria lida com serviços do Active Directory e SSH. Ao olhar para as conexões, vemos apenas uma sessão atualmente ativa: nossa própria conexão SSH na porta TCP 22.

A maioria desses comandos com os quais praticamos até agora são executáveis ​​internos do Windows e são úteis para uma visão rápida de um host, mas não para muito mais. A seguir, abordaremos vários cmdlets que são adições do PowerShell que nos permitem gerenciar nossas conexões de rede de maneira granular.

O PowerShell tem vários cmdlets integrados poderosos feitos para lidar com serviços de rede e administração. Os módulos NetAdapter, NetConnection e NetTCPIP são apenas alguns dos que praticaremos hoje.

#### Net Cmdlets

- Get-NetIPInterface: Recupere todas as propriedades visíveis do adaptador de rede.
- Get-NetIPAddress: Recupera as configurações de IP de cada adaptador. Semelhante ao IPConfig.
- Get-NetNeighbor: Recupera as entradas vizinhas do cache. Semelhante a arp -a.
- Get-Netroute: Imprime a tabela de rotas atual. Semelhante ao IPRoute.
- Set-NetAdapter: Defina as propriedades básicas do adaptador no nível da camada 2, como id de VLAN, descrição e endereço MAC.
- Set-NetIPInterface: Modifica as configurações de uma interface para incluir status de DHCP, MTU e outras métricas.
- Set-NetIPAddress: Modifica a configuração de um adaptador de rede.
- Disable-NetAdapter: Usado para desabilitar as interfaces do adaptador de rede.
- Enable-NetAdapter: Usado para ligar novamente os adaptadores de rede e permitir conexões de rede.
- Restart-NetAdapter: Usado para reiniciar um adaptador. Pode ser útil para ajudar a enviar alterações feitas nas configurações do adaptador.
- test-NetConnection: Permite que verificações de diagnóstico sejam executadas em uma conexão. Ele suporta ping, tcp, rastreamento de rota e muito mais.

Não mostraremos cada cmdlet em uso, mas seria prudente fornecer uma referência rápida para seu uso. Primeiro, vamos começar com Get-NetIPInterface.

```powershell
PS C:\htb> get-netIPInterface

ifIndex InterfaceAlias                  AddressFamily NlMtu(Bytes) InterfaceMetric Dhcp     ConnectionState PolicyStore
------- --------------                  ------------- ------------ --------------- ----     --------------- -----------
20      Ethernet 3                      IPv6                  1500              25 Enabled  Disconnected    ActiveStore
14      VMware Network Adapter VMnet8   IPv6                  1500              35 Enabled  Connected       ActiveStore
8       VMware Network Adapter VMnet2   IPv6                  1500              35 Enabled  Connected       ActiveStore
10      VMware Network Adapter VMnet1   IPv6                  1500              35 Enabled  Connected       ActiveStore
17      Local Area Connection* 2        IPv6                  1500              25 Enabled  Disconnected    ActiveStore
21      Bluetooth Network Connection    IPv6                  1500              65 Disabled Disconnected    ActiveStore
15      Local Area Connection* 1        IPv6                  1500              25 Disabled Disconnected    ActiveStore
25      Wi-Fi                           IPv6                  1500              40 Enabled  Connected       ActiveStore
7       Local Area Connection           IPv6                  1500              25 Enabled  Disconnected    ActiveStore
1       Loopback Pseudo-Interface 1     IPv6            4294967295              75 Disabled Connected       ActiveStore
20      Ethernet 3                      IPv4                  1500              25 Enabled  Disconnected    ActiveStore
14      VMware Network Adapter VMnet8   IPv4                  1500              35 Disabled Connected       ActiveStore
8       VMware Network Adapter VMnet2   IPv4                  1500              35 Disabled Connected       ActiveStore
10      VMware Network Adapter VMnet1   IPv4                  1500              35 Disabled Connected       ActiveStore
17      Local Area Connection* 2        IPv4                  1500              25 Disabled Disconnected    ActiveStore
21      Bluetooth Network Connection    IPv4                  1500              65 Enabled  Disconnected    ActiveStore
15      Local Area Connection* 1        IPv4                  1500              25 Enabled  Disconnected    ActiveStore
25      Wi-Fi                           IPv4                  1500              40 Enabled  Connected       ActiveStore
7       Local Area Connection           IPv4                  1500               1 Disabled Disconnected    ActiveStore
1       Loopback Pseudo-Interface 1     IPv4            4294967295              75 Disabled Connected       ActiveStore
```

Esta listagem nos mostra nossas interfaces disponíveis no host de uma maneira um tanto complicada. Recebemos muitas métricas, mas os adaptadores são divididos por AddressFamily. Portanto, vemos entradas para cada adaptador duas vezes se IPv4 e IPv6 estiverem ativados nessa interface específica. As propriedades ifindex e InterfaceAlias ​​são particularmente úteis. Essas propriedades facilitam o uso de outros cmdlets fornecidos pelo módulo NetTCPIP. Vamos obter as informações do adaptador para nossa conexão Wi-Fi em ifIndex 25 utilizando o cmdlet Get-NetIPAddress.

```powershell
PS C:\htb> Get-NetIPAddress -ifIndex 25

IPAddress         : fe80::a0fc:2e3d:c92a:48df%25
InterfaceIndex    : 25
InterfaceAlias    : Wi-Fi
AddressFamily     : IPv6
Type              : Unicast
PrefixLength      : 64
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 192.168.86.211
InterfaceIndex    : 25
InterfaceAlias    : Wi-Fi
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Dhcp
SuffixOrigin      : Dhcp
AddressState      : Preferred
ValidLifetime     : 21:35:36
PreferredLifetime : 21:35:36
SkipAsSource      : False
PolicyStore       : ActiveStore
```

Este cmdlet também retornou algumas informações. Observe como usamos o número ifIndex para solicitar as informações? Podemos fazer o mesmo com o InterfaceAlias ​​também. Esse cmdlet retorna muitas informações, como índice, alias, estado DHCP, tipo de interface e outras métricas. Isso reflete a maior parte do que veríamos se emitissemos o executável IPconfig no prompt de comando. Agora, e se quisermos modificar uma configuração na interface? Podemos fazer isso com os cmdlets Set-NetIPInterface e Set-NetIPAddress. Neste exemplo, digamos que queremos alterar o status DHCP da interface de ativado para desativado e alterar o IP de um atribuído automaticamente pelo DHCP para um de nossa escolha definido manualmente. Faríamos isso assim:

```powershell
PS C:\htb> Set-NetIPInterface -InterfaceIndex 25 -Dhcp Disabled
```

Desativando a propriedade DHCP com o cmdlet Set-NetIPInterface, agora podemos definir nosso endereço IP manual. Fazemos isso com o cmdlet Set-NetIPAddress.

```powershell
PS C:\htb> Set-NetIPAddress -InterfaceIndex 25 -IPAddress 10.10.100.54 -PrefixLength 24

PS C:\htb> Get-NetIPAddress -ifindex 20 | ft InterfaceIndex,InterfaceAlias,IPAddress,PrefixLength

InterfaceIndex InterfaceAlias IPAddress                   PrefixLength
-------------- -------------- ---------                   ------------
            20 Ethernet 3     fe80::7408:bbf:954a:6ae5%20           64
            20 Ethernet 3     10.10.100.54                          24

PS C:\htb> Get-NetIPinterface -ifindex 20 | ft ifIndex,InterfaceAlias,Dhcp

ifIndex InterfaceAlias     Dhcp
------- --------------     ----
     20 Ethernet 3     Disabled
     20 Ethernet 3     Disabled
```

O comando acima agora define nosso endereço IP como 10.10.100.54 e o PrefixLength (também conhecido como máscara de sub-rede) como 24. Observando nossas verificações, podemos ver que essas configurações estão em vigor. Por segurança, vamos reiniciar nosso adaptador de rede e testar nossa conexão para ver se ela persiste.

```powershell
PS C:\htb> Restart-NetAdapter -Name 'Ethernet 3'
```

Desde que nada dê errado, você não receberá a saída. Portanto, quando se trata de Restart-NetAdapter, nenhuma notícia é uma boa notícia. A maneira mais fácil de informar ao cmdlet qual interface reiniciar é com a propriedade Name, que é igual à InterfaceAlias ​​dos comandos anteriores que executamos. Agora, para garantir que ainda temos uma conexão, podemos usar o cmdlet Test-NetConnection.

```powershell
PS C:\htb> Test-NetConnection

ComputerName           : <snip>msedge.net
RemoteAddress          : 13.107.4.52
InterfaceAlias         : Ethernet 3
SourceAddress          : 10.10.100.54
PingSucceeded          : True
PingReplyDetails (RTT) : 44 ms
```

Agora, o Test-NetConnection é um cmdlet poderoso e pode testar mais do que apenas se pudermos alcançar outro host e ter conectividade de rede. Ele pode nos informar sobre nossos resultados de TCP, métricas detalhadas, diagnóstico de rota e muito mais. Valeria a pena dar uma olhada neste artigo da Microsoft em Test-NetConnection. Agora que concluímos nossa tarefa e validamos as configurações de rede do Sr. Tanaka em seu host, vamos discutir um pouco a conectividade de acesso remoto.

### Remote Access

Quando não podemos acessar sistemas Windows ou precisamos gerenciar hosts remotamente, podemos utilizar PowerShell, SSH e RDP, entre outras ferramentas, para realizar nosso trabalho. Vamos abordar as principais formas de habilitar e usar o acesso remoto. Primeiro, discutiremos o SSH.

### Como habilitar o remote access? (SSH, PSSessions, etc.)

Podemos usar o SSH para acessar o PowerShell em um sistema Windows pela rede. A partir de 2018, o SSH por meio do cliente OpenSSH e dos aplicativos de servidor foi acessível e incluído em todas as versões do Windows Server e do cliente. É um mecanismo de comunicação extensível e fácil de usar para nosso uso administrativo. Configurar o OpenSSH em nossos hosts é simples. Vamos tentar. Devemos instalar o componente SSH Server e o aplicativo cliente para acessar um host via SSH remotamente.

Podemos configurar um servidor SSH em um destino do Windows usando o cmdlet Add-WindowsCapability e confirmar se ele foi instalado com êxito usando o cmdlet Get-WindowsCapability.

```powershell
PS C:\Users\htb-student> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent

PS C:\Users\htb-student> Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

Path          :
Online        : True
RestartNeeded : False

PS C:\Users\htb-student> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent

PS C:\Users\htb-student> Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

Path          :
Online        : True
RestartNeeded : False

PS C:\Users\htb-student> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : Installed
```

Depois de confirmar que o SSH está instalado, podemos usar o cmdlet Start-Service para iniciar o serviço SSH. Também podemos usar o cmdlet Set-Service para definir as configurações de inicialização do serviço SSH, se quisermos.

```powershell
PS C:\Users\htb-student> Start-Service sshd  
  
PS C:\Users\htb-student> Set-Service -Name sshd -StartupType 'Automatic'  
```

Observação: a configuração inicial dos serviços de acesso remoto não será um requisito neste módulo para concluir as perguntas de desafio. Com cada um dos desafios deste módulo, o acesso remoto já está definido e configurado. No entanto, será necessário entender como conectar e aplicar os conceitos abordados ao longo do módulo. As etapas de instalação e configuração são fornecidas para ajudar a entender os erros comuns de configuração e, em alguns casos, as melhores práticas de segurança. Sinta-se à vontade para tentar algumas etapas de configuração em sua própria VM pessoal.

Com o SSH instalado e em execução em um destino Windows, podemos nos conectar pela rede com um cliente SSH.

```powershell
PS C:\Users\administrator> ssh htb-student@10.129.224.248

htb-student@10.129.224.248 password:
```

Por padrão, isso nos conectará a uma sessão CMD, mas podemos digitar powershell para entrar em uma sessão PowerShell, conforme mencionado anteriormente nesta seção.

### Habilitando WinRM

O Gerenciamento Remoto do Windows (WinRM) pode ser configurado usando cmdlets dedicados do PowerShell e podemos entrar em uma sessão interativa do PowerShell, bem como emitir comandos em destinos remotos do Windows. Notaremos que o WinRM é mais comumente ativado em sistemas operacionais Windows Server, para que os administradores de TI possam executar tarefas em um ou vários hosts. Ele é ativado por padrão no Windows Server.

Devido à crescente demanda pela capacidade de gerenciar e automatizar tarefas remotamente em sistemas Windows, provavelmente veremos o WinRM ativado em mais e mais sistemas operacionais de desktop Windows (Windows 10 e Windows 11). Quando o WinRM está ativado em um destino do Windows, ele escuta nas portas lógicas 5985 e 5986.

```powershell
PS C:\WINDOWS\system32> winrm quickconfig

WinRM service is already running on this machine.
WinRM is not set up to allow remote access to this machine for management.
The following changes must be made:

Enable the WinRM firewall exception.
Configure LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.

Make these changes [y/n]? y

WinRM has been updated for remote management.

WinRM firewall exception enabled.
Configured LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.
```

Como pode ser visto na saída acima, a execução deste comando garantirá automaticamente que todas as configurações necessárias sejam feitas para:
- Habilitar o serviço WinRM
- Permitir WinRM através do Firewall do Windows Defender (entrada e saída)
- Conceder direitos administrativos remotamente a usuários locais

Contanto que as credenciais para acessar o sistema sejam conhecidas, qualquer pessoa que possa alcançar o alvo pela rede pode se conectar após a execução do comando. Os administradores de TI devem tomar medidas adicionais para fortalecer essas configurações do WinRM, especialmente se o sistema for acessível remotamente pela Internet. Entre algumas dessas opções de endurecimento estão:

- Configure TrustedHosts para incluir apenas endereços IP/nomes de host que serão usados ​​para gerenciamento remoto
- Configurar HTTPS para transporte
- Junte sistemas Windows em um ambiente de domínio do Active Directory e aplique a autenticação Kerberos

Depois de habilitar e configurar o WinRM, podemos testar o acesso remoto usando o cmdlet Test-WSMan PowerShell.

```powershell
PS C:\Users\administrator> Test-WSMan -ComputerName "10.129.224.248"

wsmid           : http://schemas.dmtf.org/wbem/wsman/identity/1/wsmanidentity.xsd
ProtocolVersion : http://schemas.dmtf.org/wbem/wsman/1/wsman.xsd
ProductVendor   : Microsoft Corporation
ProductVersion  : OS: 0.0.0 SP: 0.0 Stack: 3.0
```

A execução desse cmdlet envia uma solicitação que verifica se o serviço WinRM está em execução. Lembre-se de que isso não é autenticado, portanto, nenhuma credencial é usada, e é por isso que nenhuma versão do sistema operacional é detectada. Isso nos mostra que o serviço WinRM está sendo executado no destino.

```powershell
PS C:\Users\administrator> Test-WSMan -ComputerName "10.129.224.248" -Authentication Negotiate


wsmid           : http://schemas.dmtf.org/wbem/wsman/identity/1/wsmanidentity.xsd
ProtocolVersion : http://schemas.dmtf.org/wbem/wsman/1/wsman.xsd
ProductVendor   : Microsoft Corporation
ProductVersion  : OS: 10.0.17763 SP: 0.0 Stack: 3.0
```

Podemos executar o mesmo comando com a opção -Authentication Negoitate para testar se o WinRM está autenticado e receberemos a versão do SO (10.0.11764).

Também temos a opção de usar o cmdlet Enter-PSSession para estabelecer uma sessão do PowerShell com um destino do Windows.

```powershell
PS C:\Users\administrator> Enter-PSSession -ComputerName 10.129.224.248 -Credential htb-student -Authentication Negotiate
[10.129.5.129]: PS C:\Users\htb-student\Documents> $PSVersionTable 
  
Name                           Value
----                           -----
PSVersion                      5.1.17763.592
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.592
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Podemos executar essa mesma ação a partir de um host de ataque baseado em Linux com o núcleo do PowerShell instalado (como no Pwnbox). Lembre-se de que o PowerShell não é exclusivo do Windows e agora será executado em outros sistemas operacionais.

Além de ser independente do sistema operacional, agora existem várias ferramentas diferentes que podemos usar para interagir remotamente com os hosts. A escolha de um meio para administrar remotamente nossos hosts depende principalmente do que você se sente confortável e do que pode usar com base no envolvimento ou nas configurações de segurança do seu ambiente.