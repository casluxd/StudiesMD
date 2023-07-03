As redes sem fio são redes de computadores que usam conexões de dados sem fio entre os nós da rede. Essas redes permitem que dispositivos como laptops, smartphones e tablets se comuniquem entre si e com a Internet sem a necessidade de conexões físicas, como cabos.

As redes sem fio usam tecnologia de radiofrequência (RF) para transmitir dados entre dispositivos. Cada dispositivo em uma rede sem fio possui um adaptador sem fio que converte dados em sinais de RF e os envia pelo ar. Outros dispositivos na rede recebem esses sinais com seus próprios adaptadores sem fio e os dados são convertidos de volta em um formato utilizável. Esses podem operar em vários intervalos, dependendo da tecnologia utilizada. Por exemplo, uma rede local (LAN) que cobre uma pequena área, como uma casa ou um pequeno escritório, pode usar uma tecnologia sem fio chamada WiFi, que tem um alcance de algumas centenas de pés. Por outro lado, uma rede de longa distância sem fio (WWAN) pode usar tecnologia de telecomunicação móvel, como dados celulares (3G, 4G LTE, 5G), que podem cobrir uma área muito maior, como uma cidade ou região inteira.

Portanto, para se conectar a uma rede sem fio, um dispositivo deve estar dentro do alcance da rede e configurado com as configurações de rede corretas, como o nome e a senha da rede. Uma vez conectados, os dispositivos podem se comunicar entre si e com a Internet, permitindo que os usuários acessem recursos online e troquem dados.

A comunicação entre dispositivos ocorre por RF nas bandas de 2,4 GHz ou 5 GHz em uma rede WiFi. Quando um dispositivo, como um laptop, deseja enviar dados pela rede, ele primeiro se comunica com o ponto de acesso sem fio (WAP) para solicitar permissão para transmitir. O WAP é um dispositivo central, como um roteador, que conecta a rede sem fio a uma rede com fio e controla o acesso à rede. Depois que o WAP concede permissão, o dispositivo transmissor envia os dados como sinais de RF, que são recebidos pelos adaptadores sem fio de outros dispositivos na rede. Os dados são então convertidos de volta em um formato utilizável e repassados ​​para o aplicativo ou sistema apropriado. A intensidade do sinal de RF e a distância que ele pode percorrer são influenciadas por fatores como a potência do transmissor, a presença de obstáculos e a densidade do ruído de RF no ambiente. Portanto, para garantir uma comunicação confiável, as redes WiFi usam técnicas como transmissão de espectro alargado e correção de erros para superar esses desafios.

## WiFi Connection

O dispositivo também deve ser configurado com as configurações de rede corretas, como o nome da rede/Service Set Identifier (SSID) e a senha. Portanto, para se conectar ao roteador, o laptop usa um protocolo de rede sem fio chamado IEEE 802.11. Este protocolo define os detalhes técnicos de como os dispositivos sem fio se comunicam entre si e com os WAPs. Quando um dispositivo deseja ingressar em uma rede WiFi, ele envia uma solicitação ao WAP para iniciar o processo de conexão. Essa solicitação é conhecida como quadro de solicitação de conexão ou solicitação de associação e é enviada usando o protocolo de rede sem fio IEEE 802.11. O quadro de solicitação de conexão contém vários campos de informações, incluindo os seguintes, mas não limitados a:

|   |   |
|---|---|
|`MAC address`|A unique identifier for the device's wireless adapter.|
|`SSID`|The network name, also known as the `Service Set Identifier` of the WiFi network.|
|`Supported data rates`|A list of the data rates the device can communicate.|
|`Supported channels`|A list of the `channels` (frequencies) on which the device can communicate.|
|`Supported security protocols`|A list of the security protocols that the device is capable of using, such as `WPA2`/`WPA3`.|

O dispositivo usa essas informações para configurar seu adaptador sem fio e conectar-se ao WAP. Uma vez estabelecida a conexão, o dispositivo pode se comunicar com o WAP e outros dispositivos de rede. Ele também pode acessar a Internet e outros recursos on-line por meio do WAP, que atua como um gateway para a rede com fio. No entanto, o SSID pode ser ocultado desativando a transmissão. Isso significa que os dispositivos que procuram esse WAP específico não conseguirão identificar seu SSID. No entanto, o SSID ainda pode ser encontrado no pacote de autenticação.

