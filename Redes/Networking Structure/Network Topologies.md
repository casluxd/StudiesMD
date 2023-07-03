
Uma topologia de rede é um arranjo típico e conexão física ou lógica de dispositivos em uma rede. Os computadores são hosts, como clientes e servidores, que usam ativamente a rede. Eles também incluem componentes de rede, como switches, pontes e roteadores, que discutiremos com mais detalhes nas seções posteriores, que têm uma função de distribuição e garantem que todos os hosts da rede possam estabelecer uma conexão lógica entre si. A topologia da rede determina os componentes a serem utilizados e os métodos de acesso ao meio de transmissão.

O layout do meio de transmissão usado para conectar dispositivos é a topologia física da rede. Para mídia condutiva ou de fibra de vidro, isso se refere ao plano de cabeamento, as posições dos nós, e as conexões entre os nós e o cabeamento. Em contraste, a topologia lógica é como os sinais agem na mídia da rede ou como os dados serão transmitidos pela rede de um dispositivo para a conexão física dos dispositivos.

Podemos dividir toda a área de topologia de rede em três áreas:

# 1. Conexões

## Wired Connection

1. Coaxial cabling
2. Glass fiber cabling
3. Twisted-pair cabling

## Wireless Connection

1. Wi-Fi
2. Celular
3. Satelite

# 2. Nodes

Os nós de rede são os pontos de conexão do meio de transmissão para transmissores e receptores de sinais elétricos, ópticos ou de rádio no meio. Um nó pode ser conectado a um computador, mas certos tipos podem ter apenas um microcontrolador em um nó ou podem não ter nenhum dispositivo programável.

# 3. Classificações

Podemos imaginar uma topologia como uma forma ou estrutura virtual de uma rede. Esta forma não corresponde necessariamente ao arranjo físico real dos dispositivos na rede. Portanto, essas topologias podem ser físicas ou lógicas. Por exemplo, os computadores em uma LAN podem estar dispostos em círculo em um quarto, mas é muito improvável que tenham uma topologia em anel real.

As topologias de rede são divididas nos oito tipos básicos a seguir:

- Point-to-Point
- Star
- Mesh
- Hybrid
- Bus
- Ring
- Tree
- Daisy Chain

Redes mais complexas podem ser construídas como híbridas de duas ou mais das topologias básicas mencionadas acima.

## Point-to-Point

A topologia de rede mais simples com uma conexão dedicada entre dois hosts é a topologia ponto a ponto. Nesta topologia, existe um link físico direto e direto apenas entre dois hosts. Esses dois dispositivos podem usar essas conexões para comunicação mútua.

As topologias ponto a ponto são o modelo básico da telefonia tradicional e não devem ser confundidas com P2P (arquitetura Peer-to-Peer).

![[Pasted image 20230526225433.png]]

## Bus

Todos os hosts são conectados por meio de um meio de transmissão na topologia de barramento. Cada host tem acesso ao meio de transmissão e aos sinais que são transmitidos por ele. Não há nenhum componente de rede central que controle os processos nela. O meio de transmissão para isso pode ser, por exemplo, um cabo coaxial.

Como o meio é compartilhado com todos os outros, apenas um host pode enviar, e todos os outros podem apenas receber e avaliar os dados e ver se eles são destinados a si mesmos.

![[Pasted image 20230526225613.png]]

## Star

A topologia em estrela é um componente de rede que mantém uma conexão com todos os hosts. Cada host é conectado ao componente de rede central por meio de um link separado. Isso geralmente é um roteador, um hub ou um switch. Estes lidam com a função de encaminhamento para os pacotes de dados. Para fazer isso, os pacotes de dados são recebidos e encaminhados ao destino. O tráfego de dados no componente de rede central pode ser muito alto, pois todos os dados e conexões passam por ele.

![[Pasted image 20230526225643.png]]

## Ring

