
Aplicativos da Web são aplicativos interativos executados em navegadores da Web. Os aplicativos da Web geralmente adotam uma arquitetura cliente-servidor para executar e lidar com as interações. Eles normalmente têm componentes de front-end (ou seja, a interface do site ou "o que o usuário vê") que são executados no lado do cliente (navegador) e outros componentes de back-end (código-fonte do aplicativo da web) que são executados no lado do servidor (servidor/bancos de dados de back-end).

Isso permite que as organizações hospedem aplicativos poderosos com controle quase completo em tempo real sobre seu design e funcionalidade, ao mesmo tempo em que podem ser acessados ​​em todo o mundo. Alguns exemplos de aplicativos da Web típicos incluem serviços de e-mail on-line como o Gmail, varejistas on-line como a Amazon e processadores de texto on-line como o Google Docs.

![[Pasted image 20230606183014.png]]

Os aplicativos da Web não são exclusivos de provedores gigantes como Google ou Microsoft, mas podem ser desenvolvidos por qualquer desenvolvedor da Web e hospedados online em qualquer um dos serviços de hospedagem comuns, para serem usados ​​por qualquer pessoa na Internet. É por isso que hoje temos milhões de aplicativos da Web em toda a Internet, com bilhões de usuários interagindo com eles todos os dias.

### Web Applications vs Websites

No passado, interagíamos com sites que eram estáticos e não podiam ser alterados em tempo real. Isso significa que sites tradicionais foram criados estaticamente para representar informações específicas, e essas informações não mudariam com nossa interação. Para alterar o conteúdo do site, a página correspondente deve ser editada manualmente pelos desenvolvedores. Esses tipos de páginas estáticas não contêm funções e, portanto, não produzem alterações em tempo real. Esse tipo de site também é conhecido como Web 1.0.

![[Pasted image 20230606183112.png]]

Por outro lado, a maioria dos sites executa aplicativos da Web ou Web 2.0 apresentando conteúdo dinâmico baseado na interação do usuário. Outra diferença significativa é que os aplicativos da Web são totalmente funcionais e podem executar várias funcionalidades para o usuário final, enquanto os sites da Web não possuem esse tipo de funcionalidade. Outras diferenças importantes entre sites tradicionais e aplicativos da web incluem:
- Sendo modular
- Executando em qualquer tamanho de tela
- Executando em qualquer plataforma sem ser otimizado

## Aplicativos da Web versus aplicativos de sistema operacional nativo

Ao contrário dos aplicativos de sistema operacional nativo (SO nativo), os aplicativos da Web são independentes de plataforma e podem ser executados em um navegador em qualquer sistema operacional. Esses aplicativos da web não precisam ser instalados no sistema de um usuário porque esses aplicativos da web e suas funcionalidades são executadas remotamente no servidor remoto e, portanto, não consomem nenhum espaço no disco rígido do usuário final.

Outra vantagem dos aplicativos da Web em relação aos aplicativos do sistema operacional nativo é a unidade de versão. Todos os usuários que acessam um aplicativo da web usam a mesma versão e o mesmo aplicativo da web, que pode ser continuamente atualizado e modificado sem enviar atualizações para cada usuário. Aplicações web podem ser atualizadas em um único local (webserver) sem precisar desenvolver builds diferentes para cada plataforma, o que reduz drasticamente os custos de manutenção e suporte, eliminando a necessidade de comunicar as alterações a todos os usuários individualmente.

Mais recentemente, no entanto, aplicativos da Web híbridos e progressivos estão se tornando mais comuns. Eles utilizam estruturas modernas para executar aplicativos da web usando capacidades e recursos nativos do sistema operacional, tornando-os mais rápidos do que os aplicativos da web comuns e mais capazes.

### Web Application Distribution

Existem muitos aplicativos da Web de código aberto usados ​​por organizações em todo o mundo que podem ser personalizados para atender às necessidades de cada organização. Alguns aplicativos da Web de código aberto comuns incluem:
- Wordpress
- OpenCart
- Joomla

Existem também aplicativos da Web proprietários de 'código fechado', que geralmente são desenvolvidos por uma determinada organização e depois vendidos para outra organização ou usados ​​por organizações por meio de um modelo de plano de assinatura. Algumas aplicações web comuns de código fechado incluem:
- Wix
- Shopify
- DotNetNuke

