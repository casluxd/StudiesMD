
Os protocolos da Internet são regras e diretrizes padronizadas definidas em RFCs que especificam como os dispositivos em uma rede devem se comunicar uns com os outros. Eles garantem que os dispositivos em uma rede possam trocar informações de forma consistente e confiável, independentemente do hardware e do software usados. Para que os dispositivos se comuniquem em uma rede, eles precisam estar conectados por meio de um canal de comunicação, como uma conexão com ou sem fio. Os dispositivos então trocam informações usando um conjunto de protocolos padronizados que definem o formato e a estrutura dos dados que estão sendo transmitidos. Os dois principais tipos de conexões usadas em redes são Transmission Control Protocol (TCP) e User Datagram Protocol (UDP).

Precisamos lidar e conhecer os diferentes e mais utilizados protocolos. Como já aprendemos, esses protocolos são a base de toda a comunicação entre nossos dispositivos e computadores nas redes. Compilamos abaixo muitos desses protocolos com os quais iremos lidar ao longo dos módulos. Quanto melhor os entendermos, mais efetivamente poderemos trabalhar com eles.

### Transmission Control Protocol

TCP é um protocolo orientado a conexão que estabelece uma conexão virtual entre dois dispositivos antes de transmitir dados usando um three-way-handshake. Essa conexão é mantida até que a transferência de dados seja concluída e os dispositivos podem continuar a enviar e receber dados enquanto a conexão estiver ativa.

Por exemplo, quando inserimos um URL em nosso navegador da Web, o navegador envia uma solicitação HTTP para o servidor que hospeda o site usando TCP. O servidor responde enviando o código HTML do site de volta ao navegador usando TCP. O navegador então usa esse código para renderizar o site em nossa tela. Esse processo depende do estabelecimento de uma conexão TCP entre o navegador e o servidor da Web e mantida até que a transferência de dados seja concluída. Como resultado, o TCP é confiável, mas mais lento do que o UDP porque requer sobrecarga adicional para estabelecer e manter a conexão.

|**Protocol**|**Acronym**|**Port**|**Description**|
|---|---|---|---|
|Telnet|`Telnet`|`23`|Remote login service|
|Secure Shell|`SSH`|`22`|Secure remote login service|
|Simple Network Management Protocol|`SNMP`|`161-162`|Manage network devices|
|Hyper Text Transfer Protocol|`HTTP`|`80`|Used to transfer webpages|
|Hyper Text Transfer Protocol Secure|`HTTPS`|`443`|Used to transfer secure webpages|
|Domain Name System|`DNS`|`53`|Lookup domain names|
|File Transfer Protocol|`FTP`|`20-21`|Used to transfer files|
|Trivial File Transfer Protocol|`TFTP`|`69`|Used to transfer files|
|Network Time Protocol|`NTP`|`123`|Synchronize computer clocks|
|Simple Mail Transfer Protocol|`SMTP`|`25`|Used for email transfer|
|Post Office Protocol|`POP3`|`110`|Used to retrieve emails|
|Internet Message Access Protocol|`IMAP`|`143`|Used to access emails|
|Server Message Block|`SMB`|`445`|Used to transfer files|
|Network File System|`NFS`|`111`, `2049`|Used to mount remote systems|
|Bootstrap Protocol|`BOOTP`|`67`, `68`|Used to bootstrap computers|
|Kerberos|`Kerberos`|`88`|Used for authentication and authorization|
|Lightweight Directory Access Protocol|`LDAP`|`389`|Used for directory services|
|Remote Authentication Dial-In User Service|`RADIUS`|`1812`, `1813`|Used for authentication and authorization|
|Dynamic Host Configuration Protocol|`DHCP`|`67`, `68`|Used to configure IP addresses|
|Remote Desktop Protocol|`RDP`|`3389`|Used for remote desktop access|
|Network News Transfer Protocol|`NNTP`|`119`|Used to access newsgroups|
|Remote Procedure Call|`RPC`|`135`, `137-139`|Used to call remote procedures|
|Identification Protocol|`Ident`|`113`|Used to identify user processes|
|Internet Control Message Protocol|`ICMP`|`0-255`|Used to troubleshoot network issues|
|Internet Group Management Protocol|`IGMP`|`0-255`|Used for multicasting|
|Oracle DB (Default/Alternative) Listener|`oracle-tns`|`1521`/`1526`|The Oracle database default/alternative listener is a service that runs on the database host and receives requests from Oracle clients.|
|Ingres Lock|`ingreslock`|`1524`|Ingres database is commonly used for large commercial applications and as a backdoor that can execute commands remotely via RPC.|
|Squid Web Proxy|`http-proxy`|`3128`|Squid web proxy is a caching and forwarding HTTP web proxy used to speed up a web server by caching repeated requests.|
|Secure Copy Protocol|`SCP`|`22`|Securely copy files between systems|
|Session Initiation Protocol|`SIP`|`5060`|Used for VoIP sessions|
|Simple Object Access Protocol|`SOAP`|`80`, `443`|Used for web services|
|Secure Socket Layer|`SSL`|`443`|Securely transfer files|
|TCP Wrappers|`TCPW`|`113`|Used for access control|
|Network Time Protocol|`NTP`|`123`|Synchronize computer clocks|
|Internet Security Association and Key Management Protocol|`ISAKMP`|`500`|Used for VPN connections|
|Microsoft SQL Server|`ms-sql-s`|`1433`|Used for client connections to the Microsoft SQL Server.|
|Kerberized Internet Negotiation of Keys|`KINK`|`892`|Used for authentication and authorization|
|Open Shortest Path First|`OSPF`|`520`|Used for routing|
|Point-to-Point Tunneling Protocol|`PPTP`|`1723`|Is used to create VPNs|
|Remote Execution|`REXEC`|`512`|This protocol is used to execute commands on remote computers and send the output of commands back to the local computer.|
|Remote Logion|`RLOGIN`|`513`|This protocol starts an interactive shell session on a remote computer.|
|X Window System|`X11`|`6000`|It is a computer software system and network protocol that provides a graphical user interface (GUI) for networked computers.|
|Relational Database Management System|`DB2`|`50000`|RDBMS is designed to store, retrieve and manage data in a structured format for enterprise applications such as financial systems, customer relationship management (CRM) systems.|

