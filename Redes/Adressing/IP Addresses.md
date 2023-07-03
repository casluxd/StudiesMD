
Cada host localizado na rede pode ser identificado pelo chamado endereço Media Access Control (MAC). Isso permitiria a troca de dados dentro dessa rede. Se o host remoto estiver localizado em outra rede, o conhecimento do endereço MAC não é suficiente para estabelecer uma conexão. O endereçamento na Internet é feito através do endereço IPv4 e/ou IPv6, que é composto pelo endereço de rede e pelo endereço do host.

Não importa se é uma rede menor, como uma rede doméstica de computadores ou toda a Internet. O endereço IP garante a entrega dos dados ao destinatário correto. Podemos imaginar a representação dos endereços MAC e IPv4/IPv6 da seguinte forma:

- IPv4 / IPv6 - descreve o endereço postal exclusivo e o distrito do prédio do destinatário.
- MAC - descreve o andar exato e o apartamento do receptor.

É possível que um único endereço IP enderece vários receptores (transmissão) ou que um dispositivo responda a vários endereços IP. No entanto, deve-se garantir que cada endereço IP seja atribuído apenas uma vez na rede.

## IPv4 Structure

O método mais comum de atribuição de endereços IP é o IPv4, que consiste em um número binário de 32 bits combinado em 4 bytes que consistem em grupos de 8 bits (octetos) variando de 0 a 255. Estes são convertidos em números decimais mais facilmente legíveis, separados por pontos e representados como notação decimal com pontos.

Assim, um endereço IPv4 pode ter esta aparência:
- Binary: 0111 1111.0000 0000.0000 0000.0000 0001
- Decimal: 127.0.0.1

Cada interface de rede (placas de rede, impressoras de rede ou roteadores) recebe um endereço IP exclusivo.

O formato IPv4 permite 4.294.967.296 endereços únicos. O endereço IP é dividido em uma parte de host e uma parte de rede. O roteador atribui a parte do host do endereço IP em casa ou por um administrador. O respectivo administrador de rede atribui a parte de rede. Na Internet, é a IANA, que aloca e gerencia os IPs exclusivos.

No passado, outras classificações aconteciam aqui. Os blocos de rede IP foram divididos em classes A - E. As diferentes classes diferiram nos respectivos tamanhos de compartilhamento de host e rede.

|**`Class`**|**Network Address**|**First Address**|**Last Address**|**Subnetmask**|**CIDR**|**Subnets**|**IPs**|
|---|---|---|---|---|---|---|---|
|`A`|1.0.0.0|1.0.0.1|127.255.255.255|255.0.0.0|/8|127|16,777,214 + 2|
|`B`|128.0.0.0|128.0.0.1|191.255.255.255|255.255.0.0|/16|16,384|65,534 + 2|
|`C`|192.0.0.0|192.0.0.1|223.255.255.255|255.255.255.0|/24|2,097,152|254 + 2|
|`D`|224.0.0.0|224.0.0.1|239.255.255.255|Multicast|Multicast|Multicast|Multicast|
|`E`|240.0.0.0|240.0.0.1|255.255.255.255|reserved|reserved|reserved|reserved|




### Máscara de sub-rede

Uma separação adicional dessas classes em pequenas redes é feita com a ajuda de sub-redes. Essa separação é feita usando as máscaras de rede, que são tão longas quanto um endereço IPv4. Assim como as classes, ele descreve quais posições de bit dentro do endereço IP atuam como parte da rede ou parte do host.

- Classe A: 255.0.0.0
- Classe B: 255.255.0.0
- Classe C: 255.255.255.0

|**Class**|**Network Address**|**First Address**|**Last Address**|**`Subnetmask`**|**CIDR**|**Subnets**|**IPs**|
|---|---|---|---|---|---|---|---|
|**A**|1.0.0.0|1.0.0.1|127.255.255.255|`255.0.0.0`|/8|127|16,777,214 + 2|
|**B**|128.0.0.0|128.0.0.1|191.255.255.255|`255.255.0.0`|/16|16,384|65,534 + 2|
|**C**|192.0.0.0|192.0.0.1|223.255.255.255|`255.255.255.0`|/24|2,097,152|254 + 2|
|**D**|224.0.0.0|224.0.0.1|239.255.255.255|`Multicast`|Multicast|Multicast|Multicast|
|**E**|240.0.0.0|240.0.0.1|255.255.255.255|`reserved`|reserved|reserved|reserved|


#### Endereços de rede e gateway

Os dois IPs adicionais adicionados na coluna IPs são reservados para o chamado endereço de rede e o endereço de broadcast. Outra função importante desempenha o gateway padrão, que é o nome do endereço IPv4 do roteador que acopla redes e sistemas com diferentes protocolos e gerencia endereços e métodos de transmissão. É comum que o gateway padrão receba o primeiro ou o último endereço IPv4 atribuível em uma sub-rede. Este não é um requisito técnico, mas tornou-se um padrão de fato em ambientes de rede de todos os tamanhos.

