
A análise de tráfego de rede é um processo dinâmico que pode mudar dependendo das ferramentas que temos em mãos, das permissões que nos são dadas pela organização e da visibilidade de nossa rede. Nosso objetivo é fornecer um processo repetível que possamos começar a utilizar ao realizar a análise de tráfego.

A Análise de Tráfego é um exame detalhado de um evento ou processo, determinando sua origem e impacto, que pode ser utilizada para desencadear precauções específicas e/ou ações para apoiar ou prevenir futuras ocorrências. Com o tráfego de rede, isso significa dividir os dados em partes compreensíveis, examinando-os em busca de qualquer coisa que se desvie do tráfego de rede regular, em busca de tráfego potencialmente malicioso, como comunicações remotas não autorizadas da Internet por RDP, SSH ou Telnet, ou instâncias únicas anteriores a problemas de rede. Ao realizar nossa análise, também procuramos ver como são as tendências no tráfego e determinar se ele corresponde a uma linha de base do tráfego operacional típico. 

A análise de tráfego é uma ferramenta altamente versátil e essencial para se ter em nossa caixa de ferramentas defensiva. Sem a capacidade de monitorar o tráfego, estamos trabalhando com uma grande peça do quebra-cabeça faltando. Análises sobre o uso da rede, hosts e servidores mais falados e comunicações internas são peças cruciais que fornecem a nós, administradores e defensores, uma maneira de ver e corrigir problemas antes ou logo após eles acontecerem. A visibilidade é provavelmente a coisa mais benéfica que ele oferece. Com essa visibilidade, podemos capturar o tráfego em diferentes períodos para definir uma linha de base para nosso ambiente. Essa linha de base torna mais fácil ver quando uma alteração ocorreu. Em implementações mais avançadas para NTA que incluem outras ferramentas como IDS/IPS, firewalls, host e logs de rede, e informações adicionais alimentadas em ferramentas como Splunk ou ELK Stack, ter a capacidade de monitorar o tráfego é inestimável. As ferramentas nos ajudam a alertar rapidamente sobre ações maliciosas acontecendo. Muitas ferramentas defensivas têm assinaturas criadas para a maioria dos ataques e kits de ferramentas comuns.

Ter capacidades defensivas adequadas é vital para todos, mas e as operações diárias? Como o NTA pode nos ajudar? Assistir ao tráfego de rede ao vivo pode facilitar a solução de problemas de conexão ou determinar se nossa infraestrutura e os protocolos correspondentes estão funcionando corretamente. Se pudermos ver para onde o tráfego está indo, podemos determinar se há um problema.

Por último, esta é uma habilidade dinâmica, e usar ferramentas automatizadas para nos ajudar é perfeitamente aceitável. Apenas não confie apenas neles. Utilize as habilidades que você possui e faça verificações manuais também. Isso nos ajudará colocando os olhos em nossa rede. Teremos freios e contrapesos entre nós e as ferramentas, pois as ferramentas podem ser vencidas. Atores mal-intencionados estão encontrando maneiras de contornar as medidas de segurança o tempo todo. O olho humano ainda é nosso melhor recurso para encontrar o que é ruim.

|**Dependencies**|**Passive**|**Active**|**Description**|
|---|---|---|---|
|`Permission`|`☑`|`☑`|Depending on the organization we are working in, capturing data can be against policy or even against the law in some sensitive areas like healthcare or banking. Be sure always to obtain permission in writing from someone with the proper authority to grant it to you. We may style ourselves as hackers, but we want to stay in the light legally and ethically.|
|`Mirrored Port`|`☑`|☐|A switch or router network interface configured to copy data from other sources to that specific interface, along with the capability to place your NIC into promiscuous mode. Having packets copied to our port allows us to inspect any traffic destined to the other links we could normally not have visibility over. Since VLANs and switch ports will not forward traffic outside of their broadcast domain, we have to be connected to the segment or have that traffic copied to our specific port. When dealing with wireless, passive can be a bit more complicated. We must be connected to the SSID we wish to capture traffic off of. Just passively listening to the airwaves around us will present us with many SSID broadcast advertisements, but not much else.|
|`Capture Tool`|`☑`|`☑`|A way to ingest the traffic. A computer with access to tools like TCPDump, Wireshark, Netminer, or others is sufficient. Keep in mind that when dealing with PCAP data, these files can get pretty large quickly. Each time we apply a filter to it in tools like Wireshark, it causes the application to parse that data again. This can be a resource-intensive process, so make sure the host has abundant resources.|
|`In-line Placement`|☐|`☑`|Placing a Tap in-line requires a topology change for the network you are working in. The source and destination hosts will not notice a difference in the traffic, but for the sake of routing and switching, it will be an invisible next hop the traffic passes through on its way to the destination.|
|`Network Tap or Host With Multiple NIC's`|☐|`☑`|A computer with two NIC's, or a device such as a Network Tap is required to allow the data we are inspecting to flow still. Think of it as adding another router in the middle of a link. To actively capture the traffic, we will be duplicating data directly from the sources. The best placement for a tap is in a layer three link between switched segments. It allows for the capture of any traffic routing outside of the local network. A switched port or VLAN segmentation does not filter our view here.|
|`Storage and Processing Power`|`☑`|`☑`|You will need plenty of storage space and processing power for traffic capture off a tap. Much more traffic is traversing a layer three link than just inside a switched LAN. Think of it like this; When we passively capture traffic inside a LAN, it's like pouring water into a cup from a water fountain. It's a steady stream but manageable. Actively grabbing traffic from a routed link is more like using a water hose to fill up a teacup. There is a lot more pressure behind the flow, and it can be a lot for the host to process and store.|

