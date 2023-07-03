
O Transmission Control Protocol (TCP) e o User Datagram Protocol (UDP) são protocolos usados ​​na transmissão de informações e dados na Internet. Normalmente, as conexões TCP transmitem dados importantes, como páginas da Web e e-mails. Por outro lado, as conexões UDP transmitem dados em tempo real, como streaming de vídeo ou jogos online. 

TCP é um protocolo orientado a conexão que garante que todos os dados enviados de um computador para outro sejam recebidos. É como uma conversa telefônica em que ambas as partes permanecem conectadas até que a chamada seja encerrada. Se ocorrer um erro durante o envio de dados, o destinatário envia uma mensagem de volta para que o remetente possa reenviar os dados ausentes. Isso torna o TCP confiável e mais lento que o UDP porque é necessário mais tempo para transmissão e recuperação de erros.

O UDP, por outro lado, é um protocolo sem conexão. É usado quando a velocidade é mais importante do que a confiabilidade, como para streaming de vídeo ou jogos online. Com o UDP, não há verificação de que os dados recebidos estejam completos e sem erros. Se ocorrer um erro durante o envio de dados, o destinatário não receberá esses dados ausentes e nenhuma mensagem será enviada de volta para reenviá-los. Alguns dados podem ser perdidos com UDP, mas a transmissão geral é mais rápida.

# IP Packet

Um pacote de Protocolo de Internet (IP) é a área de dados usada pela camada de rede do modelo OSI (Open Systems Interconnection) para transmitir dados de um computador para outro. Consiste em um cabeçalho e a carga útil, os dados reais da carga útil.

Também podemos pensar no pacote IP como uma carta enviada em um envelope. O envelope contém o cabeçalho, que inclui informações sobre o remetente e o destinatário

O cabeçalho de um pacote IP contém vários campos que contêm informações importantes.

|**Campo**|**Descrição**|
|---|---|
|`Version`|Indica qual versão do protocolo IP está sendo usada|
|`Internet Header Length`|Indica o tamanho do cabeçalho em palavras de 32 bits|
|`Class of Service`|Significa o quão importante é a transmissão dos dados|
|`Total length`|Especifica o comprimento total do pacote em bytes|
|`Identification (ID)`|É usado para identificar fragmentos do pacote quando fragmentado em partes menores|
|`Flags`|Usado para indicar fragmentação|
|`Fragment Offset`|Indica onde o fragmento atual é colocado no pacote|
|`Time to Live`|Especifica quanto tempo o pacote pode permanecer na rede|
|`Protocol`|Especifica qual protocolo é usado para transmitir os dados, como TCP ou UDP|
|`Checksum`|É usado para detectar erros no cabeçalho|
|`Source/Destination`|Indique de onde o pacote foi enviado e para onde está sendo enviado|
|`Options`|Contêm informações opcionais para roteamento|
|`Padding`|Preenche o pacote para um comprimento de palavra completo|

Podemos ver um computador com vários endereços IP em diferentes redes. Aqui devemos prestar atenção ao campo IP ID. É usado para identificar fragmentos de um pacote IP quando fragmentado em partes menores. É um campo de 16 bits com um número exclusivo que varia de 0 a 65535.

Se um computador tiver vários endereços IP, o campo IP ID será diferente para cada pacote enviado do computador, mas muito semelhante. No TCPdump, o tráfego de rede pode se parecer com isto:

```shell-session
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1337
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1338
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1339
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1340
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1341
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1342
```

Podemos ver na saída que dois endereços IP diferentes estão enviando pacotes para o endereço IP 10.129.1.1. No entanto, pelo ID do IP, podemos ver que os pacotes são contínuos. Isso indica fortemente que os dois endereços IP pertencem ao mesmo host na rede.

# IP Record-Route Field

O campo Record-Route no cabeçalho IP também registra a rota para um dispositivo de destino. Quando o dispositivo de destino envia de volta o pacote ICMP Echo Reply, os endereços IP de todos os dispositivos que passam pelo pacote são listados no campo Record-Route do cabeçalho IP. Isso acontece quando utilizamos o seguinte comando, por exemplo:

```shell-session
casluxd@htb[/htb]$ ping -c 1 -R 10.129.143.158

PING 10.129.143.158 (10.129.143.158) 56(124) bytes of data.
64 bytes from 10.129.143.158: icmp_seq=1 ttl=63 time=11.7 ms
RR: 10.10.14.38
        10.129.0.1
        10.129.143.158
        10.129.143.158
        10.10.14.1
        10.10.14.38


--- 10.129.143.158 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 11.688/11.688/11.688/0.000 ms
```

