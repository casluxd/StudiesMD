Quando você cria um socket, você precisa especificar o estilo de comunicação que você deseja o usar e o tipo de protocolo que deve implementá-lo. O estilo de comunicação de um socket define a semântica de nível de usuário de envio e recebimentos de dados no socket. A escolha de um estilo de comunicação especifica as respostas para perguntas como estas:
- **Quais são as unidades de transmissão de dados?** Alguns estilos de comunicação consideram os dados como uma sequência de bytes sem estrutura maior; outros agrupam os bytes em registros(que são conhecidos neste contexto como pacotes)
- **Pode os dados serem perdidos durante uma operação normal?** Alguns estilos de comunicação garantem que todos os dados enviados cheguem na ordem em que foram enviados (bloqueio de travamentos do sistema ou da rede); outros estilos ocasionalmente perdem dados como parte normal da operação e podem, às vezes, entregar pacotes mais de uma vez ou na ordem errada. Projetar um programa para usar estilos de comunicação não confiáveis geralmente envolve tomar precauções para detectar pacotes perdidos ou mal ordenados e retransmitir dados conforme necessário.
- **A comunicação é inteira com um parceiro?** Alguns estilos de comunicação são como uma chamada telefônica - você faz uma conexão com um socket remoto e depois troca os dados livremente. Outros estilos são como cartas de correspondência - você especifica um endereço de destino para cada mensagem enviada.

Você também deve escolher um namespace para nomear o socket. Um nome de socket("address") é significativo apenas no contexto de um determinado namespace. Na verdade, até mesmo o tipo de dados a ser usado para um nome de socket pode depender do namespace. Namespaces também são chamados de "domains", mas evitamos essa palavra, pois pode ser confundida com outros usos do mesmo termo. Cada namespace tem um nome simbólico que começa com `PF_`. Um nome simbólico correspondente começando com `AF_` designa o formato de endereço para aquele namespace.

## Namespaces

No contexto de sockets, o termo "namespace" refere-se à forma como os sockets são identificados e organizados em um sistema operacional. Um namespace de socket é um espaço de nomes que define como os sockets são identificados e acessados dentro de um sistema.

Cada sistema operacional pode ter sua própria implementação de namespace de socket. No entanto, em sistemas baseados em Unix, como Linux, o namespace de socket mais comum é conhecido como "namespace de domínio de comunicação" (communication domain namespace), que é baseado no modelo de domínio de comunicação definido pelo POSIX.

Em um namespace de socket, cada socket é identificado por um conjunto de valores que inclui:

1. Domínio (Domain): Define a família de protocolos de comunicação que o socket pode usar, como AF_INET para IPv4, AF_INET6 para IPv6 ou AF_UNIX para comunicação local.
2. Tipo (Type): Especifica o tipo de serviço que o socket oferece, como SOCK_STREAM para comunicação confiável baseada em TCP ou SOCK_DGRAM para comunicação não confiável baseada em UDP.
3. Protocolo (Protocol): Indica o protocolo específico dentro do domínio, como IPPROTO_TCP para TCP ou IPPROTO_UDP para UDP. Geralmente, você pode especificar 0 para deixar o sistema operacional escolher automaticamente o protocolo apropriado com base no tipo do socket.

Ao criar um socket em um programa, você precisa especificar esses valores corretamente para identificar corretamente o tipo de conexão que você deseja estabelecer.

O uso de namespaces de socket permite que diferentes tipos de conexões sejam criados e gerenciados de forma independente. Por exemplo, um programa pode criar sockets em um namespace de domínio AF_INET para comunicação baseada em TCP/IP e, simultaneamente, criar sockets em um namespace de domínio AF_UNIX para comunicação local. Isso permite que o programa se comunique com diferentes protocolos ou serviços de forma isolada.

Em resumo, um namespace de socket é uma forma de organizar e identificar sockets dentro de um sistema operacional, usando valores de domínio, tipo e protocolo para distinguir e configurar conexões de rede.

Finalmente, você deve escolher o protocolo para realizar a comunicação. O protocolo determina qual mecanismo de baixo nível é usado para transmitir e receber dados. Cada protocolo é válido para um determinado namespace e estilo de comunicação; um namespace às vezes é chamado de `protocol family` por causa disso, e é por isso que os nomes dos namespaces com `PF_`.

As regras de um protocolo se aplicam à transmissão de dados entre dois programas, talvez em computadores diferentes; a maioria dessas regras é controlada pelo sistema operacional e você não precisa conhecê-lás. O que você precisa saber sobre protocolos é o seguinte:
- Para haver comunicação entre dois sockets, eles devem especificar o mesmo protocolo.
- Cada protocolo é significativo com combinações específicas de estilo/namespace e não pode ser usado com combinações inadequadas. Por exemplo, o protocolo TCP se ajusta apenas ao estilo de comunicação de fluxo de bytes e ao namespace da Internet.
- Para cada combinação de estilo e namespace existe um protocolo padrão, que você pode solicitar especificando 0 como o número do protocolo. E é isso que você deve fazer normalmente - use o padrão.

Ao longo da descrição a seguir, em vários locais, variáveis/parâmetros para denotar tamanhos são necessários. E aqui começa o problema. Nas primeiras implementações o tipo dessas variáveis era simplesmente int. Na maioria das máquinas da época, um int tinha 32 bits de largura, o que criou um padrão `de facto` que exigia variáveis de 32 bits. Isso é importante porque as referências a variáveis desse tipo são passadas para o kernel.

Então o pessoal do POSIX veio e unificou a interface com as palavras "todos os valores de tamanho são do tipo size_t". Em máquinas de 64 bits, size_t tem 64 bits de largura, portanto, os ponteiros para variáveis não eram mais possíveis.

A especificação Unix98 fornece uma solução introduzindo um socklen_t. Este tipo é usado em todos os casos que o POSIX mudou para usar size_t. O único requisito desse tipo é que seja um tipo não assinado de pelo menos 32 bits. Portanto, as implementações que exigem que sejam passadas referências a variáveis de 32 bits podem ser tão felizes quanto as implementações que usam valores de 64 bits.