A última dependência é mais uma recomendação do que um requisito, mas achamos necessário mencioná-la. Ter uma compreensão de como o tráfego do dia-a-dia flui é fundamental para ser bem sucedido. É possível realizar a análise de tráfego sem uma, mas será muito mais difícil e demorado. A linha de base nos permitirá filtrar rapidamente o tráfego comum para essa rede enquanto realizamos nossa análise. Isso pode acelerar nosso processo e ajudar a identificar os valores discrepantes ou problemas muito mais cedo. Vamos olhar para este cenário por um segundo:

```
Você é administrador de rede de uma grande empresa com vários milhares de funcionários no campus. Foi informado de que um segmento da sua rede está com problemas de conectividade. Vários desses hosts estão relatando latência extremamente alta, junto com novos arquivos aparecendo em suas áreas de trabalho. Para começar a obter uma imagem do que está acontecendo, você conecta um computador a esse segmento e inicia uma captura. Depois de alguns minutos, você interrompe a captura e inicia a análise.
```

Agora considere isso. Sem uma linha de base de nosso tráfego de rede diário, como sabemos o que é típico dessa rede? Pegamos uma tonelada de informações durante o período de captura e precisamos limpar algumas delas. Esse processo pode levar muito tempo, pois teremos que examinar cada conversa para garantir que está tudo bem, determinar se os hosts que vemos pertencem à rede ou são ativos não autorizados, entre muito mais.

Com esse cenário e acesso a uma linha de base de rede, podemos eliminar rapidamente as comunicações em boas condições. A utilização de ferramentas de análise de dados, como o módulo top talkers no Wireshark, pode ajudar a identificar hosts que podem estar enviando uma grande quantidade de dados. Podemos verificar isso em relação à linha de base normal do host para determinar se está fora do personagem, ou seja, não está fazendo o que é comum. Outra maneira seria observar as conexões entre hosts internos ou portas comuns e incomuns. Como pudemos limpar nossa visão, agora podemos ver que vários hosts de usuários se conectam nas portas 8080 e 445. As portas em si não são estranhas, mas o fato de serem dois PCs de usuários conversando entre si por essas portas é. O tráfego da Web geralmente flui de um host para um servidor da Web hospedado ou um servidor da Web da intranet que hospeda aplicativos de negócios. O mesmo pode ser dito para o tráfego SMB. É muito suspeito ver dois hosts conversando entre si por esta porta. Com o que sabemos agora, podemos enviar rapidamente um tíquete de problema em busca de ajuda para lidar com uma possível violação agora.

Ao falar sobre invasões de rede, quanto mais rápido conseguirmos visibilidade, menos danos potenciais à nossa rede. Certifique-se de entender claramente como o tráfego flui em nossas redes e como os protocolos geralmente agem.

# Dicas para análise

Abaixo está uma lista de perguntas que podemos nos fazer durante o processo de análise para manter o controle.

- que tipo de tráfego você vê? (protocolo, porta, etc.)
- Há mais de uma conversa? (quantos?)
- Quantos hosts únicos?
- Qual é o timestamp da primeira conversa no pcap (tráfego tcp)
- Qual tráfego posso filtrar para limpar minha visualização?
- Quais registros foram solicitados ou métodos utilizados? (GET, POST, registros DNS A, etc.)


## Autoanálise

Antes de seguir as tarefas abaixo, reserve um tempo para percorrer nosso tráfego pcap sem orientação. Use as habilidades que testamos anteriormente, como seguir fluxos, análise de conversas e outras habilidades para determinar o que está acontecendo. Mantenha estas perguntas em mente ao realizar a análise:
- Quantas conversas podem ser vistas?
- Podemos determinar quem são os clientes e servidores?
- Quais protocolos estão sendo utilizados?
- Algo digno de nota está acontecendo? (portas sendo mal utilizadas, tráfego de texto simples ou credenciais, etc.)

Neste laboratório, estamos preocupados com os hosts 172.16.10.2 e 172.16.10.20 durante a execução das etapas a seguir. Em nossa análise, devemos ter notado algum tráfego da Web entre esses hosts e algum tráfego de FTP. Vamos cavar um pouco mais fundo.

## Análise FTP

Ao examinar o tráfego, capturamos, algum tráfego pertencente ao FTP foi notado? Quem era o servidor desse tráfego?

Examine os comandos FTP para determinar o que você precisa inspecionar e, em seguida, extraia os arquivos de ftp-data e remonte-os

## Análise HTTP

Deveríamos ter visto um pouco de tráfego HTTP também. Foi este o seu caso?

Em caso afirmativo, poderíamos determinar quem é o servidor da web?

Qual aplicativo está executando o servidor web?

Quais foram as solicitações de método mais comuns que você viu?