## Security Risks of Web Applications

Os ataques a aplicativos da Web são predominantes e representam um desafio para a maioria das organizações com presença na Web, independentemente de seu tamanho. Afinal, eles geralmente são acessíveis de qualquer país por qualquer pessoa com uma conexão à Internet e um navegador da Web e geralmente oferecem uma vasta superfície de ataque. Existem muitas ferramentas automatizadas para escanear e atacar aplicativos da web que, nas mãos erradas, podem causar danos significativos. À medida que os aplicativos da Web se tornam mais complicados e avançados, aumenta também a possibilidade de vulnerabilidades críticas serem incorporadas ao seu design.

Um ataque de aplicativo da web bem-sucedido pode levar a perdas significativas e interrupções maciças nos negócios. Como os aplicativos da Web são executados em servidores que podem hospedar outras informações confidenciais e geralmente também estão vinculados a bancos de dados que contêm dados corporativos ou de usuários confidenciais, todos esses dados podem ser comprometidos se um site for atacado com sucesso. É por isso que é fundamental para qualquer empresa que utiliza aplicativos da Web testar adequadamente esses aplicativos em busca de vulnerabilidades e corrigi-los imediatamente ao testar que o patch corrige a falha e não introduz inadvertidamente nenhuma nova falha.

O teste de penetração de aplicativos da Web é uma habilidade cada vez mais crítica a ser aprendida. Qualquer organização que procura proteger seus aplicativos da Web voltados para a Internet (e internos) deve passar por testes frequentes de aplicativos da Web e implementar práticas de codificação seguras em cada estágio do ciclo de vida do desenvolvimento. Para fazer o pentest de aplicações web, precisamos entender como elas funcionam, como são desenvolvidas e que tipo de risco está em cada camada e componente da aplicação dependendo das tecnologias em uso.

Sempre encontraremos vários aplicativos da Web projetados e configurados de maneira diferente. Um dos métodos mais atuais e amplamente utilizados para testar aplicativos da Web é o OWASP Web Security Testing Guide.

Um dos procedimentos mais comuns é começar revisando os componentes front-end de um aplicativo da Web, como HTML, CSS e JavaScript (também conhecido como trindade front-end) e tentar encontrar vulnerabilidades, como exposição de dados confidenciais e script entre sites (XSS). Depois que todos os componentes de front-end são testados minuciosamente, normalmente revisamos a funcionalidade principal do aplicativo da web e a interação entre o navegador e o servidor da web para enumerar as tecnologias o servidor web usa e procure por falhas exploráveis. Normalmente, avaliamos aplicativos da Web de uma perspectiva não autenticada e autenticada (se o aplicativo tiver a funcionalidade de login) para maximizar a cobertura e revisar todos os cenários de ataque possíveis.

### Attacking Web Applications

Atualmente, quase todas as empresas, independentemente do tamanho, possuem um ou mais aplicativos da Web em seu perímetro externo. Esses aplicativos podem ser desde sites estáticos simples a blogs alimentados por sistemas de gerenciamento de conteúdo (CMS), como o WordPress, até aplicativos complicados com funcionalidade de inscrição/login suportando várias funções de usuário, desde usuários básicos até superadministradores. Hoje em dia, não é muito comum encontrar um host externo diretamente explorável por meio de uma exploração pública conhecida (como um serviço vulnerável ou vulnerabilidade de execução remota de código (RCE) do Windows), embora isso aconteça. Os aplicativos da Web fornecem uma vasta superfície de ataque e sua natureza dinâmica significa que eles estão em constante mudança (e negligenciados!). Uma alteração de código relativamente simples pode introduzir uma vulnerabilidade catastrófica ou uma série de vulnerabilidades que podem ser encadeadas juntas para obter acesso a dados confidenciais ou execução remota de código no servidor da web ou outros hosts no ambiente, como servidores de banco de dados.

Não é incomum encontrar falhas que podem levar diretamente à execução do código, como um formulário de upload de arquivo que permite o upload de código malicioso ou uma vulnerabilidade de inclusão de arquivo que pode ser aproveitada para obter a execução remota de código. Uma vulnerabilidade bem conhecida que ainda prevalece em vários tipos de aplicativos da Web é a injeção de SQL. Esse tipo de vulnerabilidade surge do manuseio inseguro da entrada fornecida pelo usuário. Isso pode resultar em acesso a dados confidenciais, leitura/gravação de arquivos no servidor de banco de dados e até mesmo execução remota de código.

