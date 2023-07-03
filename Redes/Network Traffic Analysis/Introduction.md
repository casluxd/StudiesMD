
Análise de tráfego de rede (NTA) pode ser descrita como o ato de examinar o tráfego de rede para caracterizar portas e protocolos comuns utilizados, estabelecer uma linha de base para nosso ambiente, monitorar e responder a ameaças e garantir o maior conhecimento possível sobre a rede de nossa organização. Esse processo ajuda os especialistas em segurança a determinar anomalias, incluindo ameaças de segurança na rede, identificando ameaças com antecedência e eficácia. A Análise de Tráfego de Rede também pode facilitar o processo de cumprimento das diretrizes de segurança. Os invasores atualizam suas táticas com frequência para evitar a detecção e alavancar credenciais legítimas com ferramentas que a maioria das empresas permite em suas redes, tornando a detecção e, subsequentemente, a resposta desafiadora para os defensores. Nesses casos, a análise de tráfego de rede pode novamente ser útil. Casos de uso diário de NTA incluem:
- Coleta de tráfego em tempo real dentro da rede para analisar ameaças futuras.
- Definir uma linha de base para as comunicações de rede do dia-a-dia.
- Identificar e analisar o tráfego de portas não padrão, hosts suspeitos e problemas com protocolos de rede, como erros de HTTP, problemas com TCP ou outras configurações incorretas de rede.
- Detecção de malware na rede, como ransomware, exploits e interações fora do padrão.
- O NTA também é útil ao investigar incidentes anteriores e durante a busca por ameaças.

Tente imaginar um agente de ameaça visando e se infiltrando em nossa rede. Se quiserem invadir a rede, os invasores devem inevitavelmente interagir e se comunicar com nossa infraestrutura. A comunicação de rede ocorre em muitas portas e protocolos diferentes, todos sendo utilizados simultaneamente por funcionários, equipamentos e clientes. Para detectar tráfego malicioso, precisaríamos usar nosso conhecimento do tráfego de rede típico em nosso enclave. Isso restringirá nossa pesquisa e nos ajudará a encontrar e interromper rapidamente a comunicação adversária.
 
Por exemplo, se detectarmos muitos pacotes SYN em portas que nunca (ou raramente) utilizamos em nossa rede, podemos concluir que provavelmente é alguém tentando determinar quais portas estão abertas em nossos hosts. Ações como esta são marcadores típicos de um portscan. Realizar tal análise e chegar a tais conclusões requer habilidades e conhecimentos específicos.

## Skills e conhecimentos requisitados

As competências que vamos listar e descrever requerem conhecimentos teóricos e práticos adquiridos ao longo do tempo. Não precisamos saber tudo de cor, mas devemos saber o que procurar quando certos aspectos do conteúdo nos parecem estranhos. Isso se aplica não apenas ao NTA, mas também à maioria dos outros tópicos que abordaremos em segurança cibernética.

- TCP/IP Stack & OSI Model
- Conhecimentos básicos de conceitos de rede
- Portas e Protocolos comuns
- Conceitos de Pacotes IP e Subcamadas
- Encapsulamento de transporte de protocolo

## Ambiente e Equipamentos

A lista abaixo contém muitas ferramentas e tipos de equipamentos diferentes que podem ser utilizados para realizar a análise de tráfego de rede. Cada um fornecerá uma maneira diferente de capturar ou dissecar o tráfego. Alguns oferecem maneiras de copiar e capturar, enquanto outros leem e ingerem. Este módulo irá explorar apenas alguns deles (principalmente Wireshark e tcpdump). Lembre-se de que essas ferramentas não são estritamente voltadas para administradores. Muitos deles também podem ser usados ​​por motivos maliciosos.

### Ferramentas comuns de análise de tráfego

