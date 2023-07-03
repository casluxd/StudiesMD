Dois modelos de rede descrevem a comunicação e a transferência de dados de um host para outro, chamados de modelo ISO/OSI e modelo TCP/IP. Esta é uma representação simplificada das chamadas camadas que representam os Bits transferidos em conteúdos legíveis para nós.

![[Pasted image 20230527133307.png]]

## OSI Model

O modelo OSI, muitas vezes referido como modelo de camada ISO/OSI, é um modelo de referência que pode ser usado para descrever e definir a comunicação entre sistemas. O modelo de referência tem sete camadas individuais, cada uma com tarefas claramente separadas.

O termo OSI significa Open Systems Interconnection Model, publicado pela International Telecommunication Union (ITU) e pela International Organization for Standardization (ISO). Portanto, o modelo OSI é geralmente chamado de modelo de camada ISO/OSI.

## TCP/IP Model

TCP/IP (Transmission Control Protocol/Internet Protocol) é um termo genérico para muitos protocolos de rede. Os protocolos são responsáveis ​​pela comutação e transporte de pacotes de dados na Internet. A Internet é totalmente baseada na família de protocolos TCP/IP. No entanto, TCP/IP não se refere apenas a esses dois protocolos, mas geralmente é usado como um termo genérico para toda uma família de protocolos.

Por exemplo, ICMP (Internet Control Message Protocol) ou UDP (User Datagram Protocol) pertence à família de protocolos. A família de protocolos fornece as funções necessárias para transportar e comutar pacotes de dados em uma rede privada ou pública.

## ISO/OSI vs TCP/IP

TCP/IP é um protocolo de comunicação que permite que os hosts se conectem à Internet. Refere-se ao Protocolo de Controle de Transmissão usado em e por aplicativos na Internet. Ao contrário do OSI, permite um aligeiramento das regras que devem ser seguidas, desde que sejam seguidas orientações gerais.

OSI, por outro lado, é um gateway de comunicação entre a rede e os usuários finais. O modelo OSI é geralmente chamado de modelo de referência porque é mais antigo. Também é conhecido por seu protocolo rígido e limitações.

## Packet Transfers

Em um sistema em camadas, os dispositivos em uma camada trocam dados em um formato diferente chamado de unidade de dados de protocolo (PDU). Por exemplo, quando queremos navegar em um site no computador, o software do servidor remoto primeiro passa os dados solicitados para a camada de aplicativo. É processado camada por camada, cada camada executando suas funções atribuídas. Os dados são então transferidos pela camada física da rede até que o servidor de destino ou outro dispositivo os receba. Os dados são roteados pelas camadas novamente, com cada camada executando suas operações atribuídas até que o software receptor use os dados.

![[Pasted image 20230527133520.png]]

Durante a transmissão, cada camada adiciona um cabeçalho à PDU da camada superior, que controla e identifica o pacote. Este processo é chamado de encapsulamento. O cabeçalho e os dados juntos formam a PDU para a próxima camada. O processo continua até a Camada Física ou Camada de Rede, onde os dados são transmitidos ao receptor. O receptor inverte o processo e descompacta os dados em cada camada com as informações do cabeçalho. Depois disso, o aplicativo finalmente usa os dados. Este processo continua até que todos os dados tenham sido enviados e recebidos.

![[Pasted image 20230527133547.png]]

Para nós, como testadores de penetração, ambos os modelos de referência são úteis. Com o TCP/IP, podemos entender rapidamente como toda a conexão é estabelecida e, com o ISO, podemos desmontá-la peça por peça e analisá-la em detalhes. Isso geralmente acontece quando podemos ouvir e interceptar tráfego de rede específico. Portanto, devemos nos familiarizar com ambos os modelos de referência e compreendê-los e internalizá-los da melhor maneira possível.

# OSI Model

O objetivo na definição do padrão ISO/OSI foi criar um modelo de referência que permita a comunicação de diferentes sistemas técnicos por meio de vários dispositivos e tecnologias e forneça compatibilidade. O modelo OSI usa sete camadas diferentes, que são hierarquicamente baseadas umas nas outras para atingir esse objetivo. Essas camadas representam fases no estabelecimento de cada conexão por onde passam os pacotes enviados. Dessa forma, o padrão foi criado para traçar como uma conexão é estruturada e estabelecida visualmente.