## User Datagram Protocol

Por outro lado, o UDP é um protocolo sem conexão, o que significa que não estabelece uma conexão virtual antes de transmitir os dados. Em vez disso, ele envia os pacotes de dados ao destino sem verificar se foram recebidos.

Por exemplo, quando transmitimos ou assistimos a um vídeo em uma plataforma como o YouTube, os dados do vídeo são transmitidos ao nosso dispositivo usando UDP. Isso ocorre porque o vídeo pode tolerar alguma perda de dados e a velocidade de transmissão é mais importante do que a confiabilidade. Se alguns pacotes de dados de vídeo forem perdidos ao longo do caminho, isso não afetará significativamente a qualidade geral do vídeo. Isso torna o UDP mais rápido que o TCP, mas menos confiável porque não há garantia de que os pacotes chegarão ao seu destino.

|**Protocol**|**Acronym**|**Port**|**Description**|
|---|---|---|---|
|Domain Name System|`DNS`|`53`|It is a protocol to resolve domain names to IP addresses.|
|Trivial File Transfer Protocol|`TFTP`|`69`|It is used to transfer files between systems.|
|Network Time Protocol|`NTP`|`123`|It synchronizes computer clocks in a network.|
|Simple Network Management Protocol|`SNMP`|`161`|It monitors and manages network devices remotely.|
|Routing Information Protocol|`RIP`|`520`|It is used to exchange routing information between routers.|
|Internet Key Exchange|`IKE`|`500`|Internet Key Exchange|
|Bootstrap Protocol|`BOOTP`|`68`|It is used to bootstrap hosts in a network.|
|Dynamic Host Configuration Protocol|`DHCP`|`67`|It is used to assign IP addresses to devices in a network dynamically.|
|Telnet|`TELNET`|`23`|It is a text-based remote access communication protocol.|
|MySQL|`MySQL`|`3306`|It is an open-source database management system.|
|Terminal Server|`TS`|`3389`|It is a remote access protocol used for Microsoft Windows Terminal Services by default.|
|NetBIOS Name|`netbios-ns`|`137`|It is used in Windows operating systems to resolve NetBIOS names to IP addresses on a LAN.|
|Microsoft SQL Server|`ms-sql-m`|`1434`|Used for the Microsoft SQL Server Browser service.|
|Universal Plug and Play|`UPnP`|`1900`|It is a protocol for devices to discover each other on the network and communicate.|
|PostgreSQL|`PGSQL`|`5432`|It is an object-relational database management system.|
|Virtual Network Computing|`VNC`|`5900`|It is a graphical desktop sharing system.|
|X Window System|`X11`|`6000-6063`|It is a computer software system and network protocol that provides GUI on Unix-like systems.|
|Syslog|`SYSLOG`|`514`|It is a standard protocol to collect and store log messages on a computer system.|
|Internet Relay Chat|`IRC`|`194`|It is a real-time Internet text messaging (chat) or synchronous communication protocol.|
|OpenPGP|`OpenPGP`|`11371`|It is a protocol for encrypting and signing data and communications.|
|Internet Protocol Security|`IPsec`|`500`|IPsec is also a protocol that provides secure, encrypted communication. It is commonly used in VPNs to create a secure tunnel between two devices.|
|Internet Key Exchange|`IKE`|`11371`|It is a protocol for encrypting and signing data and communications.|
|X Display Manager Control Protocol|`XDMCP`|`177`|XDMCP is a network protocol that allows a user to remotely log in to a computer running the X11.|