|**Class**|**Network Address**|**`First Address`**|**Last Address**|**Subnetmask**|**CIDR**|**Subnets**|**`IPs`**|
|---|---|---|---|---|---|---|---|
|A|1.0.0.0|`1.0.0.1`|127.255.255.255|255.0.0.0|/8|127|16,777,214 `+ 2`|
|B|128.0.0.0|`128.0.0.1`|191.255.255.255|255.255.0.0|/16|16,384|65,534 `+ 2`|
|C|192.0.0.0|`192.0.0.1`|223.255.255.255|255.255.255.0|/24|2,097,152|254 `+ 2`|
|D|224.0.0.0|`224.0.0.1`|239.255.255.255|Multicast|Multicast|Multicast|Multicast|
|E|240.0.0.0|`240.0.0.1`|255.255.255.255|reserved|reserved|reserved|reserved|



#### Endereço de broadcast

A tarefa do endereço IP de transmissão é conectar todos os dispositivos em uma rede entre si. Broadcast em uma rede é uma mensagem que é transmitida a todos os participantes de uma rede e não requer nenhuma resposta. Dessa forma, um host envia um pacote de dados para todos os outros participantes da rede simultaneamente e, ao fazê-lo, comunica seu endereço IP, que os receptores podem usar para contatá-lo. Este é o último endereço IPv4 usado para a transmissão.

|**Class**|**Network Address**|**First Address**|**`Last Address`**|**Subnetmask**|**CIDR**|**Subnets**|**IPs**|
|---|---|---|---|---|---|---|---|
|A|1.0.0.0|1.0.0.1|`127.255.255.255`|255.0.0.0|/8|127|16,777,214 + 2|
|B|128.0.0.0|128.0.0.1|`191.255.255.255`|255.255.0.0|/16|16,384|65,534 + 2|
|C|192.0.0.0|192.0.0.1|`223.255.255.255`|255.255.255.0|/24|2,097,152|254 + 2|
|D|224.0.0.0|224.0.0.1|`239.255.255.255`|Multicast|Multicast|Multicast|Multicast|
|E|240.0.0.0|240.0.0.1|`255.255.255.255`|reserved|reserved|reserved|reserved|


## Sistema binário

O sistema binário é um sistema numérico que usa apenas dois estados diferentes que são representados em dois números (0 e 1) opostos ao sistema decimal (0 a 9).

Um endereço IPv4 é dividido em 4 octetos, como já vimos. Cada octeto consiste em 8 bits. Cada posição de um bit em um octeto tem um valor decimal específico. Vamos pegar o seguinte endereço IPv4 como exemplo:

- Endereço IPv4:  192.168.10.39

Aqui está um exemplo de como o primeiro octeto se parece:

```shell-session
Values:         128  64  32  16  8  4  2  1
Binary:           1   1   0   0  0  0  0  0
```

Se calcularmos a soma de todos esses valores para cada octeto em que o bit é definido como 1, obtemos a soma:

|   |   |   |
|---|---|---|
|1st|128 + 64 + 0 + 0 + 0 + 0 + 0 + 0|= `192`|
|2nd|128 + 0 + 32 + 0 + 8 + 0 + 0 + 0|= `168`|
|3rd|0 + 0 + 0 + 0 + 8 + 0 + 2 + 0|= `10`|
|4th|0 + 0 + 32 + 0 + 0 + 4 + 2 + 1|= `39`|

#### IPv4 - Notação binária

```shell-session
Octet:             1st         2nd         3rd         4th
Binary:         1100 0000 . 1010 1000 . 0000 1010 . 0010 0111
Decimal:           192    .    168    .     10    .     39
```

Essa adição ocorre para cada octeto, o que resulta em uma representação decimal do endereço IPv4. A máscara de sub-rede é calculada da mesma maneira.

### IPv4 - Decimal para binário

```shell-session
Values:         128  64  32  16  8  4  2  1
Binary:           1   1   1   1  1  1  1  1
```

|**Octet**|**Values**|**Sum**|
|---|---|---|
|1st|128 + 64 + 32 + 16 + 8 + 4 + 2 + 1|= `255`|
|2nd|128 + 64 + 32 + 16 + 8 + 4 + 2 + 1|= `255`|
|3rd|128 + 64 + 32 + 16 + 8 + 4 + 2 + 1|= `255`|
|4th|0 + 0 + 0 + 0 + 0 + 0 + 0 + 0|= `0`|

##### Máscara de sub-rede

```shell-session
Octet:             1st         2nd         3rd         4th
Binary:         1111 1111 . 1111 1111 . 1111 1111 . 0000 0000
Decimal:           255    .    255    .    255    .     0
```

# CIDR

Classless Inter-Domain Routing (CIDR) é um método de representação e substitui a atribuição fixa entre endereço IPv4 e classes de rede (A, B, C, D, E). A divisão é baseada na máscara de sub-rede ou no chamado sufixo CIDR, que permite a divisão bit a bit do espaço de endereço IPv4 e, portanto, em sub-redes de qualquer tamanho. O sufixo CIDR indica quantos bits desde o início do endereço IPv4 pertencem à rede. É uma notação que representa a máscara de sub-rede especificando o número de bits 1 na máscara de sub-rede.

Vamos nos ater ao seguinte endereço IPv4 e máscara de sub-rede como exemplo:
- IPv4: 192.168.10.39
- Máscara de sub-rede: 255.255.255.0

Agora toda a representação do endereço IPv4 e da máscara de sub-rede ficaria assim:
- CIDR: 192.168.10.39/24

O sufixo CIDR é, portanto, a soma de todos na máscara de sub-rede.

```shell-session
Octet:             1st         2nd         3rd         4th
Binary:         1111 1111 . 1111 1111 . 1111 1111 . 0000 0000 (/24)
Decimal:           255    .    255    .    255    .     0
```
