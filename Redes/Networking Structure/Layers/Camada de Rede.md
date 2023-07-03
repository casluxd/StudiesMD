A camada de rede é a terceira camada do modelo OSI (ou do modelo TCP/IP) e está localizada acima da camada de enlace de dados. Sua principal função é fornecer roteamento e encaminhamento dos pacotes de dados entre redes distintas.

A funcionalidade da camada de rede é realizar o roteamento dos pacotes de dados de origem para o destino correto através da rede. Isso envolve a seleção do melhor caminho para transmitir os dados, considerando fatores como custo, disponibilidade, congestionamento e qualidade do serviço.

A camada de rede encapsula os dados recebidos da camada de transporte em pacotes, também conhecidos como datagramas. Cada pacote contém informações como endereço de origem, endereço de destino e informações de controle. Os principais protocolos utilizados na camada de rede incluem o IP (Internet Protocol), que é responsável pelo endereçamento e roteamento dos pacotes, e o ICMP (Internet Control Message Protocol), que é usado para trocar informações e mensagens de controle entre os dispositivos de rede.

Entre as principais funcionalidades da camada de rede estão:
1. Endereçamento lógico: A camada de rede utiliza endereços lógicos (como o endereço IP) para identificar de forma exclusiva cada dispositivo e rede na internet ou em uma rede local.
2. Roteamento: A camada de rede decide a melhor rota para transmitir os pacotes de dados entre diferentes redes. Isso envolve a seleção de rotas baseadas em algoritmos de roteamento, como o roteamento estático ou dinâmico.
3. Fragmentação e remontagem: A camada de rede pode fragmentar os pacotes de dados em pedaços menores para que sejam transmitidos com sucesso em redes com restrições de tamanho máximo de pacotes. O receptor na extremidade oposta é responsável por remontar os fragmentos em pacotes completos.
4. Controle de congestionamento: A camada de rede pode gerenciar o congestionamento na rede, controlando a taxa de envio de pacotes e a adaptação da transmissão com base em informações de feedback da rede.
5. Encaminhamento: A camada de rede realiza o encaminhamento dos pacotes, decidindo qual caminho seguir com base nas tabelas de roteamento configuradas nos dispositivos de rede.

Em resumo, a camada de rede é responsável por fornecer roteamento e encaminhamento dos pacotes de dados entre redes distintas. Ela utiliza protocolos como o IP para endereçamento, roteamento e entrega confiável dos dados. A camada de rede desempenha um papel fundamental na comunicação eficiente e interconexão de redes.

# Fragmentação

O limite para fragmentação de pacotes de dados varia de acordo com o protocolo de rede e o tipo de rede em uso. No caso do protocolo IP (Internet Protocol), que é amplamente utilizado em redes TCP/IP, o tamanho máximo do pacote antes de ocorrer a fragmentação é conhecido como MTU (Maximum Transmission Unit).

O valor padrão do MTU para redes Ethernet é de 1500 bytes. Isso significa que, em uma rede Ethernet, qualquer pacote de dados com tamanho maior que 1500 bytes será fragmentado em pacotes menores antes de ser transmitido.

No entanto, é importante observar que diferentes tecnologias de rede podem ter MTUs diferentes. Por exemplo, em redes Wi-Fi, o MTU pode ser menor, geralmente em torno de 1200 bytes ou até menos, devido a limitações de largura de banda e interferências.

Além disso, a fragmentação de pacotes pode ocorrer em pontos intermediários da rede, como roteadores, quando a MTU de uma interface é menor do que a MTU da interface de entrada do pacote. Nesse caso, os roteadores fragmentarão os pacotes maiores para se adequar à MTU da interface de saída.

É importante mencionar que a fragmentação de pacotes pode ter um impacto negativo no desempenho da rede, pois aumenta a sobrecarga e pode resultar em maior latência e perda de pacotes. Portanto, é recomendado que as aplicações e dispositivos de rede ajustem o tamanho dos pacotes transmitidos para evitar a fragmentação sempre que possível, respeitando os limites da MTU da rede em uso.