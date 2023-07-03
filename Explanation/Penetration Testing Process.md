

## Pre-Engagement

A fase de pré-engajamento é onde os principais compromissos, tarefas, escopo, limitações e acordos relacionados são documentados por escrito. Nesta etapa, são elaborados documentos contratuais e trocadas informações essenciais e relevantes para os testadores de penetração e para o cliente, dependendo do tipo de avaliação.

![[Pasted image 20230613125738.png]]

Há apenas um caminho que podemos seguir a partir daqui:
- Information Gathering: Em seguida, avançamos para a fase de coleta de informações. Antes que qualquer sistema-alvo possa ser examinado e atacado, devemos primeiro identificá-los. Pode ser que o cliente não nos forneça nenhuma informação sobre sua rede e componentes além de um nome de domínio ou apenas uma lista de endereços IP/intervalos de rede dentro do escopo. Portanto, precisamos obter uma visão geral do(s) aplicativo(s) da Web ou rede de destino antes de prosseguir.

#### Information Gathering

A coleta de informações é uma parte essencial de qualquer avaliação. Porque a informação, o conhecimento adquirido com ela, as conclusões que tiramos e os passos que damos são baseados nas informações disponíveis. Essas informações devem ser obtidas de algum lugar, por isso é fundamental saber como recuperá-las e melhor aproveitá-las com base em nossos objetivos de avaliação.

![[Pasted image 20230613130134.png]]

A partir desta etapa, a próxima parte do nosso caminho é clara:
- Vulnerability Assessment: A próxima parada em nossa jornada é a Avaliação de Vulnerabilidade, onde usamos as informações encontradas para identificar possíveis pontos fracos. Podemos usar scanners de vulnerabilidade que examinarão os sistemas de destino em busca de vulnerabilidades conhecidas e análises manuais, nas quais tentamos olhar nos bastidores para descobrir onde podem estar as vulnerabilidades em potencial.

As informações que coletamos com antecedência influenciarão os resultados da etapa de Exploração. A partir disso, podemos ver se coletamos o suficiente ou mergulhamos fundo o suficiente. Tempo, paciência e comprometimento pessoal desempenham um papel significativo na coleta de informações. É quando muitos testadores de penetração tendem a pular direto para a exploração de uma vulnerabilidade em potencial. Isso geralmente falha e pode levar, entre outras coisas, a uma perda significativa de tempo. Antes de tentar explorar qualquer coisa, devemos ter completado a coleta completa de informações, mantendo notas detalhadas ao longo do caminho, focando em coisas para aprimorar quando chegarmos ao estágio de exploração. A maioria das avaliações é baseada em tempo, então não queremos perder tempo pulando, o que poderia nos levar a perder algo crítico. Organização e paciência são vitais, sendo o mais minucioso possível.


## Vulnerability Assessment

O estágio de avaliação de vulnerabilidade é dividido em duas áreas. Por um lado, é uma abordagem para verificar vulnerabilidades conhecidas usando ferramentas automatizadas. Por outro lado, está analisando possíveis vulnerabilidades por meio das informações encontradas. Muitas empresas conduzem auditorias regulares de avaliação de vulnerabilidade para verificar se há novas vulnerabilidades conhecidas em sua infraestrutura e compará-las com as entradas mais recentes nos bancos de dados dessas ferramentas.

Uma análise é mais sobre pensar fora da caixa. Tentamos descobrir lacunas e oportunidades para enganar os sistemas e aplicativos a nosso favor e obter acesso ou privilégios não intencionais. Isso requer criatividade e um profundo conhecimento técnico. Devemos conectar os vários pontos de informação que obtemos e entender seus processos.

![[Pasted image 20230613131054.png]]

A partir desta etapa, existem quatro caminhos que podemos seguir, dependendo de quanto avançamos:
- Exploitation: O primeiro em que podemos pular é o estágio de Exploração. Isso acontece quando ainda não temos acesso a um sistema ou aplicativo. Claro, isso pressupõe que já identificamos pelo menos uma lacuna e preparamos tudo o que é necessário para tentar explorá-la.
- Post-Exploitation: A segunda forma leva ao estágio Pós-exploração, onde escalamos os privilégios no sistema de destino. Isso pressupõe que já estamos no sistema de destino e podemos interagir com ele.
- Lateral Movement: Nossa terceira opção é a etapa de Movimento Lateral, onde passamos do sistema já explorado pela rede e atacamos outros sistemas. Novamente, isso pressupõe que já estamos em um sistema de destino e podemos interagir com ele. No entanto, a escalação de privilégios não é estritamente necessária porque interagir com o sistema já nos permite avançar na rede em determinadas circunstâncias. Outras vezes, precisaremos escalar privilégios antes de mover lateralmente. Cada avaliação é diferente.
- Information Gathering: A última opção é retornar ao estágio de coleta de informações quando não temos informações suficientes em mãos. Aqui podemos cavar mais fundo para encontrar mais informações que nos darão uma visão mais precisa.