|**Tool**|**Description**|
|---|---|
|`tcpdump`|[tcpdump](https://www.tcpdump.org/) is a command-line utility that, with the aid of LibPcap, captures and interprets network traffic from a network interface or capture file.|
|`Tshark`|[TShark](https://www.wireshark.org/docs/man-pages/tshark.html) is a network packet analyzer much like TCPDump. It will capture packets from a live network or read and decode from a file. It is the command-line variant of Wireshark.|
|`Wireshark`|[Wireshark](https://www.wireshark.org/) is a graphical network traffic analyzer. It captures and decodes frames off the wire and allows for an in-depth look into the environment. It can run many different dissectors against the traffic to characterize the protocols and applications and provide insight into what is happening.|
|`NGrep`|[NGrep](https://github.com/jpr5/ngrep) is a pattern-matching tool built to serve a similar function as grep for Linux distributions. The big difference is that it works with network traffic packets. NGrep understands how to read live traffic or traffic from a PCAP file and utilize regex expressions and BPF syntax. This tool shines best when used to debug traffic from protocols like HTTP and FTP.|
|`tcpick`|[tcpick](http://tcpick.sourceforge.net/index.php?p=home.inc) is a command-line packet sniffer that specializes in tracking and reassembling TCP streams. The functionality to read a stream and reassemble it back to a file with tcpick is excellent.|
|`Network Taps`|Taps ([Gigamon](https://www.gigamon.com/), [Niagra-taps](https://www.niagaranetworks.com/products/network-tap)) are devices capable of taking copies of network traffic and sending them to another place for analysis. These can be in-line or out of band. They can actively capture and analyze the traffic directly or passively by putting the original packet back on the wire as if nothing had changed.|
|`Networking Span Ports`|[Span Ports](https://en.wikipedia.org/wiki/Port_mirroring) are a way to copy frames from layer two or three networking devices during egress or ingress processing and send them to a collection point. Often a port is mirrored to send those copies to a log server.|
|`Elastic Stack`|The [Elastic Stack](https://www.elastic.co/elastic-stack) is a culmination of tools that can take data from many sources, ingest the data, and visualize it, to enable searching and analysis of it.|
|`SIEMS`|`SIEMS` (such as [Splunk](https://www.splunk.com/en_us)) are a central point in which data is analyzed and visualized. Alerting, forensic analysis, and day-to-day checks against the traffic are all use cases for a SIEM.|

# BPF Syntax

Muitas das ferramentas mencionadas acima têm sua sintaxe e comandos para utilizar, mas uma que é compartilhada entre elas é a sintaxe do Berkeley Packet Filter (BPF). Essa sintaxe é o método principal que usaremos. Em essência, o BPF é uma tecnologia que permite que uma interface bruta leia e grave na camada de enlace de dados. Pensando nisso, escolhemos o BPF pela capacidade de filtragem e decodificação que ele nos proporciona. Estaremos utilizando a sintaxe BPF através do módulo, portanto, uma compreensão básica de como um filtro BPF é configurado pode ser útil.

### Performando análise de trafego de rede

A realização da análise pode ser tão simples quanto assistir ao tráfego ao vivo em nosso console ou tão complexa quanto capturar dados com um toque, enviando-os de volta a um SIEM para ingestão, e analisando os dados pcap para assinaturas e alertas relacionados a táticas e técnicas comuns.

No mínimo, para ouvir passivamente, precisamos estar conectados ao segmento de rede que desejamos ouvir. Isso é especialmente verdadeiro em um ambiente comutado onde as VLANS e as portas do switch não encaminharão o tráfego para fora de seu domínio de transmissão. Pensando nisso, se quisermos capturar o tráfego de uma VLAN específica, nosso dispositivo de captura deve estar conectado a essa mesma rede. Dispositivos como network taps, configurações de switch ou roteador como span ports e port mirroring podem nos permitir obter uma cópia de todo o tráfego que passa por um link específico, independentemente de qual segmento de rede ou destino ele pertence.

# NTA Workflow

![[Pasted image 20230529193254.png]]

1. Ingest Traffic: Depois de decidirmos nosso posicionamento, comece a capturar o tráfego. Utilize filtros de captura se já tivermos uma ideia do que estamos procurando.
2. Reduce Noise by Filtering: Capturar o tráfego de um link, especialmente um em um ambiente de produção, pode ser extremamente ruidoso. Depois de concluir a captura inicial, uma tentativa de filtrar o tráfego desnecessário de nossa visualização pode facilitar a análise. (Broadcast and Multicast traffic, por exemplo)
3. Analyze and Explore: Agora é a hora de começar a extrair dados pertinentes ao problema que estamos perseguindo. Olhe para hosts específicos, protocolos, até mesmo coisas tão específicas quanto sinalizadores definidos no cabeçalho TCP. As seguintes perguntas nos ajudarão:
 - O tráfego é criptografado ou texto simples? Deveria ser?
 - Podemos ver usuários tentando acessar recursos aos quais não deveriam ter acesso?
 - Hosts diferentes estão conversando entre si e normalmente não?
4. Detect the Root Issue: 
- Estamos vendo algum erro? Um dispositivo não está respondendo como deveria?
- Use nossa análise para decidir se o que vemos é benigno ou potencialmente malicioso.
- Outras ferramentas como IDS e IPS podem ser úteis neste momento. Eles podem executar heurísticas e assinaturas no tráfego para determinar se algo dentro dele é potencialmente malicioso.
5. Fix and Monitor: Corrigir e monitorar não faz parte do loop, mas deve ser incluído em qualquer fluxo de trabalho que executamos. Se fizermos uma alteração ou corrigirmos um problema, devemos continuar monitorando a fonte por um tempo para determinar se o problema foi resolvido.