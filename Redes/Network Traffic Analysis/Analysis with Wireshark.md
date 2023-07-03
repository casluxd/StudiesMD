
O Wireshark é um analisador de tráfego de rede gratuito e de código aberto, muito parecido com o tcpdump, mas com uma interface gráfica. O Wireshark é multiplataforma e capaz de capturar dados ao vivo de vários tipos de interface diferentes (incluindo WiFi, USB e Bluetooth) e salvar o tráfego em vários formatos diferentes. O Wireshark permite que o usuário mergulhe muito mais fundo na inspeção de pacotes de rede do que outras ferramentas. O que torna o Wireshark verdadeiramente poderoso é a capacidade de análise que ele fornece, fornecendo uma visão detalhada do tráfego.

Dependendo do host que estamos usando, nem sempre temos uma GUI para utilizar o Wireshark tradicional. Para nossa sorte, várias variantes nos permitem usá-lo na linha de comando.

Features e capacidades:
- Inspeção profunda de pacotes para centenas de protocolos diferentes
- Interfaces gráficas e TTY
- Capaz de rodar na maioria dos sistemas operacionais
- Ethernet, IEEE 802.11, PPP/HDLC, ATM, Bluetooth, USB, Token Ring, Frame Relay, FDDI, entre outros
- Recursos de descriptografia para IPsec, ISAKMP, Kerberos, SNMPv3, SSL/TLS, WEP e WPA/WPA2
- E muito muito mais...

# TShark vs Wireshark (Terminal vs GUI)

Ambas as opções têm seus méritos. O TShark é uma ferramenta de terminal desenvolvida especificamente para o Wireshark. O TShark compartilha muitos dos mesmos recursos incluídos no Wireshark e até compartilha a sintaxe e as opções. O TShark é perfeito para uso em máquinas com pouco ou nenhum ambiente de trabalho e pode facilmente passar as informações de captura que recebe para outra ferramenta via linha de comando. O Wireshark é a opção de GUI rica em recursos para captura e análise de tráfego. Se você deseja ter uma experiência completa e trabalhar em uma máquina com um ambiente de desktop, a GUI do Wireshark é o caminho a percorrer.

### Switches básicos do TShark

|**Switch Command**|**Result**|
|:-:|---|
|D|Will display any interfaces available to capture from and then exit out.|
|L|Will list the Link-layer mediums you can capture from and then exit out. (ethernet as an example)|
|i|choose an interface to capture from. (-i eth0)|
|f|packet filter in libpcap syntax. Used during capture.|
|c|Grab a specific number of packets, then quit the program. Defines a stop condition.|
|a|Defines an autostop condition. Can be after a duration, specific file size, or after a certain number of packets.|
|r (pcap-file)|Read from a file.|
|W (pcap-file)|Write into a file using the pcapng format.|
|P|Will print the packet summary while writing into a file (-W)|
|x|will add Hex and ASCII output into the capture.|
|h|See the help menu|

### Uso básico do TShark

O TShark pode usar filtros para protocolos, itens comuns como hosts e portas, e até mesmo a capacidade de se aprofundar nos pacotes e dissecar campos individuais do pacote. 

Com a string básica na linha de comando acima, utilizamos TShark para capturar em en0, especificado com o sinalizador -i e a opção -w para salvar a captura em um arquivo de saída especificado. A utilização do TShark é muito semelhante ao TCPDump nos filtros e switches que podemos usar. Ambas as ferramentas utilizam a sintaxe BPF. Para ler a captura, o tshark pode receber a opção -r como no TCPDump, ou podemos passar a opção -P para que o tshark imprima os resumos do pacote enquanto grava em um arquivo. Abaixo está um exemplo de leitura do arquivo PCAP que capturamos anteriormente.

```shell-session
casluxd@htb[/htb]$ sudo tshark -i eth0 -w /tmp/test.pcap
```

