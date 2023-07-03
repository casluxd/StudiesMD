A divisão de um intervalo de endereços IPv4 em vários intervalos de endereços menores é chamada sub-rede.

Uma sub-rede é um segmento lógico de uma rede que usa endereços IP com o mesmo endereço de rede. Podemos pensar em uma sub-rede como uma entrada rotulada em um grande corredor de prédio. Por exemplo, pode ser uma porta de vidro que separa vários departamentos de um prédio da empresa. Com a ajuda da sub-rede, podemos criar uma sub-rede específica por nós mesmos ou descobrir o seguinte esboço da respectiva rede:
- Endereço de rede
- Endereço de broadcast
- Primeiro host
- Último host
- Número de hosts

Tomemos como exemplo o seguinte endereço IPv4 e máscara de sub-rede:
- Endereço IPv4: 192.168.12.160
- Máscara de sub-rede: 255.255.255.192
- CIDR: 192.168.12.160/26

Já sabemos que um endereço IP é dividido em parte de rede e parte de host.

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|`1100 0000`|`1010 1000`|`0000 1100`|`10`10 0000|192.168.12.160`/26`|
|Subnet mask|`1111 1111`|`1111 1111`|`1111 1111`|`11`00 0000|`255.255.255.192`|
|Bits|/8|/16|/24|/32|

Na criação de sub-redes, usamos a máscara de sub-rede como modelo para o endereço IPv4. A partir dos 1 bits na máscara de sub-rede, sabemos quais bits no endereço IPv4 não podem ser alterados. Estes são fixos e, portanto, determinam a "rede principal" na qual a sub-rede está localizada.

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|1100 0000|1010 1000|0000 1100|10`10 0000`|192.168.12.160/26|
|Subnet mask|1111 1111|1111 1111|1111 1111|11`00 0000`|255.255.255.192|
|Bits|/8|/16|/24|/32|

Os bits na parte do host podem ser alterados para o primeiro e o último endereço. O primeiro endereço é o endereço de rede e o último endereço é o endereço de broadcast para a respectiva sub-rede.

O endereço de rede é vital para a entrega de um pacote de dados. Se o endereço de rede for o mesmo para os endereços de origem e destino, o pacote de dados será entregue na mesma sub-rede. Se os endereços de rede forem diferentes, o pacote de dados deve ser roteado para outra sub-rede por meio do gateway padrão.

A máscara de sub-rede determina onde essa separação ocorre.

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|1100 0000|1010 1000|0000 1100|10`\|`10 0000|192.168.12.160/26|
|Subnet mask|`1111 1111`|`1111 1111`|`1111 1111`|`11\|`00 0000|255.255.255.192|
|Bits|/8|/16|/24|/32|

#### Endereço de rede

Portanto, se agora definirmos todos os bits como 0 na parte do host do endereço IPv4, obteremos o endereço de rede da respectiva sub-rede.

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|1100 0000|1010 1000|0000 1100|10`\|00 0000`|`192.168.12.128`/26|
|Subnet mask|`1111 1111`|`1111 1111`|`1111 1111`|`11\|`00 0000|255.255.255.192|
|Bits|/8|/16|/24|/32|

#### Endereço de broadcast

Se definirmos todos os bits na parte do host do endereço IPv4 como 1, obteremos o endereço de broadcast.

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|1100 0000|1010 1000|0000 1100|10`\|11 1111`|`192.168.12.191`/26|
|Subnet mask|`1111 1111`|`1111 1111`|`1111 1111`|`11\|`00 0000|255.255.255.192|
|Bits|/8|/16|/24|/32|

Como agora sabemos que os endereços IPv4 192.168.12.128 e 192.168.12.191 são atribuídos, todos os outros endereços IPv4 estão entre 192.168.12.129-190. Agora sabemos que esta sub-rede nos oferece um total de 64 - 2 (endereço de rede e endereço de broadcast) ou 62 endereços IPv4 que podemos atribuir aos nossos hosts.

|**Hosts**|**IPv4**|
|---|---|
|Network Address|`192.168.12.128`|
|First Host|`192.168.12.129`|
|Other Hosts|`...`|
|Last Host|`192.168.12.190`|
|Broadcast Address|`192.168.12.191`|

## Sub-redes em redes menores

Vamos supor agora que nós, como administradores, recebemos a tarefa de dividir a sub-rede atribuída a nós em 4 sub-redes adicionais. Assim, é fundamental saber que só podemos dividir as sub-redes com base no sistema binário.

