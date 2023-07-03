
## Shell

Shell é um termo muito comum que ouviremos repetidamente durante nossa jornada. Tem alguns significados. Em um sistema Linux, o shell é um programa que recebe informações do usuário por meio do teclado e passa esses comandos ao sistema operacional para executar uma função específica. Nos primórdios da computação, o shell era a única interface disponível para interagir com os sistemas. Desde então, muitos outros tipos e versões de sistemas operacionais surgiram junto com a interface gráfica do usuário (GUI) para complementar as interfaces de linha de comando (shell), como o terminal Linux, a linha de comando do Windows (cmd.exe) e o Windows PowerShell.

A maioria dos sistemas Linux usa um programa chamado [Bash (Bourne Again Shell)](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html) como um programa shell para interagir com o sistema operacional. O Bash é uma versão aprimorada do [sh](https://man7.org/linux/man-pages/man1/sh.1p.html) , o programa shell original dos sistemas Unix. Além disso, `bash` também existem outras shells, incluindo, entre outras, [Zsh](https://en.wikipedia.org/wiki/Z_shell) , [Tcsh](https://en.wikipedia.org/wiki/Tcsh) , [Ksh](https://en.wikipedia.org/wiki/KornShell) , [Fish shell](https://en.wikipedia.org/wiki/Fish_(Unix_shell)) , etc.

Freqüentemente, lemos ou ouvimos outras pessoas falando sobre "obter uma shell" em uma caixa (sistema). Isso significa que o host de destino foi explorado e obtivemos acesso em nível de shell (normalmente `bash` ou `sh`) e podemos executar comandos interativamente como se estivéssemos conectados ao host. Um shell pode ser obtido explorando um aplicativo da web ou vulnerabilidade de rede/serviço ou obtendo credenciais e fazendo login no host de destino remotamente. Existem três tipos principais de conexões shell:

- Reverse Shell: Inicia uma conexão de volta para um "ouvinte" em nossa caixa de ataque.
- Bind Shell: "Vinculado" a uma porta específica no host de destino e aguarda uma conexão de nossa caixa de ataque.
- Web shell: Executa comandos do sistema operacional por meio do navegador da Web, normalmente não interativo ou semi-interativo. Ele também pode ser usado para executar comandos únicos (ou seja, aproveitar uma vulnerabilidade de upload de arquivo e fazer upload de um script `PHP` para executar um único comando.

Cada tipo de shell tem seu caso de uso, e da mesma forma que existem várias formas de obter um shell, o programa auxiliar que usamos para obter um shell pode ser escrito em várias linguagens (`Python`, `Perl`, `Go`, `Bash`, `Java`, `awk`, `PHP`, etc). Podem ser pequenos scripts ou programas maiores e mais complexos para facilitar uma conexão do host de destino de volta ao nosso sistema de ataque e obter acesso "shell". O acesso ao shell será discutido em profundidade em uma seção posterior.

## Porta

Uma [porta](https://en.wikipedia.org/wiki/Port_(computer_networking)) pode ser considerada como uma janela ou porta em uma casa (sendo a casa um sistema remoto), se uma janela ou porta for deixada aberta ou não trancada corretamente, muitas vezes podemos obter acesso não autorizado a uma casa. Isso é semelhante na computação. As portas são pontos virtuais onde as conexões de rede começam e terminam. Eles são baseados em software e gerenciados pelo sistema operacional host. As portas estão associadas a um processo ou serviço específico e permitem que os computadores diferenciem entre diferentes tipos de tráfego (o tráfego SSH flui para uma porta diferente das solicitações da web para acessar um site, mesmo que as solicitações de acesso sejam enviadas pela mesma conexão de rede).

Cada porta recebe um número e muitas são padronizadas em todos os dispositivos conectados à rede (embora um serviço possa ser configurado para ser executado em uma porta não padrão). Por exemplo, `HTTP` as mensagens (tráfego do site) normalmente vão para a porta `80`, enquanto `HTTPS` as mensagens vão para a porta `443`, a menos que configurado de outra forma. Encontraremos aplicativos da Web em execução em portas não padrão, mas normalmente os encontraremos nas portas 80 e 443. Os números de porta nos permitem acessar serviços ou aplicativos específicos em execução nos dispositivos de destino. Em um nível muito alto, as portas ajudam os computadores a entender como lidar com os vários tipos de dados que recebem.

Existem duas categorias de portas, [Transmission Control Protocol (TCP)](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) e [User Datagram Protocol (UDP)](https://en.wikipedia.org/wiki/User_Datagram_Protocol) .  
`TCP` é orientado à conexão, o que significa que uma conexão entre um cliente e um servidor deve ser estabelecida antes que os dados possam ser enviados. O servidor deve estar em estado de escuta aguardando solicitações de conexão dos clientes.  
`UDP` utiliza um modelo de comunicação sem conexão. Não há "aperto de mão" e, portanto, apresenta certa falta de confiabilidade, pois não há garantia de entrega de dados.  `UDP` é útil quando a correção/verificação de erros não é necessária ou é tratada pelo próprio aplicativo. `UDP` é adequado para aplicativos que executam tarefas sensíveis ao tempo, pois descartar pacotes é mais rápido do que esperar por pacotes atrasados ​​devido à retransmissão, como é o caso `TCP`e pode afetar significativamente um sistema de tempo real. Existem `65,535` `TCP` portas e portas `65,535` diferentes `UDP`, cada uma indicada por um número. `TCP` Algumas das portas e portas mais conhecidas `UDP` estão listadas abaixo:

|Porta(s)|Protocolo|
|---|---|
|`20`/ `21`(TCP)|`FTP`|
|`22`(TCP)|`SSH`|
|`23`(TCP)|`Telnet`|
|`25`(TCP)|`SMTP`|
|`80`(TCP)|`HTTP`|
|`161`(TCP/UDP)|`SNMP`|
|`389`(TCP/UDP)|`LDAP`|
|`443`(TCP)|`SSL`/ `TLS`( `HTTPS`)|
|`445`(TCP)|`SMB`|
|`3389`(TCP)|`RDP`|

Como profissionais de segurança da informação, devemos ser capazes de recuperar rapidamente grandes quantidades de informações sobre uma ampla variedade de tópicos. É essencial para nós, especialmente como pentesters, ter uma compreensão firme de muitas portas `TCP` e `UDP` e ser capaz de reconhecê-los apenas pelo seu número rapidamente (ou seja, saber que port `21` é `FTP`, porta `80` é `HTTP`, porta `88` é`Kerberos`) sem ter que procurá-lo . Isso virá com prática e repetição e eventualmente se tornará uma segunda natureza à medida que atacamos mais caixas, laboratórios e redes do mundo real e nos ajudam a trabalhar com mais eficiência e priorizar melhor nossos esforços de enumeração e ataques.

Guias como [este](https://www.stationx.net/common-ports-cheat-sheet/) e [este](https://packetlife.net/media/library/23/common-ports.pdf) são ótimos recursos para aprender portas TCP e UDP padrão e menos comuns. Desafie-se a memorizar o máximo possível e faça alguma pesquisa sobre cada um dos protocolos listados na tabela acima. Esta é uma ótima [referência](https://nullsec.us/top-1-000-tcp-and-udp-ports-nmap-default/) sobre os 1.000 principais portas `TCP` e `UDP`  `nmap` junto com os 100 principais serviços verificados por `nmap`.