## ICMP

Internet Control Message Protocol (ICMP) é um protocolo usado por dispositivos para se comunicar uns com os outros na Internet para vários fins, incluindo relatórios de erros e informações de status. Ele envia solicitações e mensagens entre dispositivos, que podem ser usadas para relatar erros ou fornecer informações de status.

#### ICMP Requests

Uma solicitação é uma mensagem enviada por um dispositivo a outro para solicitar informações ou executar uma ação específica. Um exemplo de requisição no ICMP é a requisição de ping, que testa a conectividade entre dois dispositivos. Quando um dispositivo envia uma solicitação de ping para outro, o segundo dispositivo responde com uma mensagem de resposta de ping.

#### ICMP Messages

Uma mensagem no ICMP pode ser uma solicitação ou uma resposta. Além de solicitações e respostas de ping, o ICMP oferece suporte a outros tipos de mensagens, como mensagens de erro, destino inacessível e mensagens de tempo excedido. Essas mensagens são usadas para comunicar vários tipos de informações e erros entre dispositivos na rede.

Por exemplo, se um dispositivo tentar enviar um pacote para outro dispositivo e o pacote não puder ser entregue, o dispositivo poderá usar o ICMP para enviar uma mensagem de erro de volta ao remetente. O ICMP tem duas versões diferentes:
- ICMPv4: For IPv4 only
- ICMPv6: For IPv6 only

ICMPv4 é a versão original do ICMP, desenvolvida para uso com IPv4. Ainda é amplamente utilizado e é a versão mais comum do ICMP. Por outro lado, o ICMPv6 foi desenvolvido para IPv6. Ele inclui funcionalidade adicional e foi projetado para lidar com algumas das limitações do ICMPv4.

|**Request Type**|**Description**|
|---|---|
|`Echo Request`|This message tests whether a device is reachable on the network. When a device sends an echo request, it expects to receive an echo reply message. For example, the tools `tracert` (Windows) or `traceroute` (Linux) always send ICMP echo requests.|
|`Timestamp Request`|This message determines the time on a remote device.|
|`Address Mask Request`|This message is used to request the subnet mask of a device.|

|**Message Type**|**Description**|
|---|---|
|`Echo reply`|This message is sent in response to an echo request message.|
|`Destination unreachable`|This message is sent when a device cannot deliver a packet to its destination.|
|`Redirect`|A router sends this message to inform a device that it should send its packets to a different router.|
|`time exceeded`|This message is sent when a packet has taken too long to reach its destination.|
|`Parameter problem`|This message is sent when there is a problem with a packet's header.|
|`Source quench`|This message is sent when a device receives packets too quickly and cannot keep up. It is used to slow down the flow of packets.|