Muitas vezes encontramos vulnerabilidades de injeção de SQL em aplicativos da Web que usam o Active Directory para autenticação. Embora geralmente não possamos aproveitar isso para extrair senhas (já que o Active Directory as administra), muitas vezes, podemos obter a maioria ou todos os endereços de e-mail de usuários do Active Directory, que geralmente são iguais aos seus nomes de usuário. Esses dados podem ser usados ​​para executar um ataque de pulverização de senha contra portais da web que usam o Active Directory para autenticação, como VPN ou Microsoft Outlook Web Access/Microsoft O365. Um spray de senha bem-sucedido geralmente pode resultar em acesso a dados confidenciais, como e-mail ou até mesmo em um ponto de apoio direto no ambiente de rede corporativa.

Este exemplo mostra o dano que pode surgir de uma única vulnerabilidade de aplicativo da Web, especialmente quando "encadeado" para extrair dados de um aplicativo que pode ser usado para atacar outras partes da infraestrutura externa de uma empresa. Um profissional de infosec completo deve ter uma compreensão profunda de aplicativos da web e se sentir tão confortável atacando aplicativos da web quanto executando testes de penetração de rede e ataques do Active Directory. Um testador de penetração com uma base sólida em aplicativos da Web geralmente pode se diferenciar de seus pares e encontrar falhas que outros podem ignorar. Mais alguns exemplos do mundo real de ataques a aplicativos da Web e o impacto são os seguintes:
- SQL Injection: Obtenção de nomes de usuário do Active Directory e execução de um ataque de pulverização de senha contra uma VPN ou portal de e-mail.
- File Inclusion: Lendo o código-fonte para encontrar uma página ou diretório oculto que expõe funcionalidades adicionais que podem ser usadas para obter a execução remota do código.
- Unrestricted File Upload: Um aplicativo da web que permite que um usuário carregue uma foto de perfil que permite que qualquer tipo de arquivo seja carregado (não apenas imagens). Isso pode ser aproveitado para obter controle total do servidor de aplicativos da web, carregando código malicioso.
- Insecure Direct Object Referencing (IDOR): Quando combinado com uma falha, como controle de acesso quebrado, isso geralmente pode ser usado para acessar os arquivos ou a funcionalidade de outro usuário. Um exemplo seria editar seu perfil de usuário navegando em uma página como /user/701/edit-profile. Se pudermos mudar o 701 para 702, podemos editar o perfil de outro usuário!
- Broken Access Control: Outro exemplo é um aplicativo que permite que um usuário registre uma nova conta. Se a funcionalidade de registro da conta for mal projetada, um usuário pode executar a escalação de privilégios ao se registrar. Considere a solicitação POST ao registrar um novo usuário, que envia os dados `username=bjones&password=Welcome1&email=bjones@inlanefreight.local&roleid=3`. E se pudermos manipular o parâmetro roleid e alterá-lo para 0 ou 1? Vimos aplicativos do mundo real onde esse foi o caso e foi possível registrar rapidamente um usuário administrador e acessar muitos recursos não intencionais do aplicativo da web.

Comece a se familiarizar com os ataques comuns de aplicativos da Web e suas implicações. Não se preocupe se algum desses termos parecer estranho neste momento; eles se tornarão mais claros à medida que você progredir e aplicar uma abordagem iterativa ao aprendizado.

É imperativo estudar os aplicativos da Web em profundidade e se familiarizar com o modo como eles funcionam e com as várias pilhas de aplicativos diferentes. Veremos ataques de aplicativos da web repetidamente durante nossa jornada na Academia, na plataforma HTB principal e em avaliações da vida real. Vamos nos aprofundar e aprender a estrutura/função dos aplicativos da Web para nos tornarmos invasores mais bem informados, nos diferenciar de nossos pares e encontrar falhas que outros possam ignorar. É imperativo estudar os aplicativos da Web em profundidade e familiarizar-se com o funcionamento deles e com as diversas pilhas de aplicativos e tipos de aplicativos.