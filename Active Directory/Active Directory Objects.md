
Frequentemente veremos o termo "objetos" quando nos referirmos ao AD. O que é um objeto? Um objeto pode ser definido como QUALQUER recurso presente em um ambiente do Active Directory, como OUs, impressoras, usuários, controladores de domínio.

![[Pasted image 20230601142133.png]]

## Users

Esses são os usuários dentro do ambiente AD da organização. Os usuários são considerados objetos leaf, o que significa que eles não podem conter nenhum outro objeto dentro deles. Outro exemplo de objeto leaft é uma caixa de correio no Microsoft Exchange. Um objeto de usuário é considerado um objeto de segurança e possui um identificador de segurança (SID) e um identificador exclusivo global (GUID). Os objetos de usuário têm muitos atributos possíveis, como nome de exibição, hora do último login, data da última alteração de senha, endereço de e-mail, descrição da conta, gerente, endereço e muito mais. Dependendo de como um determinado ambiente do Active Directory é configurado, pode haver mais de 800 atributos de usuário possíveis ao contabilizar TODOS os atributos possíveis, conforme detalhado aqui(https://www.easy365manager.com/how-to-get-all-active-directory-user-object-attributes/). Este exemplo vai muito além do que normalmente é preenchido para um usuário padrão na maioria dos ambientes, mas mostra o tamanho e a complexidade do Active Directory. Eles são um alvo crucial para os invasores, pois obter acesso até mesmo a um usuário com poucos privilégios pode conceder acesso a muitos objetos e recursos e permitir a enumeração detalhada de todo o domínio (ou floresta).

## Contacts

Um objeto de contato geralmente é usado para representar um usuário externo e contém atributos informativos, como nome, sobrenome, endereço de e-mail, número de telefone, etc. Eles são objetos leaft e NÃO são entidades de segurança (objetos protegíveis), portanto, não possuem um SID, apenas um GUID. Um exemplo seria um cartão de contato de um fornecedor terceirizado ou de um cliente.

## Printers

Um objeto de impressora aponta para uma impressora acessível dentro da rede AD. Como um contato, uma impressora é um objeto folha e não uma entidade de segurança, portanto, ela possui apenas um GUID. As impressoras têm atributos como o nome da impressora, informações do driver, número da porta, etc.

## Computers

Um objeto de computador é qualquer computador associado à rede AD (estação de trabalho ou servidor). Os computadores são objetos leaf porque não contêm outros objetos. No entanto, eles são considerados entidades de segurança e possuem um SID e um GUID. Assim como os usuários, eles são os principais alvos para os invasores, pois o acesso administrativo total a um computador (como a conta todo-poderosa `NT AUTHORITY\SYSTEM`) concede direitos semelhantes a um usuário de domínio padrão e pode ser usado para executar a maioria das tarefas de enumeração que uma conta de usuário pode (salvo algumas exceções em relações de confiança de domínio).

## Shared Folders

Um objeto de pasta compartilhada aponta para uma pasta compartilhada no computador específico onde a pasta reside. As pastas compartilhadas podem ter um controle de acesso rigoroso aplicado a elas e podem ser acessíveis a todos (mesmo aqueles sem uma conta AD válida), aberto apenas para usuários autenticados (o que significa que qualquer pessoa com a conta de usuário privilegiada mais baixa OU uma conta de computador (`NT AUTHORITY\SYSTEM`) pode acessá-lo) ou ser bloqueado para permitir apenas o acesso de determinados usuários/grupos. Qualquer pessoa que não tenha acesso explicitamente será impedida de listar ou ler seu conteúdo. As pastas compartilhadas NÃO são princípios de segurança e possuem apenas um GUID. Os atributos de uma pasta compartilhada podem incluir o nome, localização no sistema, direitos de acesso de segurança.

## Groups

Um grupo é considerado um objeto recipiente porque pode conter outros objetos, incluindo usuários, computadores e até mesmo outros grupos.
Um grupo é considerado um objeto de segurança e possui um SID e um GUID. No AD, os grupos são uma forma de gerenciar permissões de usuário e acesso a outros objetos protegíveis (tanto usuários quanto computadores). Digamos que queremos dar a 20 usuários de suporte técnico acesso ao grupo "Remote Management Users" em um jump host. Em vez de adicionar os usuários um por um, poderíamos adicionar o grupo e os usuários herdariam as permissões pretendidas por meio de sua participação no grupo. No Active Directory, geralmente vemos o que é chamado de "grupos aninhados" (um grupo adicionado como membro de outro grupo), o que pode levar o(s) usuário(s) a obter direitos não intencionais. A associação a grupos aninhados é algo que vemos e geralmente aproveitamos durante os testes de penetração. A ferramenta BloodHound ajuda a descobrir caminhos de ataque dentro de uma rede e ilustrá-los em uma interface gráfica. É excelente para auditar membros de grupos e descobrir/ver os impactos às vezes não intencionais de membros de grupos aninhados. Os grupos no AD podem ter muitos atributos, sendo os mais comuns o nome, descrição, associação e outros grupos aos quais o grupo pertence. Muitos outros atributos podem ser definidos, os quais discutiremos mais detalhadamente posteriormente neste módulo.

## Organizational Units (OUs)

Uma unidade organizacional, ou OU daqui em diante, é um contêiner que os administradores de sistemas podem usar para armazenar objetos semelhantes para facilitar a administração. OUs são freqüentemente usadas para delegação administrativa de tarefas sem conceder direitos administrativos totais a uma conta de usuário. Por exemplo, podemos ter uma OU de nível superior chamada Funcionários e, em seguida, OUs filhas abaixo dela para os vários departamentos, como Marketing, RH, Finanças, Help Desk, etc. Se uma conta recebesse o direito de redefinir senhas na UO de nível superior, esse usuário teria o direito de redefinir as senhas de todos os usuários da empresa. No entanto, se a estrutura da OU fosse tal que departamentos específicos fossem OUs filhos da OU Help Desk, então, qualquer usuário colocado na UO Help Desk teria esse direito delegado a eles, se concedido. Outras tarefas que podem ser delegadas no nível da unidade organizacional incluem criar/excluir usuários, modificar membros de grupos, gerenciar links de diretivas de grupo e redefinir senhas. As OUs são muito úteis para gerenciar as configurações da Diretiva de Grupo (que estudaremos mais adiante neste módulo) em um subconjunto de usuários e grupos em um domínio. Por exemplo, podemos definir uma política de senha específica para contas de serviço privilegiadas para que essas contas possam ser colocadas em uma OU específica e, em seguida, ter um objeto de política de grupo atribuído a ela, que aplicaria essa política de senha em todas as contas colocadas dentro dela. Alguns atributos de OU incluem seu nome, membros, configurações de segurança e muito mais.

## Domain

Um domínio é a estrutura de uma rede AD. Os domínios contêm objetos como usuários e computadores, que são organizados em objetos contêineres: grupos e OUs. Cada domínio tem seu próprio banco de dados separado e conjuntos de políticas que podem ser aplicados a todo e qualquer objeto dentro do domínio. Algumas políticas são definidas por padrão (e podem ser ajustadas), como a política de senha de domínio. Em contraste, outros são criados e aplicados com base na necessidade da organização, como bloquear o acesso ao cmd.exe para todos os usuários não administrativos ou mapear unidades compartilhadas no login.

## Domain Controllers

Os controladores de domínio são essencialmente os cérebros de uma rede AD. Eles lidam com solicitações de autenticação, verificam usuários na rede e controlam quem pode acessar os vários recursos no domínio. Todas as solicitações de acesso são validadas por meio do controlador de domínio e as solicitações de acesso privilegiado são baseadas em funções predeterminadas atribuídas aos usuários. Ele também impõe políticas de segurança e armazena informações sobre todos os outros objetos do domínio.

## Sites

Um site no AD é um conjunto de computadores em uma ou mais sub-redes conectadas usando links de alta velocidade. Eles são usados ​​para fazer com que a replicação entre controladores de domínio seja executada com eficiência.

## Built-in

No AD, integrado é um contêiner que contém grupos padrão em um domínio do AD. Eles são predefinidos quando um domínio do AD é criado.

## Foreign Security Principals

Uma entidade de segurança externa (FSP) é um objeto criado no AD para representar uma entidade de segurança que pertence a uma floresta externa confiável. Eles são criados quando um objeto como um usuário, grupo ou computador de uma floresta externa (fora da atual) é adicionado a um grupo no domínio atual. Eles são criados automaticamente após adicionar uma entidade de segurança a um grupo. Cada entidade de segurança estrangeira é um objeto de espaço reservado que contém o SID do objeto estrangeiro (um objeto que pertence a outra floresta). O Windows usa esse SID para resolver o nome do objeto por meio da relação de confiança. Os FSPs são criados em um contêiner específico chamado ForeignSecurityPrincipals com um nome distinto como `cn=ForeignSecurityPrincipals,dc=inlanefreight,dc=local`.