Além do protocolo IEEE 802.11, outros protocolos e tecnologias de rede também podem ser usados, como TCP/IP, DHCP e WPA2, em uma rede WiFi para executar tarefas como atribuir endereços IP a dispositivos, rotear tráfego entre dispositivos e fornecer segurança.

## WEP Challenge-Response Handshake

O handshake de desafio-resposta é um processo para estabelecer uma conexão segura entre um WAP e um dispositivo cliente em uma rede sem fio que usa o protocolo de segurança WEP. Isso envolve a troca de pacotes entre o WAP e o dispositivo cliente para autenticar o dispositivo e estabelecer uma conexão segura.

|**Step**|**Who**|**Description**|
|---|---|---|
|1|`Client`|Sends an association request packet to the WAP, requesting access.|
|2|`WAP`|Responds with an association response packet to the client, which includes a challenge string.|
|3|`Client`|Calculates a response to the challenge string and a shared secret key and sends it back to the WAP.|
|4|`WAP`|Calculates the expected response to the challenge with the same shared secret key and sends an authentication response packet to the client.|

No entanto, alguns pacotes podem se perder, então a chamada soma de verificação CRC foi integrada. Cyclic Redundancy Check (CRC) é um mecanismo de detecção de erros usado no protocolo WEP para proteção contra corrupção de dados em comunicações sem fio. Um valor CRC é calculado para cada pacote transmitido pela rede sem fio com base nos dados do pacote. É usado para verificar a integridade dos dados. É usado para verificar a integridade dos dados. Quando o dispositivo de destino recebe o pacote, o valor CRC é recalculado e comparado ao valor original. Se os valores coincidirem, os dados foram transmitidos com sucesso e sem erros. No entanto, se os valores não corresponderem, os dados foram corrompidos e precisam ser retransmitidos.

O design do mecanismo CRC tem uma falha que nos permite descriptografar um único pacote sem conhecer a chave de criptografia. Isso ocorre porque o valor CRC é calculado usando os dados de texto simples no pacote, em vez dos dados criptografados. No WEP, o valor CRC é incluído no cabeçalho do pacote junto com os dados criptografados. Quando o dispositivo de destino recebe o pacote, o valor CRC é recalculado e comparado com o original para garantir que os dados foram transmitidos com sucesso e sem erros. No entanto, podemos usar o CRC para determinar os dados de texto simples no pacote, mesmo que os dados estejam criptografados.

## Security Features

As redes WiFi possuem vários recursos de segurança para proteger contra acesso não autorizado e garantir a privacidade e integridade dos dados transmitidos pela rede. Alguns dos principais recursos de segurança incluem, mas não estão limitados a:
- Encryption
- Access Control
- Firewall

#### Encryption

Podemos usar vários algoritmos de criptografia para proteger a confidencialidade dos dados transmitidos por redes sem fio. Os algoritmos de criptografia mais comuns em redes WiFi são Wired Equivalent Privacy (WEP), WiFi Protected Access 2 (WPA2) e WiFi Protected Access 3 (WPA3).

### Access Control

As redes WiFi são configuradas por padrão para permitir que dispositivos autorizados entrem na rede usando métodos de autenticação específicos. No entanto, esses métodos podem ser alterados exigindo uma senha ou um identificador exclusivo (como um endereço MAC) para identificar os dispositivos autorizados.

### Firewall

Um firewall é um sistema de segurança que controla o tráfego de rede de entrada e saída com base em regras de segurança predeterminadas. Por exemplo, os roteadores WiFi geralmente possuem firewalls integrados que podem bloquear o tráfego de entrada da Internet e proteger contra vários tipos de ameaças cibernéticas.

## Encryption Protocols

Wired Equivalent Privacy (WEP) e WiFi Protected Access (WPA) são protocolos de criptografia que protegem os dados transmitidos por uma rede WiFi. WPA pode usar diferentes algoritmos de criptografia, incluindo Advanced Encryption Standard (AES).

