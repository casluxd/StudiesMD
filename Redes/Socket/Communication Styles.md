A GNU C Library inclui suporte para vários tipos de sockets, cada um com características diferentes. Esta seção descreve os tipos de sockets suportados. As constantes simbólicas listadas aqui são definidas em `sys/socket.h`.

### int SOCK_STREAM

O estilo SOCK_STREAM é como um pipe. Ele opera em uma conexão com um socket remoto específico e transmite dados de forma confiável como um fluxo de bytes.

### int SOCK_DGRAM

O estilo SOCK_DGRAM é usado para enviar pacotes endereçados individualmente de forma não confiável. É o oposto diametral de SOCK_STREAM. 

Cada vez que você grava dados em um socket desse tipo, esses dados se tornam um pacote. Como os sockets SOCK_DGRAM não possuem conexões, você deve especificar o endereço do destinatário em cada pacote.

A única garantia que o sistema dá sobre suas solicitações de dados é que ele fará o possível para entregar cada pacote que você enviar. Por ter sucesso com o sexto pacote depois de falhar com o quarto e o quinto pacotes; o sétimo pacote pode chegar antes do sexto e pode chegar uma segunda vez depois do sexto.

O uso típico de SOCK_DGRAM é em situações em que é aceitável simplesmente reenviar um pacote se nenhuma resposta for vista em um período de tempo razoável.

### int SOCK_RAW

Esse estilo fornece acesso a interfaces e protocolos de rede de baixo nível. Programas de usuário comuns geralmente não precisam user esse estilo.