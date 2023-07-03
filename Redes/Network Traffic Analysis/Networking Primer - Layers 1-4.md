**1**Esta seção serve como uma atualização rápida sobre redes e como funcionam alguns protocolos padrão que podemos ver durante a captura de tráfego. Esses conceitos estão no centro da captura e análise do tráfego. Sem uma compreensão fundamental do fluxo de rede típico e quais portas e protocolos são usados, não podemos analisar com precisão qualquer tráfego que capturamos.

![[Pasted image 20230529200703.png]]

A imagem acima oferece uma excelente visão do modelo Open Systems Interconnect (OSI) e do modelo Transmission Control Protocol - Internet Protocol (TCP-IP) lado a lado.

#### Model Traits Comparison.

|Trait|OSI|TCP-IP|
|---|---|---|
|Layers|Seven|Four|
|Flexibility|Strict|Loose|
|Dependency|Protocol independent & generic|Based on common communication protocols|

Ao examinar esses dois modelos, podemos notar que o modelo OSI é mais segmentado que o modelo TCP-IP. Isso ocorre porque ele é dividido em pequenos pedaços funcionais. As camadas de um a quatro do modelo OSI são focadas no controle do transporte de dados entre os hosts. Este controle inclui desde o meio físico utilizado para transmissão até o protocolo utilizado para gerenciar a conversa ou a falta dele no transporte de dados. As camadas cinco a sete lidam com a interpretação, gerenciamento e apresentação dos dados encapsulados apresentados ao usuário final. Pense no modelo OSI como a teoria por trás de como tudo funciona, enquanto o modelo TCP-IP está mais alinhado com a funcionalidade real da rede. O modelo TCP-IP é um pouco mais misto e as regras são flexíveis. O modelo TCP-IP compreende quatro camadas onde as camadas cinco, seis e sete do modelo OSI se alinham com a camada quatro do modelo TCP-IP. A camada três lida com o transporte, a camada dois é a camada de internet que se alinha com a camada de rede no OSI, e a camada um é a camada de enlace que cobre as camadas dois e um do modelo OSI.

Ao longo deste módulo, examinaremos muitas Unidades de dados de protocolo (PDU) diferentes, portanto, é necessária uma compreensão funcional de como ela aparece na teoria e na rede. Uma PDU é um pacote de dados composto de informações de controle e dados encapsulados de cada camada do modelo OSI. A divisão abaixo mostrará como as camadas nos dois modelos correspondem a uma PDU.

![[Pasted image 20230529201311.png]]

Ao inspecionar uma PDU, precisamos manter a ideia de encapsulamento em mente. À medida que nossos dados descem na pilha de protocolos, cada camada envolverá os dados das camadas anteriores em uma nova bolha que chamamos de encapsulamento. Essa bolha adiciona as informações necessárias dessa camada no cabeçalho da PDU. Essas informações podem variar de acordo com o nível, mas incluem o que é mantido pela camada anterior, sinalizadores operacionais, quaisquer opções necessárias para negociar comunicações, endereços IP de origem e destino, portas, transporte e protocolos da camada de aplicação.

![[Pasted image 20230529201353.png]]

A imagem acima nos mostra a composição de uma PDU lado a lado com uma quebra de pacote do painel Packet Details do Wireshark. Observe que, quando vemos a fuga no Wireshark, é na ordem inversa. O Wireshark nos mostra o PDU ao contrário porque está na ordem em que foi desencapsulado.

# Mecanismos de Endereçamento

Agora que examinamos os conceitos básicos que orientam o comportamento da rede, vamos dedicar algum tempo para discutir os mecanismos de endereçamento que permitem a entrega de nossos pacotes aos hosts corretos. Começaremos com endereços de controle de acesso de mídia primeiro.

## MAC-Addressing

Cada interface lógica ou física anexada a um host possui um endereço Media Access Control (MAC). Este endereço é um endereço de seis octetos de 48 bits representado no formato hexadecimal. Se olharmos para a imagem abaixo, podemos ver um exemplo de um pela seta vermelha.

