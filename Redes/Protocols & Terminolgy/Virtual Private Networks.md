
Uma rede virtual privada (VPN) é uma tecnologia que permite uma conexão segura e criptografada entre uma rede privada e um dispositivo remoto. Isso permite que a máquina remota acesse a rede privada diretamente, fornecendo acesso seguro e confidencial aos recursos e serviços da rede. Por exemplo, um administrador de outro local precisa gerenciar os servidores internos para que os funcionários possam continuar usando os serviços internos. Muitas empresas limitam o acesso dos servidores, portanto, os servidores só podem acessar esses servidores da rede local. É aqui que entra a VPN, onde o administrador se conecta ao servidor VPN via internet, se autentica e, assim, cria um túnel criptografado para que outras pessoas não possam ler a transferência de dados. Além disso, o computador do administrador também recebe um endereço IP local (interno) por meio do qual ele pode acessar e gerenciar os servidores internos. Os administradores geralmente usam VPNs para fornecer acesso remoto seguro e econômico à rede de uma empresa. A VPN normalmente usa as portas TCP/1723 para conexões VPN PPTP do Point-to-Point Tunneling Protocol e UDP/500 para conexões VPN IKEv1 e IKEv2.

Isso permite que os funcionários acessem a rede e seus recursos, como e-mail e servidores de arquivos, de locais remotos, como suas casas ou durante viagens. Existem vários motivos pelos quais os administradores usam VPNs. As VPNs criptografam a conexão entre o dispositivo remoto e a rede privada, tornando muito mais difícil para os invasores interceptar e roubar informações confidenciais. Com isso, toda a comunicação fica mais segura.

Outro motivo é que as VPNs permitem que os funcionários acessem a rede privada e seus recursos remotamente de qualquer lugar, desde que tenham conexão com a internet. Isso é particularmente útil para funcionários que precisam trabalhar remotamente, como aqueles que viajam ou trabalham em casa. Além disso, as VPNs podem ser mais econômicas do que outras soluções de acesso remoto, como linhas alugadas ou conexões dedicadas, porque usam a Internet pública para conectar usuários remotos à rede privada.

Além disso, podemos usar VPNs para conectar vários locais remotos, como filiais, em uma única rede privada, facilitando o gerenciamento e o acesso aos recursos da rede. No entanto, vários componentes e requisitos são necessários para que uma VPN funcione:

|**Requirement**|**Description**|
|---|---|
|`VPN Client`|This is installed on the remote device and is used to establish and maintain a VPN connection with the VPN server. For example, this could be an OpenVPN client.|
|`VPN Server`|This is a computer or network device responsible for accepting VPN connections from VPN clients and routing traffic between the VPN clients and the private network.|
|`Encryption`|VPN connections are encrypted using a variety of encryption algorithms and protocols, such as AES and IPsec, to secure the connection and protect the transmitted data.|
|`Authentication`|The VPN server and client must authenticate each other using a shared secret, certificate, or another authentication method to establish a secure connection.|

O cliente e o servidor VPN usam essas portas para estabelecer e manter a conexão VPN. Na camada TCP/IP, uma conexão VPN normalmente usa o protocolo Encapsulating Security Payload (ESP) para criptografar e autenticar o tráfego VPN. Isso permite que o cliente e o servidor VPN troquem dados pela Internet pública com segurança.

# IPsec

Internet Protocol Security (IPsec) é um protocolo de segurança de rede que fornece criptografia e autenticação para comunicações na Internet. É um protocolo de segurança poderoso e amplamente utilizado que fornece criptografia e autenticação para comunicações na Internet e funciona criptografando a carga útil de dados de cada pacote IP e adicionando um authentication header (AH) que é usado para verificar a integridade e autenticidade do pacote. O IPsec usa uma combinação de dois protocolos para fornecer criptografia e autenticação:

1. Authentication Header (AH): Este protocolo fornece integridade e autenticidade para pacotes IP, mas não fornece criptografia. Ele adiciona um cabeçalho de autenticação a cada pacote IP, que contém uma soma de verificação criptográfica que pode ser usada para verificar se o pacote não foi adulterado.
2. Encapsulating Security Payload (ESP): Este protocolo fornece criptografia e autenticação opcional para pacotes IP. Ele criptografa a carga de dados de cada pacote IP e, opcionalmente, adiciona um cabeçalho de autenticação, semelhante ao AH.

O IPsec pode ser usado em dois modos.

|**Mode**|**Description**|
|---|---|
|`Transport Mode`|In this mode, IPsec encrypts and authenticates the data payload of each IP packet but does not encrypt the IP header. This is typically used to secure end-to-end communication between two hosts.|
|`Tunnel Mode`|With this mode, IPsec encrypts and authenticates the entire IP packet, including the IP header. This is typically used to create a VPN tunnel between two networks.|

Por exemplo, um administrador pode colocar um firewall no meio. Para facilitar o tráfego VPN IPsec de um cliente VPN fora de um firewall para um servidor VPN dentro, o firewall precisaria permitir os seguintes protocolos:

|**Protocol**|**Port**|**Description**|
|---|---|---|
|`Internet Protocol` (`IP`)|`UDP/50-51`|This is the primary protocol that provides the foundation for all internet communication. It is used to route packets of data between the VPN client and the VPN server.|
|`Internet Key Exchange` (`IKE`)|`UDP/500`|IKE is a protocol that is used to establish and maintain secure communication between the VPN client and the VPN server. It is based on the Diffie-Hellman key exchange algorithm, and it is used to negotiate and establish shared secret keys that can be used to encrypt and decrypt the VPN traffic.|
|`Encapsulating Security Payload` (`ESP`)|`UDP/4500`|ESP is also a protocol that provides encryption and authentication for IP datagrams. It is used to encrypt the VPN traffic between the VPN client and the VPN server, using the keys that were negotiated with IKE.|

Esses protocolos são necessários para facilitar o tráfego VPN IPsec porque fornecem a segurança e a criptografia necessárias para a comunicação segura na Internet pública. Sem esses protocolos, o tráfego VPN seria vulnerável a interceptação e adulteração.

# PPTP

Point-to-Point Tunneling Protocol (PPTP) também é um protocolo de rede que permite a criação de VPNs e funciona estabelecendo um túnel seguro entre o cliente VPN e o servidor e, em seguida, encapsulando os dados que estão sendo transmitidos dentro desse túnel.

É uma extensão do PPTP e é implementado em muitos sistemas operacionais. Devido a vulnerabilidades conhecidas, o PPTP não é mais considerado seguro atualmente. O PPTP pode ser usado para encapsular protocolos como IP, IPX ou NetBEUI via IP. Devido a vulnerabilidades conhecidas, o PPTP não é mais considerado seguro e foi amplamente substituído por outros protocolos VPN, como L2TP/IPsec, IPsec/IKEv2 ou OpenVPN. Desde 2012, no entanto, o PPTP não é mais considerado seguro porque o método de autenticação MSCHAPv2 usa o DES empoeirado e pode, portanto, ser facilmente quebrado com hardware especial.