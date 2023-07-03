
Nas ciências sociais, um processo é considerado um termo para a sequência dirigida de eventos. Em um contexto operacional e organizacional, os processos são referidos mais precisamente como processos de trabalho, negócios, produção ou criação de valor. Processos são outro nome para programas executados em sistemas de computador, que geralmente são partes do software do sistema.

Também é essencial distinguir entre processos determinísticos e estocásticos. Um processo determinístico é um processo no qual cada estado é causalmente dependente e determinado por outros estados e eventos anteriores. Um processo estocástico é aquele em que um estado segue de outros estados apenas com uma certa probabilidade. Aqui, apenas condições estatísticas podem ser assumidas. Para nós, várias das definições acima se sobrepõem. Usamos a definição do processo de teste de penetração das ciências sociais para representar um curso de eventos conectados com processos determinísticos. Isso ocorre porque todos os nossos passos são baseados nos eventos e resultados que podemos descobrir ou provocar.

Um processo de teste de penetração é definido por etapas e eventos sucessivos executados pelo testador de penetração para encontrar um caminho para o objetivo predefinido.

Os processos descrevem uma sequência específica de operações dentro de um determinado período de tempo que leva ao resultado desejado. Também é fundamental observar que os processos não representam uma receita fixa e não são um guia passo a passo. Nossos processos de teste de penetração devem, portanto, ser grosseiros e flexíveis. Afinal, cada cliente tem uma infraestrutura, desejos e expectativas únicos.

# Penetration Testing Stages

A maneira mais eficaz de representá-los e defini-los é por meio de estágios interdependentes. Muitas vezes descobrimos em nossas pesquisas que esses processos são apresentados na forma de um processo circular. Se olharmos para isso mais de perto e imaginarmos que mesmo um único componente do processo circular não se aplica, todo o processo é interrompido. Estritamente definido, todo o processo falha. Se assumirmos que iniciamos esse processo desde o início, mas já com as informações recém-adquiridas, já é uma nova abordagem de processo que não desfaz a anterior.

O problema é que, com essas representações e abordagens, geralmente não há nada a que recorrer para estender nosso processo de teste de penetração. Como discutimos, não há um guia passo a passo que possamos seguir, mas etapas que permitem que as etapas e abordagens individuais sejam variadas com flexibilidade e adaptadas aos resultados e informações que recebemos. Podemos desenvolver nosso próprio manual para várias coisas que tentamos em diferentes estágios de um teste de penetração, mas cada ambiente é diferente e, portanto, precisamos nos adaptar constantemente.

Entraremos em cada um desses estágios com mais detalhes e abordaremos as especificidades de cada um nas seções posteriores e veremos um plano de estudo opcional sobre como proceder para aprender as muitas táticas, técnicas e procedimentos (TTPs), usando uma estrutura para mostrar como cada estágio se baseia no outro e também pode ser iterativo por natureza. Primeiro, vamos examinar os componentes gerais do processo de teste de penetração e discutir os módulos individuais e por que eles são tão importantes.

Este plano de estudo opcional é baseado em conjuntos de módulos para cada estágio que recomendamos trabalhar antes de passar para o próximo estágio. Trabalharemos em diferentes fases em quase todos os módulos, realizando etapas como Coleta de Informações, Movimento Lateral e Pilhagem repetidamente. A separação dos módulos é pensada para focar no tema, que exige conhecimentos específicos que não devem ser omitidos. Lacunas em qualquer um desses conhecimentos, mesmo que pensemos estar familiarizados com eles, podem levar a mal-entendidos ou dificuldades no curso do estudo. Assim, o processo de teste de penetração com suas etapas é o seguinte:

## Pre-Engagement

Pré-engajamento é educar o cliente e ajustar o contrato. Todos os testes necessários e seus componentes são estritamente definidos e registrados contratualmente. Em uma reunião presencial ou teleconferência, muitos arranjos são feitos, como:
- Acordo de não divulgação
- Objetivos
- Escopo
- Estimativa de tempo
- Regras de engajamento

## Information Gathering

A coleta de informações descreve como obtemos informações sobre os componentes necessários de várias maneiras. Buscamos informações sobre a empresa-alvo e o software e hardware em uso para encontrar possíveis brechas de segurança que possamos aproveitar para uma posição segura.

## Vulnerability Assessment

Uma vez que chegamos ao estágio de Avaliação de Vulnerabilidade, analisamos os resultados de nosso estágio de Coleta de Informações, procurando por vulnerabilidades conhecidas nos sistemas, aplicativos e várias versões de cada um para descobrir possíveis vetores de ataque. Avaliação de vulnerabilidade é a avaliação de vulnerabilidades potenciais, tanto manualmente quanto por meios automatizados. Isso é usado para determinar o nível de ameaça e a suscetibilidade da infraestrutura de rede de uma empresa a ataques cibernéticos.

## Exploitation

No estágio de exploração, usamos os resultados para testar nossos ataques contra os vetores potenciais e executá-los contra os sistemas de destino para obter acesso inicial a esses sistemas.

