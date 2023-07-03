Processos complexos devem ter uma metodologia padronizada que nos ajude a manter o rumo e evitar omitir aspectos por engano. Especialmente com a variedade de casos que os sistemas de destino podem nos oferecer, é quase imprevisível como nossa abordagem deve ser projetada. Portanto, a maioria dos testadores de penetração segue seus hábitos e as etapas com as quais se sente mais confortável e familiarizada. No entanto, essa não é uma metodologia padronizada, mas uma abordagem baseada na experiência.

Sabemos que o teste de penetração e, portanto, a enumeração, é um processo dinâmico. Consequentemente, desenvolvemos uma metodologia de enumeração estática para testes de penetração externa e interna que inclui dinâmica livre e permite uma ampla gama de mudanças e adaptações ao ambiente especificado. Essa metodologia está aninhada em 6 camadas e representa, metaforicamente falando, limites que tentamos passar com o processo de enumeração. Todo o processo de enumeração é dividido em três níveis diferentes:
- Enumeração baseada em Infraestrutura
- Enumeração baseada em Host
- Enumeração baseada em OS


![[Pasted image 20230617234448.png]]

`Nota: Os componentes de cada camada mostrada representam as principais categorias e não uma lista completa de todos os componentes a serem pesquisados. Além disso, deve-se mencionar aqui que a primeira e a segunda camada ( Presença da Internet, Gateway ) não se aplicam exatamente à intranet, como uma infraestrutura do Active Directory. As camadas para infraestrutura interna serão cobertas por outros módulos.`

Considere essas linhas como algum tipo de obstáculo, como uma parede, por exemplo. O que fazemos aqui é olhar em volta para descobrir onde fica a entrada ou a lacuna pela qual podemos passar ou subir para nos aproximarmos do nosso objetivo. Teoricamente, também é possível atravessar a parede de frente, mas com muita frequência, acontece que o local em que quebramos a lacuna com muito esforço e tempo com força não nos traz muito, porque não há entrada neste ponto do muro para passar para a próxima parede.

Essas camadas são projetadas da seguinte maneira:

|**Camada**|**Descrição**|**Categorias de informação**|
|---|---|---|
|`1. Internet Presence`|Identificação da presença na Internet e infraestrutura acessível externamente.|Domínios, subdomínios, vHosts, ASN, Netblocks, endereços IP, instâncias em nuvem, medidas de segurança|
|`2. Gateway`|Identifique as possíveis medidas de segurança para proteger a infraestrutura externa e interna da empresa.|Firewalls, DMZ, IPS / IDS, EDR, Proxies, NAC, Segmentação de rede, VPN, Cloudflare|
|`3. Accessible Services`|Identifique interfaces e serviços acessíveis hospedados externa ou internamente.|Tipo de serviço, Funcionalidade, Configuração, Porta, Versão, Interface|
|`4. Processes`|Identifique os processos internos, fontes e destinos associados aos serviços.|PID, Dados processados, Tarefas, Fonte, Destino|
|`5. Privileges`|Identificação das permissões e privilégios internos aos serviços acessíveis.|Grupos, Usuários, Permissões, Restrições, Meio Ambiente|
|`6. OS Setup`|Identificação da configuração interna de componentes e sistemas.|Tipo de sistema operacional, nível de patch, configuração de rede, ambiente do sistema operacional, arquivos de configuração, arquivos privados confidenciais|

Nota importante: O aspecto humano e as informações que podem ser obtidas pelos funcionários que usam o OSINT foram removidos da camada "Presença na Internet" por simplicidade.

Finalmente, podemos imaginar todo o teste de penetração na forma de um labirinto, onde precisamos identificar as lacunas e encontrar o caminho para nos levar para dentro o mais rápido e eficaz possível. Esse tipo de labirinto pode se parecer com isso:

![[Pasted image 20230617233259.png]]


Os quadrados representam as lacunas / vulnerabilidades.