A saída indica que uma solicitação de ping foi enviada e uma resposta foi recebida do dispositivo de destino e também mostra o campo Record-Route no cabeçalho IP do pacote ICMP Echo Request. O campo Record Route contém os endereços IP de todos os dispositivos que passaram pelo pacote ICMP Echo Request a caminho do dispositivo de destino. Nesse caso, o campo Record-Route contém os endereços IP: 10.10.14.38, 10.129.143.158, 10.129.0.1, 10.10.14.1, 10.129.143.158, 10.10.14.38

A ferramenta traceroute também pode ser usada para rastrear a rota até um destino com mais precisão, que usa o método de tempo limite TCP para determinar quando a rota foi totalmente rastreada.

1.  Enviamos um pacote TCP SYN para o dispositivo de destino com um TTL inicial alto no cabeçalho IP. Quando o pacote TCP SYN atinge um roteador cujo TTL é maior que 1, o valor do TTL é diminuído em 1 e o pacote é encaminhado para o próximo dispositivo. Se o pacote TCP SYN chegar a um roteador cujo TTL é 1, o pacote será descartado e o roteador enviará um pacote ICMP Time-Exceeded de volta para nós.
2. Recebemos o pacote ICMP Time-Exceeded e anotamos o endereço IP do roteador que enviou o pacote.
3. Depois disso, enviamos outro pacote TCP SYN para o dispositivo de destino com um TTL menor.

O processo se repete até que o pacote TCP SYN alcance o host de destino e receba uma resposta TCP SYN/ACK ou TCP RST do destino. Assim que recebemos uma resposta do dispositivo de destino, sabemos que traçamos a rota até o destino e finalizamos o processo de traceroute.

## IP Payload

A carga útil (também chamada de dados IP) é a carga útil real do pacote. Ele contém os dados de vários protocolos, como TCP ou UDP, que estão sendo transmitidos, assim como o conteúdo da carta no envelope.

## TCP

Os pacotes TCP, também conhecidos como segmentos, são divididos em várias seções chamadas cabeçalhos e cargas úteis. Os segmentos TCP são agrupados no pacote IP enviado. 

O cabeçalho contém vários campos que contêm informações importantes. A source port indica o computador de onde o pacote foi enviado. A destination port indica para qual computador o pacote é enviado. O sequence number indica a ordem em que os dados foram enviados. O confirmation number é usado para confirmar que todos os dados foram recebidos com sucesso. Os control flags indicam se o pacote marca o fim de uma mensagem, se é uma confirmação de que os dados foram recebidos ou se contém uma solicitação para repetir os dados. O Window Size indica quantos dados o receptor pode receber. O checksum é usado para detectar erros no cabeçalho e na carga útil. O Urgent Pointer alerta o receptor de que dados importantes estão na carga útil.

A carga útil é a carga real do pacote e contém os dados que estão sendo transmitidos, assim como o conteúdo de uma conversa entre duas pessoas.

## UDP

O UDP transfere datagramas (pequenos pacotes de dados) entre dois hosts. É um protocolo sem conexão, ou seja, não precisa estabelecer uma conexão entre o remetente e o destinatário antes de enviar os dados. Em vez disso, os dados são enviados diretamente para o host de destino sem nenhuma conexão anterior.

Quando o traceroute é usado com UDP, receberemos uma mensagem Destination Unreachable e Port Unreachable quando o pacote de datagrama UDP atingir o dispositivo de destino. Geralmente, os pacotes UDP são enviados usando traceroute em hosts Unix.

# Blind Spoofing

Blind spoofing é um método de ataque de manipulação de dados no qual um invasor envia informações falsas em uma rede sem ver as respostas reais enviadas pelos dispositivos de destino. Envolve a manipulação do campo do cabeçalho IP para indicar endereços falsos de origem e destino. Por exemplo, enviamos um pacote TCP para o host de destino com números de porta de origem e destino falsos e um ISN (Initial Sequence Number) falso. O ISN é um campo no cabeçalho TCP usado para especificar o número de sequência do primeiro pacote TCP em uma conexão. O ISN é definido pelo remetente de um pacote TCP e enviado ao destinatário no cabeçalho TCP do primeiro pacote. Isso pode fazer com que o host de destino estabeleça uma conexão conosco sem receber a conexão.

Esse ataque é comumente usado para interromper a integridade das conexões de rede ou interromper conexões entre dispositivos de rede. Também pode ser usado para monitorar o tráfego de rede ou para interceptar informações enviadas por dispositivos de rede.