![[Pasted image 20230529201818.png]]

O endereçamento MAC é utilizado na camada dois (o link de dados ou a camada de link, dependendo de qual modelo você olha) comunicações entre hosts. Isso funciona por meio da comunicação host-to-host dentro de um domínio de broadcast. Se o tráfego da camada dois precisar cruzar uma interface da camada três, essa PDU é enviada para a interface de saída da camada três e é roteada para a rede correta. Na camada dois, parece que a PDU está endereçada à interface do roteador, e o roteador levará em consideração o endereço da camada três ao determinar para onde enviá-lo em seguida. Depois de fazer uma escolha, ele remove o encapsulamento na camada dois e o substitui por novas informações que indicam o próximo endereço físico na rota.

## IP Addressing

O Protocolo de Internet (IP) foi desenvolvido para entregar dados de um host para outro através dos limites da rede. O IP é responsável pelo roteamento de pacotes, o encapsulamento de dados e fragmentação e remontagem de datagramas quando eles alcançam o host de destino. Por natureza, o IP é um protocolo sem conexão que não oferece garantias de que os dados chegarão ao destinatário pretendido. Para a confiabilidade e validação da entrega de dados, o IP conta com protocolos de camada superior, como o TCP. Atualmente, existem duas versões principais do IP. IPv4, que é o padrão dominante atual, e IPv6, que pretende ser o sucessor do IPv4.

### IPv4

O mecanismo de endereçamento mais comum com o qual a maioria está familiarizada é o endereço IP versão 4 (IPv4). O endereçamento IPv4 é o principal método de roteamento de pacotes pelas redes para hosts localizados fora de nossa vizinhança imediata. A imagem abaixo nos mostra um exemplo de endereço IPv4 pela seta verde.

![[Pasted image 20230529203535.png]]

### IPv6

Depois de pouco mais de uma década utilizando o IPv4, foi determinado que havíamos esgotado rapidamente o pool de endereços IP utilizáveis. Com pedaços tão grandes seccionados para uso especial ou endereçamento privado, o mundo rapidamente esgotou o espaço disponível. Para ajudar a resolver esse problema, duas coisas foram feitas. A primeira foi a implementação de máscaras de sub-rede de comprimento variável (VLSM) e roteamento entre domínios sem classe (CIDR). Isso nos permitiu redefinir os endereços IP utilizáveis ​​no formato v4, alterando como os endereços eram atribuídos aos usuários. A segunda foi a criação e desenvolvimento contínuo do IPv6 como sucessor do IPv4. 

O IPv6 nos fornece um espaço de endereço muito maior que pode ser utilizado para qualquer propósito de rede. IPv6 é um endereço de 128 bits e 16 octetos representado no formato hexadecimal. Podemos ver um exemplo de endereço IPv6 encurtado na imagem abaixo pela seta azul.

![[Pasted image 20230529203928.png]]

Juntamente com um espaço de endereçamento muito maior, o IPv6 oferece: Melhor suporte para Multicasting (envio de tráfego de um para muitos) Endereçamento global por dispositivo Segurança dentro do protocolo na forma de IPSec Simplified Packet headers permitem um processamento mais fácil e movem-se de uma conexão para outra sem que um endereço seja reatribuído.

#### IPv6 Addressing Types

|**Type**|**Description**|
|---|---|
|`Unicast`|Addresses for a single interface.|
|`Anycast`|Addresses for multiple interfaces, where only one of them receives the packet.|
|`Multicast`|Addresses for multiple interfaces, where all of them receive the same packet.|
|`Broadcast`|Does not exist and is realized with multicast addresses.|

Ao pensar em cada tipo de endereço, é útil lembrar que o tráfego Unicast é de host para host, enquanto o Multicast é um para muitos e o Anycast é um para muitos em um grupo onde apenas um responderá ao pacote. (pense em balanceamento de carga).