```shell-session
casluxd@htb[/htb]$ sudo tshark -i eth0 -f "host 172.16.146.2"

Capturing on 'eth0'
    1 0.000000000 172.16.146.2 → 172.16.146.1 DNS 70 Standard query 0x0804 A github.com
    2 0.258861645 172.16.146.1 → 172.16.146.2 DNS 86 Standard query response 0x0804 A github.com A 140.82.113.4
    3 0.259866711 172.16.146.2 → 140.82.113.4 TCP 74 48256 → 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM=1 TSval=1321417850 TSecr=0 WS=128
    4 0.299681376 140.82.113.4 → 172.16.146.2 TCP 74 443 → 48256 [SYN, ACK] Seq=0 Ack=1 Win=65535 Len=0 MSS=1436 SACK_PERM=1 TSval=3885991869 TSecr=1321417850 WS=1024
    5 0.299771728 172.16.146.2 → 140.82.113.4 TCP 66 48256 → 443 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=1321417889 TSecr=3885991869
    6 0.306888828 172.16.146.2 → 140.82.113.4 TLSv1 579 Client Hello
    7 0.347570701 140.82.113.4 → 172.16.146.2 TLSv1.3 2785 Server Hello, Change Cipher Spec, Application Data, Application Data, Application Data, Application Data
    8 0.347653593 172.16.146.2 → 140.82.113.4 TCP 66 48256 → 443 [ACK] Seq=514 Ack=2720 Win=63488 Len=0 TSval=1321417937 TSecr=3885991916
    9 0.358887130 172.16.146.2 → 140.82.113.4 TLSv1.3 130 Change Cipher Spec, Application Data
   10 0.359781588 172.16.146.2 → 140.82.113.4 TLSv1.3 236 Application Data
   11 0.360037927 172.16.146.2 → 140.82.113.4 TLSv1.3 758 Application Data
   12 0.360482668 172.16.146.2 → 140.82.113.4 TLSv1.3 258 Application Data
   13 0.397331368 140.82.113.4 → 172.16.146.2 TLSv1.3 145 Application Data 
```

-f nos permite aplicar filtros à captura. No exemplo, utilizamos o host, mas você pode usar praticamente qualquer filtro que o Wireshark reconheça. Nós tocamos um pouco no TShark agora. Vamos dar uma olhada em uma ferramenta bacana chamada Termshark.

## Termshark

O Termshark é um aplicativo de interface de usuário baseado em texto (TUI) que fornece ao usuário uma interface semelhante ao Wireshark diretamente na janela do terminal.

![[Pasted image 20230530200642.png]]

O Termshark pode ser encontrado em Termshark. Ele pode ser criado a partir da fonte clonando o repositório ou baixando uma das versões estáveis ​​atuais de https://github.com/gcla/termshark/releases , extraia o arquivo e comece a trabalhar.

Para obter ajuda para navegar nesta TUI, consulte a imagem abaixo.

![[Pasted image 20230530200657.png]]

Para iniciar o Termshark, emita as mesmas strings, como TShark ou tcpdump. Podemos especificar uma interface para capturar, filtros e outras configurações do terminal. A janela do Termshark não abrirá até que detecte o tráfego em seu filtro de captura. Então espere um segundo se nada acontecer.

# Wireshark GUI

Agora que passamos algum tempo aprendendo a arte da captura de pacotes a partir da linha de comando, vamos passar algum tempo no Wireshark. Levaremos alguns minutos para examinar o que estamos vendo na saída abaixo. Vamos dissecar esta visão da GUI do Wireshark.

![[Pasted image 20230530200725.png]]

Há três painéis principais:

1. Lista de pacotes: laranja

Nesta janela, vemos uma linha de resumo de cada pacote que inclui os campos listados abaixo por padrão. Podemos adicionar ou remover colunas para alterar quais informações são apresentadas.
- No: Ordem em que o pacote chegou no Wireshark
- Time: formato de hora Unix
- Source: IP de origem
- Destination: IP de destino
- Protocol: O protocolo usado (TCP, UDP, DNS, etc.)
- Information: Informações sobre o pacote. Este campo pode variar de acordo com o tipo de protocolo usado. Ele mostrará, por exemplo, que tipo de consulta é para um pacote DNS.