### WEP

O WEP usa uma chave de 40 bits ou 104 bits para criptografar dados, enquanto o WPA usando AES usa uma chave de 128 bits. Chaves mais longas fornecem criptografia mais robusta e são mais resistentes a ataques. No entanto, ele é vulnerável a vários ataques que podem permitir que um invasor descriptografe os dados transmitidos pela rede. Além disso, o WEP não é compatível com dispositivos e sistemas operacionais mais recentes e geralmente não é mais considerado seguro. Por fim, o WEP usa o algoritmo de criptografia de cifra RC4, o que o torna vulnerável a ataques.

No entanto, o WEP usa uma chave compartilhada para autenticação, o que significa que a mesma chave é usada para criptografia e autenticação. Existem duas versões do protocolo WEP:
- WEP-40/WEP-64
- WEP-104

O WEP-40, também conhecido como WEP-64, usa uma chave (secreta) de 40 bits, enquanto o WEP-104 usa uma chave de 104 bits. A chave é dividida em um Vetor de Inicialização (IV) e uma chave secreta.

O IV é um pequeno valor incluído no cabeçalho do pacote junto com os dados criptografados e é usado para criar a chave para WEP-40 e WEP-104 e é incluído para garantir que cada chave seja exclusiva. A chave secreta é uma série de bits aleatórios usados ​​para criptografar os dados. No entanto, o WEP-104 possui uma chave secreta de 80 bits. Vejamos a tabela a seguir para ver as diferenças claramente:

|**Protocol**|**IV**|**Secret Key**|
|---|---|---|
|`WEP-40`/`WEP-64`|24-bit|40-bit|
|`WEP-104`|24-bit|80-bit|

No entanto, como o IV em WEP é relativamente pequeno, podemos forçá-lo, tentar todas as combinações possíveis de caracteres para ele e determinar o valor correto. Depois, podemos usá-lo para descriptografar os dados do pacote. Isso nos permite acessar os dados transmitidos pela rede sem fio e potencialmente comprometer a segurança da rede.

### WPA

O WPA fornece o mais alto nível de segurança e não é suscetível aos mesmos tipos de ataques que o WEP. Além disso, o WPA usa métodos de autenticação mais seguros, como uma chave pré-compartilhada (PSK) ou um servidor de autenticação 802.1X, que fornecem proteção mais forte contra acesso não autorizado.

Embora dispositivos mais antigos possam não suportar WPA, ele é compatível com a maioria dos dispositivos e sistemas operacionais. Todas as redes sem fio, especialmente em infraestrutura crítica como escritórios, geralmente devem implementar pelo menos criptografia WPA2 ou até WPA3.

## Authentication Protocols

Lightweight Extensible Authentication Protocol (LEAP) e Protected Extensible Authentication Protocol (PEAP) são protocolos de autenticação usados ​​para proteger redes sem fio para fornecer um método seguro para autenticação de dispositivos em uma rede sem fio e geralmente são usados ​​em conjunto com WEP ou WPA para fornecer uma camada adicional de segurança.

LEAP e PEAP são ambos baseados no Extensible Authentication Protocol (EAP), uma estrutura para autenticação usada em vários contextos de rede. No entanto, uma diferença fundamental entre LEAP e PEAP é como eles protegem o processo de autenticação.
- O LEAP usa uma chave compartilhada para autenticação, o que significa que a mesma chave é usada para criptografia e autenticação.

Isso pode tornar relativamente fácil para nós obter acesso à rede se a chave for comprometida.

No entanto, o PEAP usa um método de autenticação mais seguro chamado TLS (Transport Layer Security) encapsulado. Este método estabelece uma conexão segura entre o dispositivo e o WAP usando um certificado digital, e um túnel criptografado protege o processo de autenticação. Isso fornece proteção mais robusta contra acesso não autorizado e é mais resistente a ataques.

## TACACS+