A capacidade de analisar vem com o tempo e a experiência. No entanto, também precisa ser treinado porque a análise adequada faz conexões entre diferentes pontos e informações. Conectar essas informações sobre a rede de destino ou sistema de destino e nossa experiência geralmente nos permite reconhecer padrões específicos. Podemos comparar isso com a leitura. Uma vez que tenhamos lido certas palavras com bastante frequência, saberemos essa palavra em algum momento e entenderemos o que significa apenas olhando para as letras.

## Exploitation

A exploração é o ataque executado contra um sistema ou aplicativo com base na vulnerabilidade potencial descoberta durante nossa coleta e enumeração de informações. Utilizamos as informações da etapa de Coleta de Informações, analisamos na etapa de Avaliação de Vulnerabilidades e preparamos os possíveis ataques. Freqüentemente, muitas empresas e sistemas usam os mesmos aplicativos, mas tomam decisões diferentes sobre sua configuração. Isso ocorre porque muitas vezes o mesmo aplicativo pode ser usado para diversos fins, e cada organização terá objetivos diferentes.

![[Pasted image 20230613131346.png]]

A partir desta etapa, existem quatro caminhos que podemos seguir, dependendo de quanto avançamos:
- Information Gathering: Assim que tivermos acesso inicial ao sistema de destino, independentemente de quão altos sejam nossos privilégios naquele momento, precisamos coletar informações sobre o sistema local. Não importa se usamos essas novas informações para escalonamento de privilégios, movimento lateral ou exfiltração de dados. Portanto, antes de podermos dar mais passos, precisamos descobrir com o que estamos lidando. Isso inevitavelmente nos leva ao estágio de avaliação de vulnerabilidade, onde analisamos e avaliamos as informações que encontramos.
- Post-Exploitation: A pós-exploração é principalmente sobre a escalada de privilégios se ainda não obtivermos os direitos mais altos possíveis no host de destino. Na verdade, esse caminho inclui os estágios Coleta de Informações, Avaliação de Vulnerabilidade, Exploração e Movimento Lateral, mas de uma perspectiva interna no sistema de destino. O salto direto para a pós-exploração é menos frequente, mas acontece. Porque na fase de exploração, podemos já ter obtido os privilégios mais altos e, a partir daqui, começamos novamente na coleta de informações.
- Lateral Movement: A partir daqui, também podemos pular diretamente para o Movimento Lateral. Isso pode ocorrer em diferentes condições. Se obtivermos os privilégios mais altos em um sistema dual-homed usado para conectar duas redes, provavelmente poderemos usar esse host para começar a enumerar os hosts que não estavam disponíveis anteriormente para nós.
- Proof-of-Concept: Podemos seguir o último caminho depois de obter os privilégios mais altos, explorando um sistema interno. Claro, não precisamos necessariamente assumir todos os sistemas. No entanto, se obtivermos os privilégios de administrador de domínio em um ambiente do Active Directory, provavelmente poderemos nos mover livremente por toda a rede e executar qualquer ação que possamos imaginar. Assim podemos criar a Prova de Conceito a partir de nossas anotações para detalhar e potencialmente automatizar os caminhos e atividades e disponibilizá-los ao departamento técnico.

Esta etapa é tão abrangente que foi dividida em duas áreas distintas. A primeira categoria são os protocolos de rede gerais frequentemente usados ​​e presentes em quase todas as redes. A exploração real das vulnerabilidades potenciais e existentes é baseada na adaptabilidade e conhecimento dos diferentes protocolos de rede com os quais iremos lidar. Além disso, precisamos ser capazes de criar uma visão geral da rede existente para entender os propósitos de seus componentes individuais. Na maioria dos casos, os servidores e aplicativos da web contêm uma grande quantidade de informações que podem ser usadas contra eles. Como dito anteriormente, por ser a web uma vasta área técnica por si só, ela será tratada separadamente. Também estamos interessados ​​nos serviços expostos remotamente em execução nos hosts de destino, pois eles podem ter configurações incorretas ou vulnerabilidades públicas conhecidas que podemos aproveitar para acesso inicial. Finalmente, os usuários existentes também desempenham um papel significativo na rede geral.