Mesmo com seu estado atual oferecendo muitas vantagens sobre o IPv4, a adoção do IPv6 tem demorado para pegar.

![[Pasted image 20230529204030.png]]

# TCP/UDP, Mecanismos de Transporte

A Camada de Transporte possui vários mecanismos para ajudar a garantir a entrega contínua de dados da origem ao destino. Pense na camada de transporte como um hub de controle. Essa camada direciona como o tráfego será encapsulado e lançado para os protocolos da camada inferior ( IP e MAC ). Assim que os dados chegam ao destinatário pretendido, a camada de Transporte, trabalhando com os protocolos da camada de Rede/Internet, é responsável por remontar os dados encapsulados de volta na ordem correta. Os dois mecanismos usados ​​para realizar essa tarefa são o Controle de Transmissão (TCP) e o Protocolo de Datagrama do Usuário (UDP).

### TCP VS UDP

Tomemos um segundo para examinar esses dois protocolos lado a lado.

|**Characteristic**|**TCP**|**UDP**|
|---|---|---|
|`Transmission`|Connection-oriented|Connectionless. Fire and forget.|
|`Connection Establishment`|TCP uses a three-way handshake to ensure that a connection is established.|UDP does not ensure the destination is listening.|
|`Data Delivery`|Stream-based conversations|packet by packet, the source does not care if the destination is active|
|`Receipt of data`|Sequence and Acknowledgement numbers are utilized to account for data.|UDP does not care.|
|`Speed`|TCP has more overhead and is slower because of its built-in functions.|UDP is fast but unreliable.|

Observando a tabela acima, podemos ver que o TCP e o UDP fornecem dois métodos de transmissão de dados muito diferentes. O TCP é considerado um protocolo mais confiável, pois permite a verificação de erros e o reconhecimento de dados como uma função normal. Por outro lado, o UDP é um protocolo rápido, dispara e esquece melhor utilizado quando nos preocupamos com a velocidade em detrimento da qualidade e validação.

Para colocar isso em perspectiva, o TCP é utilizado ao mover dados que exigem integridade em vez de velocidade. Por exemplo, quando usamos Secure Shell (SSH) para conectar de um host a outro, é aberta uma conexão que permanece ativa enquanto você emite comandos e executa ações. Esta é uma função do TCP, garantindo que nossa conversa com o host distante não seja interrompida. Se for interrompido por algum motivo, o TCP não irá remontar um fragmento parcial de um pacote e enviá-lo para o aplicativo. Podemos evitar erros desta forma. O que aconteceria se emitissemos um comando como sudo passwd user para alterar a senha do usuário em um host remoto e durante a mudança, parte da mensagem cai. Se fosse sobre o UDP, não teríamos como saber o que aconteceu com o resto da mensagem e potencialmente estragar a senha do usuário ou pior. O TCP ajuda a evitar isso reconhecendo cada pacote recebido para garantir que o host de destino tenha adquirido cada pacote antes de montar o comando e enviá-lo ao aplicativo para ação.

Por outro lado, quando exigimos respostas rápidas ou utilizamos aplicativos que exigem velocidade acima da integridade, o UDP é a nossa resposta. Veja a transmissão de um vídeo, por exemplo. O usuário não notará a queda de um ou dois pixels de um streaming de vídeo. Nós nos preocupamos mais em assistir ao vídeo sem que ele pare constantemente para armazenar a próxima peça. Outro exemplo disso seria o DNS. Quando um host solicita uma entrada de registro para inlanefreight.com, o host está procurando uma resposta rápida para continuar o processo que estava executando. A pior coisa que acontece se uma solicitação de DNS é descartada é que ela é reemitida. Nenhum dano, nenhuma falta. O usuário não receberá dados corrompidos por causa dessa queda.

O tráfego UDP aparece como tráfego normal; é um único pacote, sem resposta ou reconhecimento de que foi enviado ou recebido, então não há muito o que mostrar aqui. No entanto, podemos dar uma olhada no TCP e como ele estabelece conexões.

