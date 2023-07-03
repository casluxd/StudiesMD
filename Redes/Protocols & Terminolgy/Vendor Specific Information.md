O Cisco IOS é o sistema operacional dos dispositivos de rede da Cisco, como roteadores e switches. Ele fornece recursos e serviços necessários para gerenciar e operar dispositivos de rede. Este sistema operacional vem em diferentes versões e lançamentos que variam em recursos, suporte e desempenho. Ele oferece vários recursos necessários para a operação de redes modernas, como, mas não se limitando a:

- Suporte para IPv6
- Qualidade de Serviço (QoS)
- Recursos de segurança, como criptografia e autenticação
- Recursos de virtualização, como Virtual Private LAN Service (VPLS)
- Roteamento e encaminhamento virtual (VRF)

O Cisco IOS pode ser gerenciado de várias maneiras, dependendo do dispositivo de rede e do hardware usados. O método mais comumente usado é a interface de linha de comando (CLI), que também pode ser gerenciada na interface gráfica do usuário (GUI). Além disso, suporta vários protocolos de rede e serviços necessários para operações de rede. Esses incluem:

|**Protocol Type**|**Description**|
|---|---|
|`Routing protocols`|Such as [OSPF](https://en.wikipedia.org/wiki/Open_Shortest_Path_First) and [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) are used to route data packets on a network.|
|`Switching protocols`|Such as [VLAN Trunking Protocol](https://en.wikipedia.org/wiki/VLAN_Trunking_Protocol) (`VTP`) and [Spanning Tree Protocol](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol) (`STP`) is used to configure and manage switches on a network.|
|`Network services`|Such as [Dynamic Host Configuration Protocol](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) (`DHCP`) are used to automatically provide clients on the network with IP addresses and other network configurations.|
|`Security features`|Such as [Access Control Lists](https://en.wikipedia.org/wiki/Access-control_list) (`ACLs`), which are used to control access to network resources and prevent security threats.|

No Cisco IOS, diferentes tipos de senha são usados ​​para diversas finalidades, por exemplo:

|**Password Type**|**Description**|
|---|---|
|`User`|The [user](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/s1/sec-s1-cr-book/sec-cr-t2.html#wp2992613898) password is used for logging in to Cisco IOS. It is used to restrict access to the network device and its features.|
|`Enable Password`|The [enable password](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/d1/sec-d1-cr-book/sec-cr-e1.html#wp3884449514) is used to enter "enable" mode. The "enable" mode is the mode where you have access to advanced functions and settings.|
|`Secret`|The [secret](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/s1/sec-s1-cr-book/sec-cr-s1.html#wp2622423174) is a password to secure access to certain functions and services. It is often used to restrict access to remote management tools and services.|
|`Enable Secret`|The [enable secret](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/d1/sec-d1-cr-book/sec-cr-e1.html#wp3438133060) is an extra-secure password used to secure access to "enable" mode, and they are stored encrypted to provide additional protection.|

É altamente recomendável consultar os recursos externos fornecidos para entender a mecânica de criptografia do Cisco IOS e como eles são usados.

Os dispositivos Cisco IOS podem ser configurados para SSH ou Telnet. Assim, pode ser acessado remotamente. Podemos determinar pela resposta que recebemos que é realmente um Cisco IOS, pois ele responde com a mensagem de verificação de acesso do usuário.

```shell-session
casluxd@htb[/htb]$ telnet 10.129.10.2

Trying 10.129.10.2...
Connected to 10.129.10.2.
Escape character is '^]'.


User Access Verification

Password:
```

# VLAN

Uma rede local virtual (VLAN) é uma conexão LAN virtual que agrupa dispositivos em uma rede. As VLANs são comumente usadas para segmentar o tráfego de rede e aprimorar os recursos de segurança. Podemos configurar e gerenciar VLANs no Cisco IOS e adicionar tags de VLAN (IDs de VLAN) a pacotes de dados para classificá-los em VLANs. 

A tag VLAN é um campo no quadro Ethernet usado para indicar a qual VLAN um pacote de dados pertence. O Cisco IOS distingue entre VLAN-tagged e non-VLAN-tagged.

### VLAN-Tagged

O tráfego de rede marcado por VLAN contém tags de VLAN e é usado para rotear e segmentar pacotes de dados entre switches.

### Non-VLAN-Tagged

O tráfego de rede sem marcação de VLAN, por outro lado, é o tráfego de rede que não contém uma marcação de VLAN e é usado para rotear e segmentar pacotes de dados dentro de um switch. Todo o tráfego de rede não marcado por VLAN é colocado na VLAN1.


# Cisco Discovery Protocol

O Cisco Discovery Protocol (CDP) é um protocolo de rede de camada 2 da Cisco usado por dispositivos Cisco, como roteadores, switches e pontes, para coletar informações sobre outros dispositivos Cisco conectados diretamente. Essas informações podem ser usadas para descobrir e rastrear a topologia da rede e ajudar a gerenciar e solucionar problemas da rede. Esse protocolo geralmente é habilitado em dispositivos Cisco, mas pode ser desabilitado se não for necessário ou se for necessário desabilitar por motivos de segurança.

## CDP Network Traffic

```shell-session
22:14:11.563654 CDPv2, ttl: 180s, checksum: 0xebc1 (incorrect -> 0x8b71), length: 180
        Device-ID (0x01), length: 14 bytes: 'router.inlanefreight.loc'
        Addresses (0x02), length: 8 bytes:
                IPv4 (0x01), length: 4: 10.129.100.1
        Port-ID (0x03), length: 9 bytes: 'Ethernet0/0'
        Capability (0x04), length: 4: (0x00000010): Router
        Version String (0x05), length: 27 bytes: 'Cisco IOS Software, C880 Software'
        Platform (0x06), length: 26 bytes: 'Cisco 881 (MPC8300) processor'
```

A mensagem mostrada contém informações sobre o próprio dispositivo, como nome do dispositivo, endereço IP, nome da porta e funcionalidade do roteador, bem como informações sobre o sistema operacional e a plataforma de hardware do dispositivo. Além disso, podemos ver na primeira linha do CDPv2 que estamos lidando com o Cisco Discovery Protocol.

Para comparação, podemos olhar para outro protocolo chamado Spanning Tree Protocol (STP). O STP é um protocolo de rede que garante a ausência de loops em uma rede com várias conexões entre switches. Não há loops e evita que os pacotes de dados circulem em loop e congestionem a rede.

```shell-session
22:14:11.563654 STP 802.1w, Rapid STP, Flags [Learn, Forward], bridge-id 8001.00:11:22:33:44:55.8000, length 43
        root-id 8001.AA:AA:AA:AA:AA:AA, cost 0, port-id 8001, message-age 0.00s, max-age 20.00s, hello-time 2.00s, forward-delay 15.00s
```

Neste exemplo, vemos que uma mensagem STP foi enviada contendo informações sobre o switch raiz, o endereço MAC do switch raiz, o ID da porta pela qual a mensagem foi enviada, e outros parâmetros de configuração, como tempo máximo de envelhecimento, tempo de saudação e atraso de encaminhamento.