1. Camada Física:
    - A camada Física lida com a transmissão física dos dados, convertendo os bits em sinais elétricos, ópticos ou de rádio, dependendo do meio de transmissão.
    - Ela define as características mecânicas, elétricas e funcionais dos dispositivos físicos, como cabos, conectores e interfaces de rede.
    - A camada Física trata de aspectos como taxa de transmissão, voltagem dos sinais, largura de banda, sincronização e a distância máxima permitida entre dispositivos.
2. Camada de Enlace de Dados:
    - A camada de Enlace de Dados fornece um canal de comunicação confiável e livre de erros entre dispositivos conectados diretamente.
    - Ela segmenta os dados recebidos da camada de Rede em quadros (frames) para transmissão e os reassembla no destino.
    - A camada de Enlace de Dados também lida com o controle de acesso ao meio, gerenciando colisões em redes compartilhadas, como o Ethernet.
    - Ela garante a integridade dos dados, adicionando informações de detecção e correção de erros aos quadros transmitidos.
3. Camada de Rede:
    - A camada de Rede é responsável pelo roteamento e encaminhamento dos pacotes de dados pela rede.
    - Ela define os endereços lógicos (endereços IP) e o protocolo utilizado para encaminhar os pacotes pelos roteadores.
    - A camada de Rede determina o melhor caminho para a entrega dos pacotes, com base em algoritmos de roteamento, como o OSPF (Open Shortest Path First) ou o BGP (Border Gateway Protocol).
    - Ela também lida com a fragmentação e remontagem dos pacotes quando o tamanho excede a capacidade do meio de transmissão.
4. Camada de Transporte:
    - A camada de Transporte garante a entrega confiável e ordenada dos dados entre as aplicações de origem e destino.
    - Ela segmenta os dados recebidos da camada de Aplicação em unidades menores, adicionando cabeçalhos para controle de fluxo, controle de erros e sequenciamento.
    - O protocolo TCP (Transmission Control Protocol) opera nessa camada, oferecendo um serviço orientado à conexão e garantindo que os dados sejam entregues corretamente.
    - O protocolo UDP (User Datagram Protocol) também opera nessa camada, mas sem a garantia de entrega confiável, tornando-o mais adequado para aplicações de streaming ou comunicações em tempo real.
5. Camada de Sessão:
    - A camada de Sessão estabelece, gerencia e finaliza as sessões de comunicação entre as aplicações.
    - Ela permite que as aplicações estabeleçam conexões, sincronizem o diálogo e gerenciem pontos de verificação para recuperação em caso de falhas.
    - A camada de Sessão também pode lidar com funções de autenticação, autorização e controle de acesso.
6. Camada de Apresentação:
    - A camada de Apresentação lida com a representação dos dados, garantindo que eles sejam entendidos e interpretados corretamente pelo destinatário.
    - Ela trata da codificação, compressão e criptografia dos dados.
    - Essa camada pode converter formatos de dados para que sejam compatíveis entre diferentes sistemas e também permite a criptografia dos dados para garantir a segurança da comunicação.
7. Camada de Aplicação:
    - A camada de Aplicação é a camada mais alta do modelo OSI e contém os protocolos e serviços usados pelas aplicações de usuário final.
    - Ela oferece interfaces para que os aplicativos possam acessar os serviços de rede.
    - Exemplos de protocolos e serviços nessa camada incluem HTTP (Hypertext Transfer Protocol), SMTP (Simple Mail Transfer Protocol), FTP (File Transfer Protocol) e SSH (Secure Shell).

As camadas 2-4 são orientadas para transporte e as camadas 5-7 são camadas orientadas para aplicação. Em cada camada, tarefas definidas com precisão são executadas e as interfaces para as camadas vizinhas são descritas com precisão. Cada camada oferece serviços para uso na camada diretamente acima dela. Para disponibilizar esses serviços, a camada utiliza os serviços da camada abaixo dela e executa as tarefas de sua camada.

Se dois sistemas se comunicam, todas as sete camadas do modelo OSI são executadas pelo menos duas vezes, pois tanto o emissor quanto o receptor devem levar em consideração o modelo de camada. Portanto, um grande número de tarefas diferentes deve ser executado nas camadas individuais para garantir a segurança, confiabilidade e desempenho da comunicação.

Quando um aplicativo envia um pacote para o outro sistema, o sistema trabalha as camadas mostradas acima da camada 7 até a camada 1, e o sistema receptor descompacta o pacote recebido da camada 1 até a camada 7.