2. Detalhes do pacote: Azul

A janela Packet Details nos permite detalhar o pacote para inspecionar os protocolos com mais detalhes. Ele o dividirá em partes que esperaríamos seguir a referência típica do modelo OSI. o pacote é dividido em diferentes camadas de encapsulamento para inspeção.

Lembre-se de que o Wireshark mostrará esse encapsulamento na ordem inversa, com o encapsulamento da camada inferior na parte superior da janela e os níveis superiores na parte inferior.

3. Bytes do pacote: Verde

A janela Packet Bytes nos permite ver o conteúdo do pacote em ASCII ou saída hexadecimal. À medida que selecionamos um campo nas janelas acima, ele será destacado na janela Packet Bytes e nos mostrará onde esse bit ou byte se enquadra no pacote geral.
Essa é uma ótima maneira de validar se o que vemos no painel Detalhes é preciso e se a interpretação feita pelo Wireshark corresponde à saída do pacote.
Cada linha na saída contém o deslocamento de dados, dezesseis bytes hexadecimais e dezesseis bytes ASCII. Os bytes não imprimíveis são substituídos por um ponto no formato ASCII.

### Outras features notáveis

Ao olhar para a interface do Wireshark, notamos algumas áreas de opções diferentes e botões radiais. Essas áreas são pontos de controle nos quais podemos modificar a interface e nossa visão dos pacotes na captura atual. Veja a figura abaixo
![[Pasted image 20230530201307.png]]

## Realizando nossa primeira captura no Wireshark

