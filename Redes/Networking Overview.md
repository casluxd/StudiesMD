
Uma rede permite que dois computadores se comuniquem. Existe uma grande variedade de topologias (mesh/tree/star), meios (ethernet/fibra/coax/wireless) e protocolos (TCP/UDP/IPX) que podem ser usados ​​para facilitar a rede. É importante que os profissionais de segurança entendam a rede porque, quando a rede falha, o erro pode ser silencioso, fazendo com que percamos alguma coisa.

Configurar uma rede grande e plana não é extremamente difícil e pode ser uma rede confiável, pelo menos operacionalmente. No entanto, uma rede plana é como construir uma casa em um terreno e considerá-la segura porque tem fechadura na porta. Ao criar várias redes menores e fazer com que elas se comuniquem, podemos adicionar camadas de defesa. Girar em torno de uma rede não é difícil, mas fazê-lo de forma rápida e silenciosa é difícil e atrasará os invasores. De volta ao cenário da casa, vamos percorrer os seguintes exemplos:

### Exemplo 1

Construir redes menores e colocar Listas de Controle de Acesso ao redor delas é como colocar uma cerca ao redor da fronteira da propriedade que cria pontos específicos de entrada e saída. Sim, um invasor pode pular a cerca, mas isso parece suspeito e não é comum, permitindo que seja rapidamente detectado como uma atividade maliciosa. Por que a rede da impressora está se comunicando com os servidores por HTTP?

### Exemplo 2

Reservar um tempo para mapear e documentar o propósito de cada rede é como colocar luzes ao redor da propriedade, certificando-se de que todas as atividades possam ser vistas. Por que a rede da impressora está se comunicando com a Internet?

### Exemplo 3

Ter arbustos ao redor das janelas é um impedimento para as pessoas que tentam abrir a janela. Assim como os sistemas de detecção de intrusão, como Suricata ou Snort, são um impedimento para a execução de varreduras de rede. Por que uma verificação de porta se originou na rede da impressora?

Esses exemplos podem parecer bobos e é de bom senso impedir que uma impressora execute todas as ações acima. No entanto, se a impressora estiver em uma "rede plana /24" e obtiver um endereço DHCP, pode ser um desafio colocar esses tipos de restrições nelas.

# Hora da História - Uma Supervisão dos Pentesters

A maioria das redes usa uma sub-rede /24, tanto que muitos testadores de penetração definirão essa máscara de sub-rede (255.255.255.0) sem verificar. A rede /24 permite que os computadores se comuniquem desde que os três primeiros octetos de um endereço IP sejam os mesmos (ex: 192.168.1.xxx). Definir a máscara de sub-rede para /25 divide esse intervalo pela metade, e o computador poderá se comunicar apenas com os computadores da "sua metade". Vimos relatórios de teste de penetração em que o avaliador alegou que um controlador de domínio estava offline quando, na realidade, estava apenas em uma rede diferente. A estrutura da rede era mais ou menos assim:

- Server Gateway: 10.20.0.1/25
- Domain Controller: 10.20.0.10/25
- Client Gateway: 10.20.0.129/25
- Client Workstation: 10.20.0.200/25
- Pentester IP: 10.20.0.252/24 (Gateway setado para 10.20.0.1)

O Pentester se comunicou com as estações de trabalho do cliente e achou que eles fizeram um excelente trabalho porque conseguiram roubar uma senha da estação de trabalho via Impacket. No entanto, devido a uma falha no entendimento da rede, eles nunca conseguiram sair da Rede do Cliente e atingir mais destinos de "alto valor", como servidores de banco de dados. Esperançosamente, se isso parece confuso para você, você pode voltar a esta declaração no final do módulo e entendê-la!

# Informação básica

Vejamos o seguinte diagrama de alto nível de como uma configuração Work From Home pode funcionar.

![[Pasted image 20230526222112.png]]

Toda a Internet é baseada em muitas redes subdivididas, conforme mostrado no exemplo e marcadas como "Rede Doméstica" e "Rede da Empresa". Podemos imaginar a rede como a entrega de correspondência ou pacotes enviados por um computador e recebidos por outro.

Suponha que imaginamos como um cenário que queremos visitar o site de uma empresa a partir de nossa "rede doméstica". Nesse caso, trocamos dados com o site da empresa localizado em sua "Rede de empresas". Assim como no envio de correspondência ou pacotes, sabemos o endereço para onde os pacotes devem ir. O endereço do site ou localizador uniforme de recursos (URL) que inserimos em nosso navegador também é conhecido como nome de domínio totalmente qualificado (FQDN).