Em uma rede sem fio, quando um ponto de acesso sem fio (WAP) envia uma solicitação de autenticação para um servidor Terminal Access Controller Access-Control System Plus (TACACS+), é provável que todo o pacote de solicitação seja criptografado para proteger a confidencialidade e a integridade da solicitação.

TACACS+ é um protocolo usado para autenticar e autorizar usuários acessando dispositivos de rede, como roteadores e switches. Quando um WAP envia um pedido de autenticação a um servidor TACACS+, a solicitação geralmente inclui as credenciais do usuário e outras informações sobre a sessão.

Criptografar a solicitação de autenticação ajuda a garantir que essas informações confidenciais não fiquem visíveis para pessoas não autorizadas que possam interceptar a solicitação. Ao mesmo tempo, está sendo transmitido pela rede. Também ajuda a evitar adulterar a solicitação ou substituí-la por uma solicitação maliciosa própria.

Vários métodos de criptografia podem ser usados ​​para criptografar a solicitação de autenticação, como SSL/TLS ou IPSec. O método de criptografia específico usado pode depender da configuração do servidor TACACS+ e das capacidades do WAP.

## Disassociation Attack

Um ataque de dissociação é um tipo de ataque de rede sem fio que visa interromper a comunicação entre um WAP e seus clientes, enviando quadros de desassociação para um ou mais clientes.

O WAP usa quadros de desassociação para desconectar um cliente da rede. Quando um WAP envia um quadro de desassociação para um cliente, o cliente se desconecta da rede e precisa se reconectar para continuar usando a rede.

Podemos lançar o ataque de dentro ou de fora da rede, dependendo da nossa localização e das medidas de segurança da rede. O objetivo deste ataque é interromper a comunicação entre o WAP e seus clientes, fazendo com que os clientes se desconectem e possivelmente causando inconveniência ou interrupção aos usuários. Também podemos usá-lo como precursor de outros ataques, como um ataque MITM, forçando os clientes a se reconectarem à rede e potencialmente expondo-os a novos ataques.

## Wireless Hardening

Existem muitas maneiras diferentes de proteger redes sem fio. No entanto, alguns exemplos devem ser considerados para aumentar drasticamente a segurança das redes sem fio. Estes são os seguintes, mas não limitados a:

- Desabilitando broadcasting
- Acesso Protegido Wi-Fi
- filtragem MAC
- Implantando EAP-TLS

#### Desabilitando broadcasting

Desativar a transmissão do SSID é uma medida de segurança que pode ajudar a proteger um WAP, tornando mais difícil descobrir e conectar-se à rede. Quando o SSID é transmitido, ele é incluído nos beacon frames regularmente transmitidos pelo WAP para anunciar a disponibilidade da rede. Ao desativar a transmissão do SSID, o WAP não transmitirá quadros de beacon e a rede não ficará visível para dispositivos que ainda não estejam conectados à rede.

### WPA

Mais uma vez, o WPA fornece criptografia e autenticação fortes para comunicações sem fio, ajudando a proteger contra acesso não autorizado à rede e interceptação de dados confidenciais. O WPA inclui duas versões principais:
1. WPA-Personal
2. WPA-Enterprise

WPA-Personal, projetado para redes domésticas e de pequenas empresas, e WPA-Enterprise, projetado para organizações maiores e usa um servidor de autenticação centralizado (por exemplo, RADIUS ou TACACS+) para verificar a identidade dos clientes.

### Filtragem MAC

A filtragem MAC é uma medida de segurança que permite que um WAP aceite ou rejeite conexões de dispositivos específicos com base em seus endereços MAC. Ao configurar o WAP para aceitar conexões apenas de dispositivos com endereços MAC aprovados, é possível impedir que dispositivos não autorizados se conectem à rede.

### Implantando EAP-TLS

EAP-TLS é um protocolo de segurança usado para autenticar e criptografar comunicações sem fio. Ele usa certificados digitais e PKI para verificar a identidade dos clientes e estabelecer conexões seguras. A implantação de EAP-TLS pode ajudar a proteger um WAP, fornecendo autenticação e criptografia fortes para comunicações sem fio, que podem proteger contra acesso não autorizado à rede e interceptação de dados confidenciais.