# TCP Three-way Handshake

Uma das maneiras pelas quais o TCP garante a entrega de dados do servidor para o cliente é a utilização de sessões. Essas sessões são estabelecidas por meio do que é chamado de aperto de mão de três vias. Para fazer isso acontecer, o TCP utiliza uma opção no cabeçalho TCP chamada flags. Não vamos nos aprofundar nos sinalizadores TCP agora; saiba que os sinalizadores comuns que veremos em um handshake de três vias são Sincronização (SYN) e confirmação (ACK). Quando um host solicita uma conversa com um servidor por TCP;

1. O cliente envia um pacote com o sinalizador SYN ativado junto com outras opções negociáveis ​​no cabeçalho TCP. Este é um pacote de sincronização. Ele será definido apenas no primeiro pacote do host e do servidor e permite estabelecer uma sessão, permitindo que ambas as extremidades concordem com um número de sequência para iniciar a comunicação. Isso é crucial para o rastreamento de pacotes. Juntamente com a sincronização do número de sequência, muitas outras opções são negociadas nesta fase para incluir tamanho da janela, tamanho máximo do segmento e confirmações seletivas.
2. O servidor responderá com um pacote TCP que inclui um sinalizador SYN definido para a negociação do número de sequência e um sinalizador ACK definido para reconhecer o pacote SYN anterior enviado pelo host. O servidor também incluirá quaisquer alterações nas opções de TCP necessárias definidas nos campos de opções do cabeçalho TCP.
3. O cliente responderá com um pacote TCP com um sinalizador ACK definido concordando com a negociação. Este pacote é o fim do handshake triplo e estabelece a conexão entre cliente e servidor.

Vamos dar uma olhada rápida nisso em ação para estarmos familiarizados com ele quando aparecer em nossa saída de pacote mais tarde no módulo.

![[Pasted image 20230529211247.png]]

Ao examinar essa saída, podemos ver o início do nosso aperto de mão na linha um. Observando as informações destacadas na caixa vermelha, podemos ver que nosso sinalizador Syn inicial está definido. Se olharmos para os números de porta sublinhados em verde, podemos ver dois números, 57678 e 80. O primeiro número é o número alto aleatório da porta em uso pelo cliente, e a segunda é a porta bem conhecida para HTTP usada pelo servidor para escutar as conexões de solicitação da Web recebidas. Na linha 2, podemos ver a resposta do servidor ao cliente com um pacote SYN/ACK enviado para as mesmas portas. Na linha 3, podemos ver o cliente reconhecer o pacote de sincronização do servidor para estabelecer a conexão.

O pacote 4 nos mostra que a solicitação HTTP foi enviada e uma sessão é estabelecida para transmitir os dados da imagem solicitada. Podemos ver enquanto o fluxo continua que o TCP envia confirmações para cada bloco de dados enviado. Este é um exemplo de comunicação TCP típica.

Vimos como uma sessão é estabelecida com TCP; agora, vamos examinar como uma sessão é concluída.

![[Pasted image 20230529211340.png]]

Na imagem acima, um conjunto de pacotes semelhante ao nosso aperto de mão de três vias visível no final da saída. É assim que o TCP fecha as conexões normalmente. Outro sinalizador que veremos com o TCP é o sinalizador FIN. É usado para sinalizar que a transferência de dados foi concluída e o remetente está solicitando o término da conexão. O cliente confirma o recebimento dos dados e então envia um FIN e ACK para iniciar o término da sessão. O servidor responde com uma confirmação do FIN e envia de volta seu próprio FIN. Por fim, o cliente confirma que a sessão foi concluída e fecha a conexão. Antes do término da sessão, devemos ver um padrão de pacote de:

1. `FIN, ACK`
2. `FIN, ACK`,
3. `ACK`

Se olharmos para a imagem acima detalhando uma sessão, veremos que é esse o caso. Uma saída semelhante a esta é considerada uma conexão adequadamente terminada.