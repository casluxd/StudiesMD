
No entanto, para se proteger contra o ARP spoofing, é importante usar protocolos de rede seguros, como IPSec ou SSL, e implementar medidas de segurança, como firewalls e sistemas de detecção de intrusão. A Internet Assigned Numbers Authority (IANA) é responsável por atribuir endereços IPv4 e IPv6 e suas porções de rede associadas. A longo prazo, espera-se que o IPv6 substitua completamente o IPv4, que ainda é predominantemente usado na Internet. Em princípio, porém, IPv4 e IPv6 podem ser disponibilizados simultaneamente (Dual Stack).

O IPv6 segue consistentemente o princípio de ponta a ponta e fornece endereços IP publicamente acessíveis para qualquer dispositivo final sem a necessidade de NAT. Consequentemente, uma interface pode ter vários endereços IPv6 e existem endereços IPv6 especiais aos quais várias interfaces são atribuídas.

O IPv6 é um protocolo com muitas novidades, que também apresenta muitas outras vantagens sobre o IPv4:
- Maior espaço de endereço
- Autoconfiguração de endereço (SLAAC)
- Vários endereços IPv6 por interface
- Roteamento mais rápido
- Criptografia de ponta a ponta (IPsec)
- Pacotes de dados de até 4 GByte

|**Features**|**IPv4**|**IPv6**|
|---|---|---|
|Bit length|32-bit|128 bit|
|OSI layer|Network Layer|Network Layer|
|Adressing range|~ 4.3 billion|~ 340 undecillion|
|Representation|Binary|Hexadecimal|
|Prefix notation|10.10.10.0/24|fe80::dd80:b1a9:6687:2d3b/64|
|Dynamic addressing|DHCP|SLAAC / DHCPv6|
|IPsec|Optional|Mandatory|

Existem quatro tipos diferentes de endereços IPv6:

|**Type**|**Description**|
|---|---|
|`Unicast`|Addresses for a single interface.|
|`Anycast`|Addresses for multiple interfaces, where only one of them receives the packet.|
|`Multicast`|Addresses for multiple interfaces, where all receive the same packet.|
|`Broadcast`|Do not exist and is realized with multicast addresses.|

## Sistema Hexadecimal

O sistema hexadecimal (hex) é usado para tornar a representação binária mais legível e compreensível. Podemos mostrar apenas 10 (0-9) estados com o sistema decimal e 2 (0/1) com o sistema binário usando um único caractere. Em contraste com o sistema binário e decimal, podemos usar o sistema hexadecimal para mostrar 16 (0-F) estados com um único caractere.

|**Decimal**|**Hex**|**Binary**|
|---|---|---|
|1|1|0001|
|2|2|0010|
|3|3|0011|
|4|4|0100|
|5|5|0101|
|6|6|0110|
|7|7|0111|
|8|8|1000|
|9|9|1001|
|10|A|1010|
|11|B|1011|
|12|C|1100|
|13|D|1101|
|14|E|1110|
|15|F|1111|

Vejamos um exemplo com um IPv4, como o endereço IPv4 (192.168.12.160) ficaria em representação hexadecimal.

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|
|---|---|---|---|---|
|Binary|1100 0000|1010 1000|0000 1100|1010 0000|
|`Hex`|`C0`|`A8`|`0C`|`A0`|
|Decimal|192|168|12|160|

No total, o endereço IPv6 consiste em 16 bytes. Devido ao seu comprimento, um endereço IPv6 é representado em notação hexadecimal. Portanto, os 128 bits são divididos em 8 blocos multiplicados por 16 bits (ou 4 números hexadecimais). Todos os quatro números hexadecimais são agrupados e separados por dois pontos (:) em vez de um simples ponto (.) como no IPv4. Para simplificar a notação, deixamos de fora pelo menos 4 zeros iniciais nos blocos e podemos substituí-los por dois dois-pontos (::).

Um endereço IPv6 pode ter esta aparência:
- IPv6 completo: fe80:0000:0000:0000:dd80:b1a9:6687:2d3b/64
- IPv6 curto: fe80::dd80:b1a9:6687:2d3b/64

Um endereço IPv6 consiste em duas partes:
- Prefixo de rede (parte de rede)
- Identificador de interface também chamado de sufixo (parte do host)

O prefixo de rede identifica a rede, sub-rede ou intervalo de endereços. O identificador de interface é formado a partir do endereço MAC de 48 bits (que discutiremos mais adiante) da interface e é convertido em um endereço de 64 bits no processo. O comprimento do prefixo padrão é /64. No entanto, outros prefixos típicos são /32, /48 e /56. Se quisermos usar nossas redes, obtemos um prefixo mais curto (por exemplo, /56) do que /64 de nosso provedor.

No RFC 5952, a notação de endereço IPv6 acima mencionada foi definida:
- Todos os caracteres alfabéticos são sempre escritos em letras minúsculas.
- Todos os zeros à esquerda de um bloco são sempre omitidos.
- Um ou mais blocos consecutivos de 4 zeros (hex) são abreviados por dois dois pontos (::).
- O encurtamento para dois dois pontos (::) só pode ser feito uma vez a partir da esquerda.
