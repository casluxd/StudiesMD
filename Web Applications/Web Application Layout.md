
Não há dois aplicativos da web idênticos. As empresas criam aplicativos da Web para uma infinidade de usos e públicos.

Os aplicativos da Web são projetados e programados de maneira diferente, e a infraestrutura de back-end pode ser configurada de várias maneiras diferentes. É importante entender as várias maneiras pelas quais os aplicativos da Web podem ser executados nos bastidores, a estrutura de um aplicativo da Web, seus componentes e como eles podem ser configurados na infraestrutura de uma empresa.

Os layouts de aplicativos da Web consistem em várias camadas diferentes que podem ser resumidas nas três categorias principais a seguir:
- Web Application Infrastructure: Descreve a estrutura dos componentes necessários, como o banco de dados, necessário para que o aplicativo da Web funcione conforme pretendido. Como o aplicativo da Web pode ser configurado para ser executado em um servidor separado, é essencial saber qual servidor de banco de dados ele precisa acessar.
- Web Application Components: Os componentes que compõem um aplicativo da web representam todos os componentes com os quais o aplicativo da web interage. Eles são divididos nas três áreas a seguir: componentes de UI/UX, cliente e servidor.
- Web Application Architecture: A arquitetura compreende todas as relações entre os vários componentes da aplicação web.

## Web Application Infrastructure

Os aplicativos da Web podem usar muitas configurações de infraestrutura diferentes. Estes também são chamados de modelos. Os mais comuns podem ser agrupados nos quatro tipos a seguir:
- Client-Server
- One Server
- Many Servers - One Database
- Many Servers - Many Databases

### Client-Server

Os aplicativos da Web geralmente adotam o modelo cliente-servidor. Um servidor hospeda o aplicativo da Web em um modelo cliente-servidor e o distribui para qualquer cliente que tente acessá-lo.

![[Pasted image 20230609132329.png]]

Neste modelo, as aplicações web possuem dois tipos de componentes, os do front-end, que geralmente são interpretados e executados no lado do cliente (navegador), e os componentes do back-end, geralmente compilados, interpretados e executados pelo servidor de hospedagem.