A diferença entre URLs e FQDNs é que:
- um FQDN (www.hackthebox.eu) apenas especifica o endereço do "edifício" e
- um URL (https://www.hackthebox.eu/example?floor=2&office=dev&employee=17) também especifica o "andar", "escritório", "caixa de correio" e o "funcionário" correspondente a quem o pacote se destina.

O fato é que sabemos o endereço, mas não a localização geográfica exata do endereço. Nessa situação, o correio pode determinar o local exato, que então encaminha os pacotes para o local desejado. Portanto, nosso correio encaminha nossos pacotes para o correio principal, representando nosso provedor de serviços de Internet (ISP).

Nossa agência postal é nosso roteador que utilizamos para conectar-se à "Internet" na rede.

Assim que enviamos nosso pacote através de nossa agência postal (roteador), o pacote é encaminhado para a agência postal principal (ISP). Esta agência postal principal procura no registro de endereço/lista telefônica (Domain Name Service) onde este endereço está localizado e retorna as coordenadas geográficas correspondentes (endereço IP). Agora que sabemos a localização exata do endereço, nosso pacote é enviado diretamente para lá por um voo direto via nossa agência postal principal.

Depois que o servidor web recebe nosso pacote com a solicitação de como é o site, o servidor web nos envia de volta o pacote com os dados para a apresentação do site via correio (roteador) da "Rede da Empresa" para o endereço de retorno especificado (nosso endereço IP).

# Pontos extras

Nesse diagrama, espero que a rede da empresa mostrada consista em cinco redes separadas!

1. O Web Server deve estar em uma DMZ (Zona Desmilitarizada) porque os clientes na Internet podem iniciar a comunicação com o site, tornando-o mais propenso a ser comprometido. Colocá-lo em uma rede separada permite que os administradores coloquem proteções de rede entre o servidor da Web e outros dispositivos.
2. As estações de trabalho devem estar em sua própria rede e, em um mundo perfeito, cada estação de trabalho deve ter uma regra de firewall baseado em host que a impeça de se comunicar com outras estações de trabalho. Se uma estação de trabalho estiver na mesma rede que um servidor, ataques de rede como spoofing ou man in the middle se tornarão um problema muito maior.
3. O switch e o roteador devem estar em uma "rede de administração". Isso evita que as estações de trabalho bisbilhotem qualquer comunicação entre esses dispositivos. Muitas vezes, realizei um teste de penetração e vi anúncios OSPF (Open Shortest Path First). Como o roteador não tinha uma rede confiável, qualquer pessoa na rede interna poderia ter enviado um anúncio malicioso e realizado um ataque man in the middle contra qualquer rede.
4. Os telefones IP devem estar em sua própria rede. Em termos de segurança, isso evita que os computadores possam interceptar a comunicação. Além da segurança, os telefones são únicos no sentido de que a latência/atraso é significativo. Colocá-los em sua própria rede pode permitir que os administradores de rede priorizem seu tráfego para evitar alta latência com mais facilidade.
5. As impressoras devem estar em sua própria rede. Isso pode parecer estranho, mas é quase impossível proteger uma impressora. Devido à forma como o Windows funciona, se uma impressora informar que a autenticação do computador é necessária durante um trabalho de impressão, esse computador tentará uma autenticação NTLMv2, o que pode levar ao roubo de senhas. Além disso, esses dispositivos são ótimos para persistência e, em geral, recebem toneladas de informações confidenciais enviadas a eles.

# Fun Story

Durante o COVID, fui incumbido de realizar um Teste de Penetração Física em todos os estados, e meu estado estava sob ordem de permanência em casa. A empresa que eu estava testando tinha poucos funcionários no escritório. Resolvi comprar uma impressora cara e a explorei para colocar um shell reverso nela, assim, quando ela se conectasse à rede, me enviaria um shell (acesso remoto). Em seguida, enviei a impressora para a empresa e enviei um e-mail de phishing agradecendo à equipe por ter vindo e explicando que a impressora deveria permitir que eles imprimissem ou digitalizassem coisas mais rapidamente se quisessem trazer algumas coisas para casa para WFH por alguns dias. A impressora foi conectada quase instantaneamente, e o computador do administrador do domínio teve a gentileza de enviar à impressora suas credenciais!

Se o cliente tivesse projetado uma rede segura, esse ataque provavelmente não teria sido possível por vários motivos:
- A impressora não deveria ser capaz de falar com a internet
- A estação de trabalho não deveria ser capaz de se comunicar com a impressora pela porta 445
- A impressora não deve ser capaz de iniciar conexões com estações de trabalho. Em alguns casos, combinações de impressora/scanner devem ser capazes de se comunicar com um servidor de e-mail para enviar documentos digitalizados por e-mail.