Como provavelmente já notamos, podemos ver que encontraremos uma lacuna e muito provavelmente várias. O fato interessante e muito comum é que nem todas as lacunas que encontramos podem nos levar para dentro. Todos os testes de penetração são limitados no tempo, mas devemos sempre ter em mente que existe quase sempre uma maneira de entrar. Mesmo após um teste de penetração de quatro semanas, não podemos dizer 100% que não há mais vulnerabilidades. Alguém que estuda a empresa há meses e os analisa provavelmente terá uma compreensão muito maior dos aplicativos e da estrutura do que conseguimos obter nas poucas semanas gastamos na avaliação. Um exemplo excelente e recente disso é o [ataque cibernético ao SolarWinds](https://www.rpc.senate.gov/policy-papers/the-solarwinds-cyberattack), o que aconteceu não faz muito tempo. Essa é outra excelente razão para uma metodologia que deve excluir esses casos.

Vamos supor que nos pediram para realizar um teste de penetração externo de "caixa preta. Depois que todos os itens de contrato necessários forem completamente cumpridos, nosso teste de penetração começará no momento especificado.

## Camada No.1: Presença na Internet

A primeira camada que precisamos passar é a camada "Presença da Internet", onde nos concentramos em encontrar os alvos que podemos investigar. Se o escopo do contrato nos permitir procurar hosts adicionais, essa camada será ainda mais crítica do que apenas para destinos fixos. Nesta camada, usamos diferentes técnicas para encontrar domínios, subdomínios, blocos de rede e muitos outros componentes e informações que apresentam a presença da empresa e sua infraestrutura na Internet.

`O objetivo dessa camada é identificar todos os possíveis sistemas de destino e interfaces que podem ser testados.`

## Camada No.2: Gateway

Aqui tentamos entender a interface do destino acessível, como ele é protegido e onde está localizado na rede. Devido à diversidade, diferentes funcionalidades e alguns procedimentos específicos, entraremos em mais detalhes sobre essa camada em outros módulos.

`O objetivo é entender com o que estamos lidando e com o que devemos estar atentos.`

## Camada No.3: Serviços Acessíveis

No caso de serviços acessíveis, examinamos cada destino em todos os serviços que ele oferece. Cada um desses serviços tem uma finalidade específica que foi instalada por um motivo específico pelo administrador. Cada serviço possui certas funções, que também levam a resultados específicos. Para trabalhar efetivamente com eles, precisamos saber como eles funcionam. Caso contrário, precisamos aprender a entendê-los.

`Essa camada visa entender o motivo e a funcionalidade do sistema de destino e obter o conhecimento necessário para se comunicar com ele e explorá-lo para nossos propósitos de maneira eficaz.`

Essa é a parte da enumeração com a qual trataremos principalmente neste módulo.

## Camada No.4: Processos

Sempre que um comando ou função é executado, os dados são processados, inseridos pelo usuário ou gerados pelo sistema. Isso inicia um processo que precisa executar tarefas específicas e essas tarefas têm pelo menos uma fonte e um destino.

`O objetivo aqui é entender esses fatores e identificar as dependências entre eles.`

## Camada No.5: Privilégios

Cada serviço é executado através de um usuário específico em um grupo específico, com permissões e privilégios definidos pelo administrador ou pelo sistema. Esses privilégios geralmente nos fornecem funções que os administradores ignoram. Isso geralmente acontece nas infraestruturas do Active Directory e em muitos outros ambientes e servidores de administração específicos de casos, nos quais os usuários são responsáveis por várias áreas de administração.

`É crucial identificá-los e entender o que é e o que não é possível com esses privilégios.`

## Camada No.6: Configuração do SO

Aqui, coletamos informações sobre o sistema operacional real e sua configuração usando acesso interno. Isso nos fornece uma boa visão geral da segurança interna dos sistemas e reflete as habilidades e capacidades das equipes administrativas da empresa.

`O objetivo aqui é ver como os administradores gerenciam os sistemas e quais informações internas confidenciais podemos extrair deles.`

## Metodologia de Enumeração na Prática

Uma metodologia resume todos os procedimentos sistemáticos na obtenção de conhecimento dentro dos limites de um determinado objetivo. É importante observar que uma metodologia não é um guia passo a passo, mas, como a definição implica, um resumo dos procedimentos sistemáticos. No nosso caso, a metodologia de enumeração é a abordagem sistemática para explorar um determinado objetivo.

Como os componentes individuais são identificados e as informações obtidas nessa metodologia são um aspecto dinâmico e crescente que muda constantemente e, portanto, pode diferir. Um excelente exemplo disso é o uso de ferramentas de coleta de informações de servidores da web. Existem inúmeras ferramentas diferentes para isso, e cada uma delas tem um foco específico e, portanto, fornece resultados individuais que diferem de outras aplicações. O objetivo, no entanto, é o mesmo. Portanto, a coleção de ferramentas e comandos não faz parte da metodologia real, mas uma folha de dicas a que podemos nos referir usando os comandos e ferramentas listados em determinados casos.