Quando um cliente visita o URL do aplicativo da web (endereço da web, ou seja, https://www.acme.local), o servidor usa a interface principal do aplicativo da web (IU). Depois que o usuário clica em um botão ou solicita uma função específica, o navegador envia uma solicitação da Web HTTP ao servidor, que interpreta esta solicitação e executa a(s) tarefa(s) necessária(s) para concluir a solicitação (ou seja, fazer login do usuário, adicionar um item ao carrinho de compras, navegar para outra página, etc.). Assim que o servidor tiver os dados necessários, ele enviará o resultado de volta ao navegador do cliente, exibindo o resultado de forma legível por humanos.

No entanto, embora a maioria dos aplicativos da Web utilize uma arquitetura front-back-end cliente-servidor, há muitas implementações de design.

### One Server

Nesta arquitetura, toda a aplicação web ou mesmo várias aplicações web e seus componentes, incluindo o banco de dados, são hospedados em um único servidor. Embora esse projeto seja direto e fácil de implementar, também é o projeto mais arriscado.

![[Pasted image 20230609132522.png]]

Se qualquer aplicativo da web hospedado neste servidor for comprometido nessa arquitetura, todos os dados dos aplicativos da web serão comprometidos. Esse design representa uma abordagem "todos os ovos na mesma cesta", pois se qualquer um dos aplicativos da Web hospedados estiver vulnerável, todo o servidor da Web ficará vulnerável.

Além disso, se o servidor da Web cair por qualquer motivo, todos os aplicativos da Web hospedados ficarão totalmente inacessíveis até que o problema seja resolvido.

### Many Server - One Database

Esse modelo separa o banco de dados em seu próprio servidor de banco de dados e permite que o servidor de hospedagem de aplicativos da web acesse o servidor de banco de dados para armazenar e recuperar dados. Pode ser visto como muitos servidores para um banco de dados e um servidor para um banco de dados, desde que o banco de dados esteja separado em seu próprio servidor de banco de dados.

![[Pasted image 20230609133004.png]]

Este modelo pode permitir que vários aplicativos da web acessem um único banco de dados para ter acesso aos mesmos dados sem sincronizar os dados entre eles. Os aplicativos da web podem ser replicações de um aplicativo principal (ou seja, primário/backup) ou podem ser aplicativos da web separados que compartilham dados comuns.

A principal vantagem deste modelo (do ponto de vista de segurança) é a segmentação, onde cada um dos principais componentes de uma aplicação web está localizado e hospedado separadamente. No caso de um servidor web ser comprometido, outros servidores web não são afetados diretamente. Da mesma forma, se o banco de dados for comprometido (ou seja, por meio de uma vulnerabilidade de injeção de SQL), o próprio aplicativo da Web não será afetado diretamente. Ainda existem medidas de controle de acesso que precisam ser implementadas após a segmentação de ativos, como limitar o acesso de aplicativos da Web apenas aos dados necessários para funcionar conforme o pretendido.

### Many Servers - Many Database

Este modelo baseia-se no modelo Many Servers, One Database. No entanto, dentro do servidor de banco de dados, os dados de cada aplicativo da web são hospedados em um banco de dados separado. O aplicativo da web só pode acessar dados privados e apenas dados comuns que são compartilhados entre aplicativos da web. Também é possível hospedar o banco de dados de cada aplicativo da Web em seu servidor de banco de dados separado.

![[Pasted image 20230609140820.png]]

Esse design também é amplamente utilizado para fins de redundância, portanto, se qualquer servidor da Web ou banco de dados ficar offline, um backup será executado em seu lugar para reduzir o tempo de inatividade o máximo possível. Embora isso possa ser mais difícil de implementar e exigir ferramentas como balanceadores de carga para funcionar adequadamente, essa arquitetura é uma das melhores escolhas em termos de segurança devido às suas adequadas medidas de controle de acesso e adequada segmentação de ativos.

Além desses modelos, existem outros modelos de aplicativos da Web disponíveis, como aplicativos da Web sem servidor ou aplicativos da Web que utilizam microsserviços.

### Web Application Components

Cada aplicativo da Web pode ter um número diferente de componentes. No entanto, todos os componentes dos modelos mencionados anteriormente podem ser divididos em:
1. Client
2. Server: Webserver, Web Application Logic, Database
3. Services: 3rd Party Integrations, Web Application Integrations
4. Functions (Serverless)

### Web Application Architecture

Os componentes de um aplicativo da Web são divididos em três camadas diferentes (AKA Three Tier Architecture).
- Presentation Layer: Consiste em componentes de processo de interface do usuário que permitem a comunicação com o aplicativo e o sistema. Eles podem ser acessados ​​pelo cliente por meio do navegador da Web e são retornados na forma de HTML, JavaScript e CSS.
- Application Layer: Essa camada garante que todas as solicitações do cliente (solicitações da web) sejam processadas corretamente. Vários critérios são verificados, como autorização, privilégios e dados repassados ​​ao cliente.
- Data Layer: A camada de dados trabalha em estreita colaboração com a camada de aplicativos para determinar exatamente onde os dados necessários são armazenados e podem ser acessados.

![[Pasted image 20230609141106.png]]

Podemos pensar em microsserviços como componentes independentes da aplicação web, que na maioria das vezes são programados para uma única tarefa. Por exemplo, para uma loja online, podemos decompor as tarefas principais nos seguintes componentes:
- Registration
- Search
- Payments
- Ratings
- Reviews

Esses componentes se comunicam com o cliente e entre si. A comunicação entre esses microsserviços é sem estado, o que significa que a solicitação e a resposta são independentes. Isso ocorre porque os dados armazenados são armazenados separadamente dos respectivos microsserviços. O uso de microsserviços é considerado arquitetura orientada a serviços (SOA), construída como uma coleção de diferentes funções automatizadas focadas em um único objetivo de negócio. No entanto, esses microsserviços dependem uns dos outros.

Outro componente de microsserviço essencial e eficiente é que eles podem ser escritos em diferentes linguagens de programação e ainda assim interagir. Os microsserviços se beneficiam de um dimensionamento mais fácil e um desenvolvimento mais rápido de aplicativos, o que incentiva a inovação e acelera a entrega de novos recursos ao mercado. Alguns benefícios dos microsserviços incluem:

- Agilidade
- Escala flexível
- Deploy fácil
- Código reusável
- Resiliência

### Serverless

Provedores de nuvem como AWS, GCP, Azure, entre outros, oferecem arquiteturas serverless. Essas plataformas fornecem estruturas de aplicativos para criar esses aplicativos da Web sem a necessidade de se preocupar com os próprios servidores. Esses aplicativos da Web são executados em contêineres de computação sem estado (Docker, por exemplo). Esse tipo de arquitetura oferece à empresa a flexibilidade de criar e implantar aplicativos e serviços sem precisar gerenciar a infraestrutura; todo o gerenciamento do servidor é feito pelo provedor de nuvem, que elimina a necessidade de provisionar, escalar e manter os servidores necessários para executar aplicativos e bancos de dados.

### Architecture Security

Compreender a arquitetura geral dos aplicativos da web e o design específico de cada aplicativo da web é importante ao realizar um teste de penetração em qualquer aplicativo da web. Em muitos casos, a vulnerabilidade de um aplicativo da Web individual pode não ser necessariamente causada por um erro de programação, mas por um erro de design em sua arquitetura.

Por exemplo, um aplicativo da Web individual pode ter todas as suas principais funcionalidades implementadas com segurança. No entanto, devido à falta de medidas adequadas de controle de acesso em seu projeto, ou seja, com o uso do controle de acesso baseado em função (RBAC), os usuários podem acessar alguns recursos administrativos que não devem ser acessados ​​diretamente por eles ou até mesmo acessar informações privadas de outros usuários sem ter os privilégios para fazê-lo. Para corrigir esse tipo de problema, uma alteração significativa no projeto precisaria ser implementada, o que provavelmente seria caro e demorado.

Outro exemplo seria se não pudéssemos encontrar o banco de dados depois de explorar uma vulnerabilidade e obter controle sobre o servidor de back-end, o que pode significar que o banco de dados está hospedado em um servidor separado. Podemos encontrar apenas parte dos dados do banco de dados, o que pode significar que existem vários bancos de dados em uso. É por isso que a segurança deve ser considerada em cada fase do desenvolvimento de aplicativos da Web e os testes de penetração devem ser realizados durante todo o ciclo de vida de desenvolvimento de aplicativos da Web.