## Web Exploitation

A exploração da Web é a segunda parte do estágio de exploração. Muitas tecnologias, melhorias, recursos e aprimoramentos diferentes foram desenvolvidos nessa área nos últimos anos, e as coisas estão em constante evolução. Como resultado, muitos componentes diferentes entram em ação ao lidar com aplicativos da web. Isso inclui muitos tipos de bancos de dados que requerem diferentes sintaxes de comando para interagir. Devido à diversidade de aplicações web disponíveis para as empresas e sua prevalência em todo o mundo, devemos tratar esta área separadamente e focar intensamente nela. Os aplicativos da Web apresentam uma vasta superfície de ataque e geralmente são os principais alvos acessíveis durante os engajamentos de testes de penetração externos; portanto, fortes habilidades de enumeração e exploração da Web são fundamentais.

## Post-Exploitation

Na maioria dos casos, quando exploramos determinados serviços para nossos propósitos de obter acesso ao sistema, geralmente não obtemos os privilégios mais altos possíveis. Como os serviços são normalmente configurados de uma certa maneira "isolados" para impedir invasores em potencial, contornar essas restrições é o próximo passo que damos neste estágio. No entanto, nem sempre é fácil escalar os privilégios. Depois de obter um conhecimento aprofundado sobre o funcionamento desses sistemas operacionais, devemos adaptar nossas técnicas ao sistema operacional específico e estudar cuidadosamente como o Linux Privilege Escalation e o Windows Privilege Escalation funcionam.

![[Pasted image 20230613132210.png]]

A partir desta etapa, existem quatro caminhos que podemos seguir, dependendo de quanto avançamos:
- Information Gathering/Pillaging: Antes de começarmos a escalar privilégios, devemos primeiro obter uma visão geral do funcionamento interno do sistema explorado. Afinal, não sabemos quais usuários estão no sistema e quais opções estão disponíveis para nós até o momento. Esta etapa também é conhecida como pilhagem. Este caminho não é opcional, como os outros, mas essencial. Mais uma vez, entrar no estágio de coleta de informações nos coloca nessa perspectiva. Isso inevitavelmente nos leva ao estágio de avaliação de vulnerabilidade, onde analisamos e avaliamos as informações que encontramos.
- Exploitation: Suponha que encontramos informações confidenciais sobre o sistema e seu conteúdo. Nesse caso, podemos usá-lo para explorar aplicativos ou serviços locais com privilégios mais altos para executar comandos com esses privilégios.
- Lateral Movement: A partir daqui, também podemos pular diretamente para o Movimento Lateral. Isso pode ocorrer em diferentes condições. Se obtivermos os privilégios mais altos em um sistema dual-homed usado para conectar duas redes, provavelmente poderemos usar esse host para começar a enumerar os hosts que não estavam disponíveis anteriormente para nós.
- Proof-Of-Concept: Podemos seguir o último caminho depois de obter os privilégios mais altos, explorando um sistema interno. Claro, não precisamos necessariamente assumir todos os sistemas. No entanto, se obtivermos os privilégios de administrador de domínio em um ambiente do Active Directory, provavelmente poderemos nos mover livremente por toda a rede e executar qualquer ação que possamos imaginar. Assim podemos criar a Prova de Conceito a partir de nossas anotações para detalhar e potencialmente automatizar os caminhos e atividades e disponibilizá-los ao departamento técnico.

Depois de obtermos acesso a um sistema, devemos ser capazes de dar mais passos dentro do sistema. Durante um teste de penetração, os clientes geralmente desejam descobrir até onde um invasor pode ir em sua rede. Existem muitas versões diferentes de sistemas operacionais. Por exemplo, podemos executar Windows XP, Windows 7, 8, 10, 11 e Windows Server 2008, 2012, 2016 e 2019. Existem também distribuições diferentes para sistemas operacionais baseados em Linux, como Ubuntu, Debian, Parrot OS, Arch, Deepin, Redhat, `Pop!_OS` e muitos outros. Não importa em qual desses sistemas entramos, temos que encontrar nosso caminho para contorná-lo e entender os pontos fracos individuais que um sistema pode ter por dentro.