|**Exponent**|**Value**|
|---|---|
|2`^0`|= 1|
|2`^1`|= 2|
|2`^2`|= 4|
|2`^3`|= 8|
|2`^4`|= 16|
|2`^5`|= 32|
|2`^6`|= 64|
|2`^7`|= 128|
|2`^8`|= 256|

Portanto, podemos dividir os 64 hosts que conhecemos por 4. O 4 é igual ao expoente 2^2 no sistema binário, então descobrimos o número de bits para a máscara de sub-rede pela qual devemos estendê-la. Assim, conhecemos os seguintes parâmetros:
- Sub-rede: 192.168.12.128/26
- Sub-redes requeridas: 4

Agora aumentamos/expandimos nossa máscara de sub-rede em 2 bits de /26 para /28, e fica assim:

|**Details of**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|**Decimal**|
|---|---|---|---|---|---|
|IPv4|1100 0000|1010 1000|0000 1100|1000`\|` 0000|192.168.12.128`/28`|
|Subnet mask|`1111 1111`|`1111 1111`|`1111 1111`|`1111\|` 0000|`255.255.255.240`|
|Bits|/8|/16|/24|/32|

A seguir, podemos dividir os 64 endereços IPv4 que estão à nossa disposição em 4 partes:

|**Hosts**|**Math**|**Subnets**|**Host range for each subnet**|
|---|---|---|---|
|64|/|4|= `16`|

|**Subnet No.**|**Network Address**|**First Host**|**Last Host**|**Broadcast Address**|**CIDR**|
|---|---|---|---|---|---|
|1|`192.168.12.128`|192.168.12.129|192.168.12.142|`192.168.12.143`|192.168.12.128/28|
|2|`192.168.12.144`|192.168.12.145|192.168.12.158|`192.168.12.159`|192.168.12.144/28|
|3|`192.168.12.160`|192.168.12.161|192.168.12.174|`192.168.12.175`|192.168.12.160/28|
|4|`192.168.12.176`|192.168.12.177|192.168.12.190|`192.168.12.191`|192.168.12.176/28|

## Mental Subnetting

Pode parecer que há muita matemática envolvida na criação de sub-redes, mas cada octeto se repete e tudo é uma potência de dois, então não precisa haver muita memorização. A primeira coisa a fazer é identificar qual octeto muda.

|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|
|---|---|---|---|
|/8|/16|/24|/32|

É possível identificar qual octeto do endereço IP pode mudar lembrando-se desses quatro números. Dado o endereço de rede: 192.168.1.1/25, é imediatamente aparente que 192.168.2.4 não estaria na mesma rede porque a sub-rede /25 significa que apenas o quarto octeto pode mudar.

A próxima parte identifica o tamanho que cada sub-rede pode ter, mas dividindo oito pela rede e observando o restante. Isso também é chamado de Modulo Operation (%) e é muito utilizado em criptologia. Dado nosso exemplo anterior de /25, (25 % 8) seria 1. Isso ocorre porque oito cabe em 25 três vezes (8 * 3 = 24). Sobrou 1, que é o bit de rede reservado para a máscara de rede. Há um total de oito bits em cada octeto de um endereço IP. Se for usado um para a máscara de rede, a equação torna-se 2^(8-1) ou 2^7, 128. A tabela abaixo contém todos os números.

|**Remainder**|**Number**|**Exponential Form**|**Division Form**|
|---|---|---|---|
|0|256|2^8|256|
|1|128|2^7|256/2|
|2|64|2^6|256/2/2|
|3|32|2^5|256/2/2/2|
|4|16|2^4|256/2/2/2/2|
|5|8|2^3|256/2/2/2/2/2|
|6|4|2^2|256/2/2/2/2/2/2|
|7|2|2^1|256/2/2/2/2/2/2/2|

Ao lembrar as potências de dois até oito, pode se tornar um cálculo instantâneo. No entanto, se esquecido, pode ser mais rápido lembrar de dividir 256 pela metade o número de vezes o restante.

A parte complicada disso é obter o intervalo real do endereço IP porque 0 é um número e não nulo na rede. Portanto, em nosso /25 com 128 endereços IP, o primeiro intervalo é 192.168.1.0-127. O primeiro endereço é a rede e o último é o endereço de broadcast, o que significa que o espaço IP utilizável se tornaria 192.168.1.1-126. Se nosso endereço IP ficar acima de 128, o espaço ip utilizável será 192.168.129-254 (128 é a rede e 255 é a transmissão).