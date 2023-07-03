
# Network Layer

A camada de rede (Camada 3) do OSI controla a troca de pacotes de dados, pois estes não podem ser roteados diretamente para o receptor e, portanto, devem ser fornecidos com nós de roteamento. Para implementar isso, a camada de rede identifica os nós de rede individuais, configura e limpa os canais de conexão e cuida do roteamento e do controle do fluxo de dados. Ao enviar os pacotes, os endereços são avaliados e os dados são roteados pela rede de nó a nó. Geralmente não há processamento dos dados nas camadas acima do L3 nos nós. Com base nos endereços, é feito o roteamento e a construção das tabelas de roteamento.

Em suma, é responsável pelas seguintes funções:

 - Logical Addressing
 - Routing

Os protocolos são definidos em cada camada do OSI, e esses protocolos representam uma coleção de regras para comunicação na respectiva camada. Eles são transparentes para os protocolos das camadas acima ou abaixo. Alguns protocolos cumprem tarefas de várias camadas e se estendem por duas ou mais camadas. Os protocolos mais utilizados nesta camada são:

- IPv4 / IPv6
- IPsec
- ICMP
- IGMP
- RIP
- OSPF

Ele garante o roteamento de pacotes da origem ao destino dentro ou fora de uma sub-rede. Essas duas sub-redes podem ter esquemas de endereçamento diferentes ou tipos de endereçamento incompatíveis. Em ambos os casos, a transmissão de dados em cada caso passa por toda a rede de comunicação e inclui o roteamento entre os nós da rede. Como a comunicação direta entre o remetente e o destinatário nem sempre é possível devido às diferentes sub-redes, os pacotes devem ser encaminhados dos nós (roteadores) que estão a caminho. Os pacotes encaminhados não alcançam as camadas superiores, mas recebem um novo destino intermediário e são enviados para o próximo nó. Anterior Marcar como concluído e próximo Próximo