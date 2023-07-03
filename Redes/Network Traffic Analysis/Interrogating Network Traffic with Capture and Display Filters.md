
Este laboratório tem como objetivo fornecer alguma exposição para interrogar o tráfego de rede e dar a todos alguma prática valiosa na implementação de filtros de pacotes. Estaremos utilizando filtros como host, porta, protocolo e muito mais para alterar nossa visão enquanto vasculhamos um arquivo .PCAP.

Agora que provamos ser capazes de capturar o tráfego de rede para a Corporação, a administração nos encarregou de realizar uma análise rápida do tráfego que nossa equipe capturou durante o levantamento da rede. O objetivo é determinar quais servidores estão respondendo às solicitações de DNS e HTTP/S em nossa rede local.

### Tasks:

1. Identifique o tipo de tráfego: portas utilizadas, protocolos comuns e outras informações relevantes

Protocolos comuns: DNS, HTTP e HTTPs
Portas comuns: 53, 80, 443

2. Identifique as conversas: agora que examinamos o básico do tráfego, agora determinamos se encontramos algum padrão nesse tráfego. Você está percebendo alguma conexão comum entre um servidor e um host. Se sim, quem? Quais são os números de porta do cliente e do servidor usados no primeiro three-way handshake completo? Quem são os servidores nessa conversa? Como nós sabemos? Quem são os hosts?

Para ajudar a tornar isso mais direto, ativar os números de sequência absoluta (-S) será extremamente útil para determinar as conversas. Também podemos examinar os diferentes hosts envolvidos. Os servidores normalmente se comunicam pelo número de porta conhecido atribuído ao protocolo (80 para HTTP, 443 para HTTPS, por exemplo). Os hosts ou destinatários nas conversas geralmente utilizam um número de porta alto aleatório.

3. Interprete a captura em profundidade. Agora que temos alguma familiaridade com o arquivo pcap, vamos fazer algumas análises. Utilize qualquer sintaxe necessária para conseguir responder às perguntas abaixo. Qual é o timestamp da primeira conversa estabelecida no arquivo pcap? Qual é o endereço IP do apache.org das respostas do servidor DNS? Qual protocolo está sendo utilizado nessa primeira conversa? (nome/#)

Timestamp da primeira conversa: 12:34:01
O endereço IP do servidor DNS do apache.org é 172.16.146.1
Protocolo da primeira conversa: HTTPs

Para determinar o carimbo de data/hora correto: Leia o arquivo com (-r) e examine as conversas. Encontre o primeiro estabelecido ( Full TCP Handshake "Syn / SYN-ACK / ACK") e observe o primeiro campo na saída para localizar o registro de data e hora. Para encontrar o IP do host ( ), podemos filtrar o tráfego apenas para ver as conversas provenientes dele ( src 'name-of-host' ) e, em seguida, desabilitar a resolução de nomes com (-n). Para determinar o protocolo que está sendo utilizado na primeira conversa, procure o conhecido número da porta enquanto desativa o nome do host e a resolução do nome da porta (-nn) ou deixe de fora (-nn) e ele informará o nome do protocolo em a saída.

1. É hora de limpar alguns desses dados agora. Recarregue o arquivo pcap e filtre todo o tráfego que não seja DNS. O que você pode ver? Quem é o servidor DNS para este segmento? Quais nomes de domínio foram solicitados no arquivo pcap? Que tipo de registros DNS pode ser visto?

O servidor DNS para este segmento é 172.16.146.1