## Post-Exploitation

Nesta fase do teste de penetração, já temos acesso à máquina explorada e garantimos que ainda temos acesso a ela, mesmo que sejam feitas modificações e alterações. Durante esta fase, podemos tentar aumentar nossos privilégios para obter os direitos mais altos possíveis e buscar dados confidenciais, como credenciais ou outros dados que o cliente esteja preocupado em proteger (pilhagem). Às vezes, realizamos pós-exploração para demonstrar a um cliente o impacto de nosso acesso. Outras vezes, realizamos a pós-exploração como uma entrada para o processo de movimento lateral descrito a seguir.

## Lateral Movement

O movimento lateral descreve o movimento dentro da rede interna de nossa empresa-alvo para acessar hosts adicionais com o mesmo nível de privilégio ou superior. Muitas vezes, é um processo iterativo combinado com atividades pós-exploração até atingirmos nosso objetivo. Por exemplo, ganhamos uma base em um servidor web, escalamos privilégios e encontramos uma senha no registro. Realizamos uma enumeração adicional e vemos que essa senha funciona para acessar um servidor de banco de dados como um usuário administrador local. A partir daqui, podemos pilhar dados confidenciais do banco de dados e encontrar outras credenciais para aprofundar nosso acesso à rede. Nesta etapa, normalmente usaremos muitas técnicas com base nas informações encontradas no host ou servidor explorado.

## Proof-Of-Concept

Nesta etapa, documentamos, passo a passo, as etapas que realizamos para atingir o comprometimento da rede ou algum nível de acesso. Nosso objetivo é mostrar como conseguimos encadear vários pontos fracos para atingir nosso objetivo, para que eles possam ver uma imagem clara de como cada vulnerabilidade se encaixa e ajudar a priorizar seus esforços de remediação. Se não documentarmos bem nossas etapas, é difícil para o cliente entender o que conseguimos fazer e, assim, dificultar seus esforços de remediação. Se possível, poderíamos criar um ou mais scripts para automatizar as etapas que executamos para ajudar nosso cliente a reproduzir nossas descobertas. Cobrimos isso em profundidade no módulo Documentação e relatórios.

## Post-Engagement

Durante o pós-engajamento, uma documentação detalhada é preparada para administradores e gerenciamento da empresa cliente para entender a gravidade das vulnerabilidades encontradas. Nesta fase, também limpamos todos os vestígios de nossas ações em todos os hosts e servidores. Durante esta fase, criamos os resultados para o nosso cliente, realizamos uma reunião de apresentação do relatório e, às vezes, fazemos uma apresentação executiva para os executivos da empresa-alvo ou seu conselho de administração. Por fim, arquivaremos nossos dados de teste de acordo com nossas obrigações contratuais e política da empresa. Normalmente reteremos esses dados por um período definido ou até realizarmos uma avaliação pós-remediação (novo teste) para testar as correções do cliente.

# Importance

Devemos internalizar esse procedimento e usá-lo como base para todos os nossos compromissos técnicos. Os componentes de cada etapa nos permitem entender com precisão em quais áreas precisamos melhorar e onde estão as nossas maiores dificuldades e lacunas de conhecimento. Por exemplo, podemos pensar em um site como um alvo que precisamos estudar.

1. Pre-Engagement: O primeiro passo é criar todos os documentos necessários na fase de pré-contratação, discutir os objetivos da avaliação e esclarecer qualquer dúvida.
2. Information Gathering: Uma vez concluídas as atividades de pré-contratação, investigamos o site existente da empresa que fomos designados para avaliar. Identificamos as tecnologias em uso e aprendemos como o aplicativo da web funciona.
3. Vulnerability Assessment: Com essas informações, podemos procurar vulnerabilidades conhecidas e investigar recursos questionáveis ​​que podem permitir ações não intencionais.
4. Exploitation: Depois de encontrar vulnerabilidades em potencial, preparamos nosso código de exploração, ferramentas e ambiente e testamos o servidor da Web em busca dessas vulnerabilidades em potencial.
5. Post-Exploitation: Depois de explorar o alvo com sucesso, passamos à coleta de informações e examinamos o servidor da web por dentro. Se encontrarmos informações confidenciais durante esta etapa, tentamos escalar nossos privilégios (dependendo do sistema e das configurações).
6. Lateral Movement: Se outros servidores e hosts na rede interna estiverem no escopo, tentamos nos mover pela rede e acessar outros hosts e servidores usando as informações que coletamos.
7. Proof-Of-Concept: Criamos uma prova de conceito que prova que essas vulnerabilidades existem e potencialmente até automatizamos as etapas individuais que acionam essas vulnerabilidades.
8. Post-Engagement: Por fim, a documentação é concluída e apresentada ao nosso cliente como um relatório formal a ser entregue. Posteriormente, podemos realizar uma reunião de apresentação do relatório para esclarecer qualquer coisa sobre nossos testes ou resultados e fornecer qualquer suporte necessário ao pessoal encarregado de corrigir nossas descobertas.