A topologia física do anel é tal que cada host ou nó é conectado ao anel com dois cabos:

- Um para sinais de entrada
- Um para sinais de saída

Isso significa que um cabo chega a cada host e um cabo sai. A topologia em anel normalmente não requer um componente de rede ativo. O controle e o acesso ao meio de transmissão são regulados por um protocolo ao qual todas as estações aderem.

Uma topologia de anel lógico é baseada em uma topologia em estrela física, onde um distribuidor no nó simula o anel encaminhando de uma porta para a seguinte.

A informação é transmitida em uma direção de transmissão predeterminada. Normalmente, o meio de transmissão é acessado sequencialmente de estação para estação usando um sistema de recuperação da estação central ou um token. Um token é um padrão de bits que passa continuamente por uma rede em anel em uma direção, que funciona de acordo com o processo de reivindicação de token.

![[Pasted image 20230526225834.png]]

## Mesh

Muitos nós decidem sobre as conexões em nível físico e o roteamento em nível lógico em redes em malha. Portanto, as estruturas em malha não têm topologia fixa. Existem duas estruturas básicas do conceito básico: a estrutura totalmente em malha e a estrutura parcialmente em malha.

Cada host está conectado a todos os outros hosts na rede em uma estrutura totalmente em malha. Isso significa que os hosts estão interligados. Essa técnica é usada principalmente em WAN ou MAN para garantir alta confiabilidade e largura de banda.

Nesta configuração, nós de rede importantes, como roteadores, podem ser conectados em rede. Se um roteador falhar, os outros podem continuar funcionando sem problemas e a rede pode absorver a falha devido às muitas conexões.

Cada nó de uma topologia totalmente em malha possui as mesmas funções de roteamento e conhece os nós vizinhos com os quais pode se comunicar com proximidade ao gateway de rede e cargas de tráfego.

Na estrutura parcialmente em malha, os pontos finais são conectados por apenas uma conexão. Neste tipo de topologia de rede, nós específicos são conectados a exatamente um outro nó, e alguns outros nós estão conectados a dois ou mais outros nós com uma conexão ponto a ponto.

![[Pasted image 20230526225939.png]]

## Tree

A topologia em árvore é uma topologia em estrela estendida que redes locais mais extensas possuem nesta estrutura. Isso é especialmente útil quando várias topologias são combinadas. Esta topologia é frequentemente utilizada, por exemplo, em grandes edifícios empresariais.

Existem estruturas lógicas de árvore de acordo com a spanning tree e físicas. As redes modernas modulares, baseadas em cabeamento estruturado com hierarquia de hub, também possuem uma estrutura em árvore. As topologias em árvore também são utilizadas para redes de banda larga e redes urbanas (MAN).

![[Pasted image 20230526230004.png]]


## Hybrid

As redes híbridas combinam duas ou mais topologias para que a rede resultante não apresente nenhuma topologia padrão. Por exemplo, uma rede em árvore pode representar uma topologia híbrida na qual as redes em estrela são conectadas por meio de redes de barramento interconectadas. No entanto, uma rede de árvores que está ligada a outra rede de árvores ainda é topologicamente uma rede de árvores. Uma topologia híbrida sempre é criada quando duas topologias de rede básicas diferentes são interconectadas.

![[Pasted image 20230526230110.png]]

## Daisy Chain

Na topologia daisy chain, vários hosts são conectados colocando um cabo de um nó para outro.

Como isso cria uma cadeia de conexões, também é conhecida como configuração em cadeia, na qual vários componentes de hardware são conectados em série. Esse tipo de rede é frequentemente encontrado na tecnologia de automação (CAN).

O encadeamento em série é baseado no arranjo físico dos nós, em contraste com os procedimentos de token, que são estruturais, mas podem ser feitos independentemente do layout físico. O sinal é enviado de e para um componente por meio de seus nós anteriores ao sistema de computador.

![[Pasted image 20230526230143.png]]

