Cada host em uma rede tem seu próprio endereço Media Access Control (MAC) de 48 bits (6 octetos), representado em formato hexadecimal. MAC é o endereço físico para nossas interfaces de rede. Existem vários padrões diferentes para o endereço MAC:
- Ethernet (IEEE 802.3)
- Bluetooth (IEEE 802.15)
- WLAN (IEEE 802.11)

Isso ocorre porque o endereço MAC aborda a conexão física (placa de rede, Bluetooth ou adaptador WLAN) de um host. Cada placa de rede tem seu endereço MAC individual, que é configurado uma vez no lado do hardware do fabricante, mas sempre pode ser alterado, pelo menos temporariamente.

Vamos dar uma olhada em um exemplo de tal endereço MAC:

- DE:AD:BE:EF:13:37
- DE-AD-BE-EF-13-37
- DEAD.BEEF.1337

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|1101 1110|1010 1101|1011 1110|1110 1111|0001 0011|0011 0111|
|Hex|DE|AD|BE|EF|13|37|

Quando um pacote IP é entregue, ele deve ser endereçado na camada 2 para o endereço físico do host de destino ou para o roteador/NAT, que é responsável pelo roteamento. Cada pacote tem um endereço de remetente e um endereço de destino.

O endereço MAC consiste em um total de 6 bytes. A primeira metade (3 bytes / 24 bits) é o chamado Organization Unique Identifier (OUI) definido pelo Institute of Electrical and Electronics Engineers (IEEE) para os respectivos fabricantes.

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|`1101 1110`|`1010 1101`|`1011 1110`|1110 1111|0001 0011|0011 0111|
|Hex|`DE`|`AD`|`BE`|EF|13|37|

A última metade do endereço MAC é chamada de Parte do Endereço Individual ou Controlador de Interface de Rede (NIC), que os fabricantes atribuem. O fabricante define essa sequência de bits apenas uma vez e, assim, garante que o endereço completo seja único.

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|1101 1110|1010 1101|1011 1110|`1110 1111`|`0001 0011`|`0011 0111`|
|Hex|DE|AD|BE|`EF`|`13`|`37`|

Se um host com o endereço IP de destino estiver localizado na mesma sub-rede, a entrega será feita diretamente no endereço físico do computador de destino. No entanto, se este host pertencer a uma sub-rede diferente, o quadro Ethernet é endereçado ao endereço MAC do roteador responsável (gateway padrão). Se o endereço de destino do quadro Ethernet corresponder ao seu próprio endereço da camada 2, o roteador encaminhará o quadro para as camadas superiores. O Address Resolution Protocol (ARP) é usado no IPv4 para determinar os endereços MAC associados aos endereços IP.

Assim como nos endereços IPv4, também existem certas áreas reservadas para o endereço MAC. Isso inclui, por exemplo, o alcance local do MAC.

Além disso, os dois últimos bits do primeiro octeto podem desempenhar outro papel essencial. O último bit pode ter dois estados, 0 e 1, como já sabemos. O último bit identifica o endereço MAC como Unicast (0) ou Multicast (1). Com unicast, significa que o pacote enviado chegará a apenas um host específico.

### MAC Unicast

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|1101 111`0`|1010 1101|1011 1110|1110 1111|0001 0011|0011 0111|
|Hex|D`E`|AD|BE|EF|13|37|

Com o multicast, o pacote é enviado apenas uma vez para todos os hosts da rede local, que decidem se aceitam ou não o pacote com base em sua configuração.
O endereço multicast é um endereço único, assim como o endereço broadcast, que possui valores de octetos fixos. Broadcast em uma rede representa uma chamada transmitida, onde os pacotes de dados são transmitidos simultaneamente de um ponto para todos os membros de uma rede. É usado principalmente se o endereço do destinatário do pacote ainda não for conhecido. Um exemplo são os protocolos ARP (para endereços MAC) e DHCP (para endereços IPv4).

### MAC Multicast

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|`0000 0001`|`0000 0000`|`0101 1110`|1110 1111|0001 0011|0011 0111|
|Hex|`01`|`00`|`5E`|EF|13|37|

### MAC Broadcast

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|`1111 1111`|`1111 1111`|`1111 1111`|`1111 1111`|`1111 1111`|`1111 1111`|
|Hex|`FF`|`FF`|`FF`|`FF`|`FF`|`FF`|

O penúltimo bit no primeiro octeto identifica se é um OUI global, definido pelo IEEE, ou um endereço MAC administrado localmente.

### Global OUI

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|1101 11`0`0|1010 1101|1011 1110|1110 1111|0001 0011|0011 0111|
|Hex|D`C`|AD|BE|EF|13|37|

### Locally Administrated

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**5th Octet**|**6th Octet**|
|---|---|---|---|---|---|---|
|Binary|1101 11`1`0|1010 1101|1011 1110|1110 1111|0001 0011|0011 0111|
|Hex|D`E`|AD|BE|EF|13|37|

# Address Resolution Protocol

Os endereços MAC podem ser alterados/manipulados ou falsificados e, como tal, não devem ser usados ​​como único meio de segurança ou identificação. Os administradores de rede devem implementar medidas de segurança adicionais, como segmentação de rede e protocolos de autenticação fortes, para proteção contra possíveis ataques.