## Lateral Movement

O movimento lateral é um dos componentes essenciais para a movimentação em uma rede corporativa. Podemos usá-lo para sobrepor-se a outros hosts internos e aumentar ainda mais nossos privilégios na sub-rede atual ou em outra parte da rede. Porém, assim como a Pilhagem, a etapa de Movimentação Lateral requer acesso a pelo menos um dos sistemas da rede corporativa. No estágio de exploração, os privilégios obtidos não desempenham um papel crítico em primeira instância, pois também podemos nos mover pela rede sem direitos de administrador.

![[Pasted image 20230613132529.png]]

Existem três caminhos que podemos seguir a partir deste estágio:
- Vulnerability Assessment: Se o teste de penetração ainda não estiver concluído, podemos pular daqui para o estágio de avaliação de vulnerabilidade. Aqui, a informação já obtida na pilhagem é utilizada e analisada para avaliar onde estão a correr os serviços de rede ou aplicações que utilizam um mecanismo de autenticação que podemos explorar.
- Information Gathering / Pillaging: Depois de um movimento lateral bem-sucedido, podemos pular para a Pilhagem mais uma vez. Esta é a coleta de informações locais no sistema de destino que acessamos.
- Proof-Of-Concept: Uma vez que tenhamos feito o último movimento lateral possível e concluído nosso ataque à rede corporativa, podemos resumir as informações e as etapas que coletamos e talvez até automatizar certas seções que demonstram vulnerabilidade às vulnerabilidades que encontramos.

## Proof-Of-Concept

A prova de conceito (POC) é apenas uma prova de que existe uma vulnerabilidade encontrada. Assim que os administradores receberem nosso relatório, eles tentarão confirmar as vulnerabilidades encontradas reproduzindo-as. Afinal, nenhum administrador alterará processos críticos de negócios sem antes confirmar a existência de determinada vulnerabilidade. Uma grande rede pode ter muitos sistemas interoperacionais e dependências que devem ser verificadas após fazer uma alteração, o que pode levar uma quantidade considerável de tempo e dinheiro. Só porque um pentester encontrou uma determinada falha, isso não significa que a organização pode corrigi-la facilmente mudando apenas um sistema, pois isso pode afetar negativamente os negócios. Os administradores devem testar cuidadosamente as correções para garantir que nenhum outro sistema seja afetado negativamente quando uma alteração for introduzida. Os PoCs são enviados junto com a documentação como parte de um teste de penetração de alta qualidade, permitindo que os administradores os usem para confirmar os próprios problemas.

![[Pasted image 20230613132941.png]]

A partir deste estágio, há apenas um caminho que podemos seguir:
- Post-Engagement: Neste ponto, só podemos ir para a fase pós-engajamento, onde otimizamos e melhoramos a documentação e a enviamos ao cliente após uma revisão intensiva.

Quando já temos todas as informações que coletamos e usamos a vulnerabilidade a nosso favor, não é preciso muito esforço para automatizar as etapas individuais para isso.

## Post-Engagement

O estágio Pós-Engajamento também inclui a limpeza dos sistemas que exploramos para que nenhum desses sistemas possa ser explorado usando nossas ferramentas. Por exemplo, deixar um bind shell em um servidor web que não requer autenticação e é fácil de encontrar fará o oposto do que estamos tentando fazer. Desta forma, colocamos a rede em perigo devido ao nosso descuido. Portanto, é essencial remover todo o conteúdo que transferimos para os sistemas durante nosso teste de penetração para que a rede corporativa fique no mesmo estado de antes de nosso teste de penetração. Também devemos anotar quaisquer alterações no sistema, tentativas de exploração bem-sucedidas, credenciais capturadas e arquivos carregados nos apêndices de nosso relatório para que nossos clientes possam verificar isso com quaisquer alertas que recebem para provar que foram resultado de nossas ações de teste e não de um invasor real na rede.

Além disso, temos que reconciliar todas as nossas anotações com a documentação que escrevemos nesse meio tempo para garantir que não pulemos nenhuma etapa e possamos fornecer um relatório abrangente, bem formatado e organizado aos nossos clientes.