Vamos considerar o exemplo de uma aplicação cliente-servidor usando o protocolo TCP/IP:

1. Camada de Aplicação (lado cliente):
    - A aplicação cliente envia uma solicitação para estabelecer uma conexão TCP com um servidor remoto.
    - A aplicação cliente especifica o endereço IP do servidor (por exemplo, um endereço IP público ou um nome de domínio que será resolvido para um endereço IP).
2. Camada de Transporte:
    - A camada de transporte (como o TCP) é responsável pelo estabelecimento da conexão TCP.
    - Ela define uma porta de origem (porta de origem do cliente) e uma porta de destino (porta do serviço no servidor) para a comunicação.
3. Camada de Rede:
    - A camada de rede (como o IP) é responsável pelo roteamento dos pacotes entre redes.
    - Nesta etapa, o endereço IP de destino é definido na camada de rede, com base nas informações fornecidas pela aplicação cliente.
4. Camada de Enlace de Dados:
    - A camada de enlace de dados (por exemplo, Ethernet) lida com a comunicação entre dispositivos conectados na mesma rede local.
    - Ela adiciona cabeçalhos e trailers aos pacotes para fornecer informações de controle necessárias para a comunicação física.
5. Camada Física:
    - A camada física lida com a transmissão física dos bits por meio de cabos, sinais elétricos, ópticos ou de rádio.

# TCP/IP Model

O modelo TCP/IP também é um modelo de referência em camadas, muitas vezes chamado de Internet Protocol Suite. O termo TCP/IP significa os dois protocolos Transmission Control Protocol (TCP) e Internet Protocol (IP). O IP está localizado na camada de rede (Camada 3) e o TCP está localizado na camada de transporte (Camada 4) do modelo de camada OSI.

1. Link (Enlace): A camada de enlace é responsável por colocar os pacotes TCP/IP no meio da rede e receber os pacotes correspondentes do meio da rede. O TCP/IP foi projetado para funcionar independentemente do método de acesso à rede, formato do quadro e mídia.
2. Internet: A camada Internet é responsável pelo endereçamento do host, empacotamento e funções de roteamento.
3. Transport (Transporte): A Camada de Transporte é responsável por fornecer serviços de sessão (TCP) e datagrama (UDP) para a Camada de Aplicação.
4. Application (Aplicação): A Camada de Aplicação permite que os aplicativos acessem os serviços das outras camadas e define os protocolos que os aplicativos usam para trocar dados.

Com o TCP/IP, todo aplicativo pode transferir e trocar dados em qualquer rede, não importa onde o receptor esteja localizado. O IP garante que o pacote de dados chegue ao seu destino e o TCP controla a transferência de dados e garante a conexão entre o fluxo de dados e o aplicativo. A principal diferença entre TCP/IP e OSI é o número de camadas, algumas das quais foram combinadas.

![[Pasted image 20230527134127.png]]

As tarefas mais importantes do TCP/IP são:

- Logical Addressing (Endereço Lógico) - Protocolo IP: Devido a muitos hosts em redes diferentes, existe a necessidade de estruturar a topologia da rede e o endereçamento lógico. No TCP/IP, o IP assume o endereçamento lógico de redes e nós. Os pacotes de dados só chegam à rede onde deveriam estar. Os métodos para fazer isso são classes de rede, sub-redes e CIDR.
- Routing (Roteamento) - Protocolo IP: Para cada pacote de dados, o próximo nó é determinado em cada nó no caminho do remetente ao destinatário. Dessa forma, um pacote de dados é roteado para seu receptor, mesmo que sua localização seja desconhecida do remetente.
- Error & Control Flow (Fluxo de controle e erro) - Protocolo TCP: O remetente e o destinatário estão frequentemente em contato um com o outro por meio de uma conexão virtual. Portanto, mensagens de controle são enviadas continuamente para verificar se a conexão ainda está estabelecida.
- Application Support(Suporte de aplicativo) - Protocolo TCP: As portas TCP e UDP formam uma abstração de software para distinguir aplicativos específicos e seus links de comunicação.
- Name Resolution (Resolução do nome) - Protocolo DNS: O DNS fornece resolução de nomes por meio de nomes de domínio totalmente qualificados (FQDN) em endereços IP, permitindo que alcancemos o host desejado com o nome especificado na Internet.