Existem vários vetores de ataque que podem ser potencialmente explorados por meio do uso de endereços MAC:
- MAC spoofing: Isso envolve alterar o endereço MAC de um dispositivo para corresponder ao de outro dispositivo, geralmente para obter acesso não autorizado a uma rede.
- MAC flooding: Isso envolve o envio de muitos pacotes com diferentes endereços MAC para um switch de rede, fazendo com que ele atinja sua capacidade da tabela de endereços MAC e efetivamente impedindo-o de funcionar corretamente.
- MAC address filtering: Algumas redes podem ser configuradas apenas para permitir o acesso a dispositivos com endereços MAC específicos que poderíamos explorar ao tentar obter acesso à rede usando um endereço MAC falsificado.

O Address Resolution Protocol (ARP) é um protocolo de rede. É uma parte importante da comunicação de rede usada para resolver um endereço IP da camada de rede (camada 3) para um endereço MAC da camada de link (camada 2). Ele mapeia o endereço IP de um host para seu endereço MAC correspondente para facilitar a comunicação entre dispositivos em uma rede local (LAN). Quando um dispositivo em uma LAN deseja se comunicar com outro dispositivo, ele envia uma mensagem de transmissão contendo o endereço IP de destino e seu próprio endereço MAC. O dispositivo com o endereço IP correspondente responde com seu próprio endereço MAC e os dois dispositivos podem se comunicar diretamente usando seus endereços MAC. Este processo é conhecido como resolução ARP.

O ARP é uma parte importante do processo de comunicação de rede porque permite que os dispositivos enviem e recebam dados usando endereços MAC em vez de endereços IP, o que pode ser mais eficiente. Dois tipos de mensagens de solicitação podem ser usados:

### ARP Request

Quando um dispositivo deseja se comunicar com outro dispositivo em uma LAN, ele envia uma solicitação ARP para resolver o endereço IP do dispositivo de destino em seu endereço MAC. A solicitação é transmitida para todos os dispositivos na LAN e contém o endereço IP do dispositivo de destino. O dispositivo com o endereço IP correspondente responde com seu endereço MAC.

### ARP Reply

Quando um dispositivo recebe uma solicitação ARP, ele envia uma resposta ARP ao dispositivo solicitante com seu endereço MAC. A mensagem de resposta contém os endereços IP e MAC dos dispositivos solicitantes e respondentes.

#### Tshark Capture of ARP Requests

```shell-session
1   0.000000 10.129.12.100 -> 10.129.12.255 ARP 60  Who has 10.129.12.101?  Tell 10.129.12.100
2   0.000015 10.129.12.101 -> 10.129.12.100 ARP 60  10.129.12.101 is at AA:AA:AA:AA:AA:AA

3   0.000030 10.129.12.102 -> 10.129.12.255 ARP 60  Who has 10.129.12.103?  Tell 10.129.12.102
4   0.000045 10.129.12.103 -> 10.129.12.102 ARP 60  10.129.12.103 is at BB:BB:BB:BB:BB:BB
```

A mensagem "who has" na primeira e terceira linhas indica que um dispositivo está solicitando o endereço MAC para o endereço IP especificado, enquanto a segunda e quarta linhas mostram a resposta ARP com o endereço MAC do dispositivo de destino.

No entanto, também é vulnerável a ataques, como o ARP Spoofing, que pode ser usado para interceptar ou manipular o tráfego na rede. No entanto, para se proteger contra esses ataques, é importante implementar medidas de segurança, como firewalls e sistemas de detecção de intrusão.

ARP spoofing, também conhecida como ARP cache poisoning ou ARP poison routing, é um ataque que pode ser feito usando ferramentas como Ettercap ou Cain & Abel em que enviamos mensagens ARP falsificadas por uma LAN. O objetivo é associar nosso endereço MAC ao endereço IP de um dispositivo legítimo na rede da empresa, permitindo-nos efetivamente interceptar o tráfego destinado ao dispositivo legítimo. Por exemplo, isso pode se parecer com o seguinte:

```shell-session
1   0.000000 10.129.12.100 -> 10.129.12.101 ARP 60  10.129.12.100 is at AA:AA:AA:AA:AA:AA
2   0.000015 10.129.12.100 -> 10.129.12.255 ARP 60  Who has 10.129.12.101?  Tell 10.129.12.100
3   0.000030 10.129.12.101 -> 10.129.12.100 ARP 60  10.129.12.101 is at BB:BB:BB:BB:BB:BB
4   0.000045 10.129.12.100 -> 10.129.12.101 ARP 60  10.129.12.100 is at AA:AA:AA:AA:AA:AA
```

A primeira e a quarta linhas nos mostram (10.129.12.100) enviando mensagens ARP falsificadas ao alvo, associando seu endereço MAC ao seu endereço IP (10.129.12.101). A segunda e a terceira linhas mostram o alvo enviando uma solicitação ARP e respondendo ao nosso endereço MAC. Isso indica que envenenamos o cache ARP do alvo e que todo o tráfego destinado ao alvo agora será enviado para nosso endereço MAC.

Podemos usar o envenenamento ARP para realizar várias atividades, como roubar informações confidenciais, redirecionar tráfego ou iniciar ataques MITM. No entanto, para se proteger contra o ARP spoofing, é importante usar protocolos de rede seguros, como IPSec ou SSL, e implementar medidas de segurança, como firewalls e sistemas de detecção de intrusão.