![image](https://academy.hackthebox.com/storage/modules/81/first-capture-ws.gif)

Lembre-se de que, sempre que alterarmos as opções de captura, o Wireshark reiniciará o rastreamento. Assim como o TCPDump, o Wireshark possui opções de filtro de captura e exibição que podem ser usadas.

## O básico

### Toolbar

![[Pasted image 20230530201409.png]]

A barra de ferramentas do Wireshark é um ponto central para gerenciar os muitos recursos que o Wireshark inclui. A partir daqui podemos iniciar e parar capturas, alterar interfaces, abrir e salvar arquivos .pcap e aplicar diferentes filtros ou add-ins de análise.

### Como salvar uma captura

Digamos que precisamos capturar o que temos em nossa janela atualmente para solucionar problemas posteriormente. Salvar uma captura é super simples:

- Select File ⇢ save OR
- Na barra de ferramentas, selecione a opção de arquivo e escolha onde salvar o arquivo e em qual formato.

Esteja ciente de que o Wireshark pode salvar capturas em vários formatos. Escolha o necessário para o cenário, mas usaremos o formato .pcap por enquanto.

# Processamento e filtragem pré-captura e pós-captura

Ao capturar o tráfego com o Wireshark, temos várias opções sobre como e quando filtramos o tráfego. Isso é feito utilizando filtros de captura e exibição. O primeiro iniciado antes do início da captura e o último durante ou após a conclusão da captura. Embora o Wireshark tenha várias funcionalidades integradas úteis, vale a pena mencionar que ele tem um pouco de dificuldade em lidar com grandes capturas. Quanto mais pacotes capturados, mais tempo levará para o Wireshark executar o filtro de exibição ou análise contra ele. Pode levar de apenas alguns segundos a alguns minutos, se for concluído. Se estivermos trabalhando com um arquivo pcap grande, talvez seja melhor dividi-lo em pedaços menores primeiro.

### Filtros de captura

Filtros de captura - são inseridos antes que a captura seja iniciada. Eles usam a sintaxe BPF como o host 214.15.2.30 da mesma forma que o TCPDump. Eles usam a sintaxe BPF como o host 214.15.2.30 da mesma forma que o TCPDump. Temos menos opções de filtro dessa maneira, e um filtro de captura eliminará todo o outro tráfego que não atenda explicitamente aos critérios definidos. Essa é uma ótima maneira de reduzir os dados que você grava no disco ao solucionar problemas de uma conexão, como capturar as conversas entre dois hosts.

Aqui está uma tabela de filtros de captura comuns e úteis com uma descrição de cada um:

|**Capture Filters**|**Result**|
|:-:|---|
|host x.x.x.x|Capture only traffic pertaining to a certain host|
|net x.x.x.x/24|Capture traffic to or from a specific network (using slash notation to specify the mask)|
|src/dst net x.x.x.x/24|Using src or dst net will only capture traffic sourcing from the specified network or destined to the target network|
|port #|will filter out all traffic except the port you specify|
|not port #|will capture everything except the port specified|
|port # and #|AND will concatenate your specified ports|
|portrange x-x|portrange will grab traffic from all ports within the range only|
|ip / ether / tcp|These filters will only grab traffic from specified protocol headers.|
|broadcast / multicast / unicast|Grabs a specific type of traffic. one to one, one to many, or one to all.|

## Aplicando filtros de captura

Antes de aplicarmos um filtro de captura, vamos dar uma olhada nos filtros embutidos. Para fazer isso: Clique no radial de captura na parte superior da janela do Wireshark → então selecione os filtros de captura no menu suspenso.

![[Pasted image 20230530201659.png]]

A partir daqui, podemos modificar os filtros existentes ou adicionar os nossos próprios.
Para aplicar o filtro a uma captura, iremos: Clique no radial de captura na parte superior da janela do Wireshark → então selecione Opções no menu suspenso → na nova janela, selecione o menu suspenso para Filtro de captura para interfaces selecionadas ou digite o filtro que desejamos usar. 

![[Pasted image 20230530201738.png]]

### Filtros de exibição

Filtros de exibição - são usados ​​durante a execução da captura e depois que ela é interrompida. Os filtros de exibição são proprietários do Wireshark, que oferece muitas opções diferentes para quase todos os protocolos.

Aqui está uma tabela de filtros de exibição comuns e úteis com uma descrição de cada um:

|**Display Filters**|**Result**|
|:-:|---|
|ip.addr == x.x.x.x|Capture only traffic pertaining to a certain host. This is an OR statement.|
|ip.addr == x.x.x.x/24|Capture traffic pertaining to a specific network. This is an OR statement.|
|ip.src/dst == x.x.x.x|Capture traffic to or from a specific host|
|dns / tcp / ftp / arp / ip|filter traffic by a specific protocol. There are many more options.|
|tcp.port == x|filter by a specific tcp port.|
|tcp.port / udp.port != x|will capture everything except the port specified|
|and / or / not|AND will concatenate, OR will find either of two options, NOT will exclude your input option.|

`Lembre-se de que, ao utilizar os filtros de exibição, o tráfego é processado para mostrar apenas o que é solicitado, mas o restante do arquivo de captura não será substituído. Aplicar filtros de exibição e opções de análise fará com que o Wireshark reprocesse os dados pcap para aplicar.`

### Aplicando filtros de exibição

Aplicar um filtro de exibição é ainda mais fácil do que um filtro de captura. Na janela principal de captura do Wireshark, tudo o que precisamos fazer é: selecionar o marcador na barra de ferramentas → e, em seguida, selecionar uma opção no menu suspenso. Alternativamente, coloque o cursor no radial de texto → e digite o filtro que desejamos usar. Se o campo ficar verde, o filtro está correto. 

![[Pasted image 20230530201902.png]]

Ao usar filtros de captura e exibição, lembre-se de que o que especificamos é considerado literal. Por exemplo, filtrar o tráfego da porta 80 não é o mesmo que filtrar para HTTP. Pense em portas e protocolos mais como diretrizes em vez de regras rígidas. As portas podem ser vinculadas e usadas para finalidades diferentes daquelas para as quais foram originalmente planejadas. Por exemplo, a filtragem de HTTP procurará marcadores de chave que o protocolo usa, como solicitações GET/POST, e mostrará os resultados deles. A filtragem da porta 80 mostrará qualquer coisa enviada ou recebida nessa porta, independentemente do protocolo de transporte.

