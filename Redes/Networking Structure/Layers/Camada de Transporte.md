
A camada de transporte é a quarta camada do modelo OSI (ou do modelo TCP/IP) e está localizada acima da camada de rede. Sua principal função é garantir uma comunicação confiável, eficiente e ordenada entre processos de aplicação que estão sendo executados em dispositivos finais (por exemplo, computadores).

A camada de transporte oferece serviços de comunicação ponto a ponto, ou seja, ela estabelece uma conexão entre um remetente e um destinatário para a transmissão de dados. Ela segmenta os dados recebidos da camada de aplicação em unidades menores chamadas segmentos (ou datagramas, no caso do UDP) e adiciona informações de controle para garantir que os dados sejam transmitidos corretamente.

Existem dois principais protocolos de transporte amplamente utilizados: TCP (Transmission Control Protocol) e UDP (User Datagram Protocol).

O TCP é um protocolo orientado à conexão, o que significa que ele estabelece uma conexão confiável e garante a entrega ordenada dos dados. Ele utiliza técnicas como controle de fluxo, controle de congestionamento, retransmissão de pacotes e confirmação de recebimento para garantir a entrega confiável dos dados. O TCP é amplamente utilizado em aplicações que requerem entrega precisa e confiável dos dados, como transferência de arquivos, navegação na web e envio de e-mails.

Por outro lado, o UDP é um protocolo sem conexão, o que significa que ele não estabelece uma conexão prévia antes de enviar os dados. O UDP é mais simples e leve em termos de recursos de rede, pois não possui os mecanismos de controle de fluxo e confiabilidade do TCP. Ele é utilizado em aplicações que exigem transmissão rápida e eficiente de dados, como streaming de áudio e vídeo, jogos online e transmissões em tempo real.

Além da segmentação e entrega de dados, a camada de transporte também é responsável pela multiplexação e demultiplexação dos dados. Isso significa que ela permite que vários processos de aplicação se comuniquem simultaneamente usando portas de origem e destino. A camada de transporte identifica os processos de aplicação com base nas portas e encaminha os dados corretamente para o processo de destino no dispositivo final.

Em resumo, a camada de transporte é responsável por estabelecer uma conexão confiável e eficiente entre processos de aplicação em dispositivos finais. Ela segmenta, entrega e controla a transmissão dos dados, garantindo a confiabilidade (no caso do TCP) ou a transmissão rápida e eficiente (no caso do UDP). A camada de transporte desempenha um papel fundamental na comunicação fim a fim na rede.

# TCP

  
O TCP (Transmission Control Protocol) é um protocolo de transporte confiável amplamente utilizado em redes de computadores. Ele utiliza uma série de flags (sinais) em seu cabeçalho para controlar diferentes aspectos da comunicação. Aqui estão as principais flags TCP e sua função:

1. SYN (Synchronize): A flag SYN é utilizada durante o processo de estabelecimento de uma conexão TCP. Quando um host deseja iniciar uma comunicação TCP, ele envia um segmento com a flag SYN ativada para solicitar a abertura de uma conexão. Essa flag é usada tanto pelo lado cliente quanto pelo lado servidor para sincronizar seus números de sequência iniciais.
2. ACK (Acknowledgment): A flag ACK é usada para confirmar o recebimento de dados. Quando essa flag está ativada, indica que o campo "Número de Reconhecimento" (Acknowledgment Number) no cabeçalho TCP contém o próximo número de sequência esperado pelo receptor. Ela é usada para confirmar a recepção de dados enviados anteriormente.
3. PSH (Push): A flag PSH é usada para indicar ao receptor que os dados devem ser entregues à camada de aplicação assim que chegarem, sem esperar por um buffer cheio. Ela é útil quando há uma necessidade de transmitir dados em tempo real ou quando é importante minimizar o atraso na entrega.
4. RST (Reset): A flag RST é usada para reiniciar abruptamente uma conexão TCP. Quando essa flag é ativada, indica que a conexão deve ser encerrada imediatamente devido a algum problema grave, como uma falha na comunicação ou um erro fatal.
5. SYN/ACK (Synchronize/Acknowledgment): Essa combinação de flags é usada durante o processo de estabelecimento de conexão TCP. Quando um host recebe um segmento com a flag SYN ativada, ele responde com um segmento que tem tanto as flags SYN quanto ACK ativadas para confirmar o recebimento e sincronizar seus números de sequência.
6. FIN (Finish): A flag FIN é usada para indicar o encerramento de uma conexão TCP. Quando essa flag é ativada, indica que o remetente não tem mais dados para enviar e deseja encerrar a conexão. O receptor pode responder com a flag ACK para confirmar o encerramento.

Essas flags TCP são usadas em diferentes fases da comunicação TCP, como estabelecimento de conexão, transmissão de dados e encerramento de conexão. Elas desempenham um papel crucial na garantia da confiabilidade e controle da comunicação TCP.