Outra parte crucial do ICMP para nós é o campo Time-To-Live (TTL) no cabeçalho do pacote ICMP que limita o tempo de vida do pacote conforme ele viaja pela rede. Evita que os pacotes circulem indefinidamente na rede em caso de loops de roteamento. Cada vez que um pacote passa por um roteador, o roteador diminui o valor TTL em 1. Quando o valor TTL atinge 0, o roteador descarta o pacote e envia uma mensagem ICMP Time Exceeded de volta ao remetente.

Também podemos usar o TTL para determinar o número de hoops que um pacote percorreu e a distância aproximada até o destino. Por exemplo, se um pacote tem um TTL de 10 e leva 5 hoops para chegar ao seu destino, pode-se inferir que o destino está a aproximadamente 5 hoops de distância. Por exemplo, se vemos um ping com o valor TTL de 122, isso pode significar que estamos lidando com um sistema Windows (TTL 128 por padrão) que está a 6 hoops de distância.

No entanto, também é possível adivinhar o sistema operacional com base no valor TTL padrão usado pelo dispositivo. Cada sistema operacional normalmente tem um valor TTL padrão ao enviar pacotes. Esse valor é definido no cabeçalho do pacote e é decrementado em 1 cada vez que o pacote passa por um roteador. Portanto, examinar o valor TTL padrão de um dispositivo permite inferir qual sistema operacional o dispositivo está usando. Por exemplo: os sistemas Windows (2000/XP/2003/Vista/10) geralmente têm um valor TTL padrão de 128, enquanto os sistemas macOS e Linux geralmente têm um valor TTL padrão de 64 e o valor TTL padrão do Solaris de 255. No entanto, é importante observar que o usuário pode alterar esses valores, portanto, eles devem ser independentes de uma maneira definitiva de determinar o sistema operacional de um dispositivo.

## VoIP

Voice over Internet Protocol (VoIP) é um método de transmissão de voz e comunicações multimídia. Por exemplo, permite fazer chamadas telefônicas usando uma conexão de internet banda larga em vez de uma linha telefônica tradicional, como Skype, Whatsapp, Google Hangouts, Slack, Zoom e outros.

As portas VoIP mais comuns são TCP/5060 e TCP/5061, que são usadas para o Session Initiation Protocol (SIP). No entanto, a porta TCP/1720 também pode ser usada por alguns sistemas VoIP para o protocolo H.323, um conjunto de padrões para comunicação multimídia em redes baseadas em pacotes. Ainda assim, o SIP é mais amplamente utilizado do que o H.323 em sistemas VoIP.

No entanto, o SIP é um protocolo de sinalização para iniciar, manter, modificar e encerrar sessões em tempo real envolvendo vídeo, voz, mensagens e outros aplicativos e serviços de comunicação entre dois ou mais terminais na Internet.
Portanto, ele usa solicitações e métodos entre os endpoints. As solicitações e métodos SIP mais comuns são:

|**Method**|**Description**|
|---|---|
|`INVITE`|Initiates a session or invites another endpoint to participate.|
|`ACK`|Confirms the receipt of an INVITE request.|
|`BYE`|Terminate a session.|
|`CANCEL`|Cancels a pending INVITE request.|
|`REGISTER`|Registers a SIP user agent (UA) with a SIP server.|
|`OPTIONS`|Requests information about the capabilities of a SIP server or user agent, such as the types of media it supports.|

## Information Disclosure

No entanto, o SIP nos permite enumerar os usuários existentes para possíveis ataques. No entanto, o SIP nos permite enumerar os usuários existentes para possíveis ataques.

Uma das formas possíveis de enumerar usuários é a solicitação SIP OPTIONS. É um método usado para solicitar informações sobre os recursos de um servidor SIP ou agentes de usuário, como os tipos de mídia que suporta, os codecs que pode decodificar e outros detalhes. A solicitação OPTIONS pode sondar um servidor SIP ou agente do usuário para obter informações ou testar sua conectividade e disponibilidade.

Durante nossa análise, é possível descobrir um arquivo SEPxxxx.cnf, onde xxxx é um identificador exclusivo, é um arquivo de configuração usado pelo Cisco Unified Communications Manager, anteriormente conhecido como Cisco CallManager, para definir as configurações e os parâmetros de um Cisco Unified IP Phone. O arquivo especifica o modelo do telefone, versão do firmware, configurações de rede e outros detalhes.
