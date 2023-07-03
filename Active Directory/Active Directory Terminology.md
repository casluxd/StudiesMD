
Antes de prosseguirmos, vamos dar um passo atrás e definir algumas terminologias-chave que serão usadas ao longo deste módulo e, em geral, ao lidar com o Active Directory em qualquer capacidade.

## Object

Um objeto pode ser definido como QUALQUER recurso presente em um ambiente do Active Directory, como OUs, impressoras, usuários, controladores de domínio, etc.

## Attributes

Cada objeto no Active Directory tem um conjunto associado de atributos usados ​​para definir as características do determinado objeto. Um objeto de computador contém atributos como o nome do host e o nome DNS. Todos os atributos no AD têm um nome LDAP associado que pode ser usado ao realizar consultas LDAP, como displayName para Full Name e given name para First Name.

## Schema

O esquema do Active Directory é essencialmente o projeto de qualquer ambiente corporativo. Ele define quais tipos de objetos podem existir no banco de dados do AD e seus atributos associados. Ele lista as definições correspondentes aos objetos do AD e contém informações sobre cada objeto. Por exemplo, os usuários no AD pertencem à classe "user" e os objetos de computador a "computer" e assim por diante. Cada objeto possui suas próprias informações (algumas necessárias para serem definidas e outras opcionais) que são armazenadas em Atributos. Quando um objeto é criado a partir de uma classe, isso é chamado de instanciação, e um objeto criado a partir de uma classe específica é chamado de instância dessa classe. Por exemplo, se pegarmos o computador RDS01. Este objeto de computador é uma instância da classe "computer" no Active Directory.

## Domain

Um domínio é um grupo lógico de objetos, como computadores, usuários, OUs, grupos, etc. Podemos pensar em cada domínio como uma cidade diferente dentro de um estado ou país. Os domínios podem operar de forma totalmente independente um do outro ou ser conectados por meio de relações de confiança.

## Forest

Uma floresta é uma coleção de domínios do Active Directory. É o contêiner superior e contém todos os objetos do AD apresentados abaixo, incluindo, entre outros, domínios, usuários, grupos, computadores e objetos de Diretiva de Grupo. Uma floresta pode conter um ou vários domínios e ser considerada um estado dos EUA ou um país da UE. Cada floresta opera de forma independente, mas pode ter vários relacionamentos de confiança com outras florestas. 

## Tree

Uma árvore é uma coleção de domínios do Active Directory que começa em um único domínio raiz. Uma floresta é uma coleção de árvores AD. Cada domínio em uma árvore compartilha um limite com os outros domínios. Uma relação de confiança pai-filho é formada quando um domínio é adicionado sob outro domínio em uma árvore. Duas árvores na mesma floresta não podem compartilhar um nome (namespace). Digamos que temos duas árvores em uma floresta do AD: inlanefreight.local e ilfreight.local. Um domínio filho do primeiro seria corp.inlanefreight.local, enquanto um domínio filho do segundo poderia ser corp.ilfreight.local. Todos os domínios em uma árvore compartilham um Catálogo Global padrão que contém todas as informações sobre os objetos que pertencem à árvore.

## Container

Os objetos Container armazenam outros objetos e têm um lugar definido na hierarquia da subárvore do diretório.

## Leaf

Os objetos folha não contêm outros objetos e são encontrados no final da hierarquia da subárvore.

## Global Unique Identifier (GUID)

Um GUID é um valor exclusivo de 128 bits atribuído quando um usuário ou grupo de domínio é criado. Esse valor GUID é exclusivo em toda a empresa, semelhante a um endereço MAC. Cada objeto criado pelo Active Directory recebe um GUID, não apenas objetos de usuário e grupo. O GUID é armazenado no atributo ObjectGUID. Ao consultar um objeto AD (como um usuário, grupo, computador, domínio, controlador de domínio, etc.), podemos consultar seu valor objectGUID usando o PowerShell ou pesquisá-lo especificando seu nome distinto, GUID, SID ou SAM nome da conta. GUIDs são usados ​​pelo AD para identificar objetos internamente. Pesquisar no Active Directory por valor GUID é provavelmente a maneira mais precisa e confiável de encontrar o objeto exato que você está procurando, especialmente se o catálogo global puder conter correspondências semelhantes para um nome de objeto. Especificar o valor ObjectGUID ao executar a enumeração do AD garantirá que obtenhamos os resultados mais precisos relativos ao objeto sobre o qual estamos procurando informações. A propriedade ObjectGUID nunca muda e está associada ao objeto enquanto esse objeto existir no domínio.

## Security Principals

Security Principals são qualquer coisa que o sistema operacional pode autenticar, incluindo usuários, contas de computador ou até mesmo threads/processos executados no contexto de um usuário ou conta de computador (ou seja, um aplicativo como o Tomcat em execução no contexto de uma conta de serviço dentro do domínio). No AD, os security principals são objetos de domínio que podem gerenciar o acesso a outros recursos dentro do domínio. Também podemos ter contas de usuários locais e grupos de segurança usados ​​para controlar o acesso a recursos apenas naquele computador específico. Estes não são gerenciados pelo AD, mas sim pelo Security Accounts Manager (SAM).

## Security Identifier (SID)

Um identificador de segurança ou SID é usado como um identificador exclusivo para uma entidade de segurança ou grupo de segurança. Cada conta, grupo ou processo tem seu próprio SID exclusivo, que, em um ambiente AD, é emitido pelo controlador de domínio e armazenado em um banco de dados seguro. Um SID só pode ser usado uma vez. Mesmo que o princípio de segurança seja excluído, ele nunca mais poderá ser usado naquele ambiente para identificar outro usuário ou grupo. Quando um usuário faz login, o sistema cria um token de acesso para ele, que contém o SID do usuário, os direitos concedidos a ele e os SIDs de quaisquer grupos dos quais o usuário seja membro. Este token é usado para verificar os direitos sempre que o usuário executa uma ação no computador. Também existem SIDs conhecidos que são usados ​​para identificar usuários e grupos genéricos. Estes são os mesmos em todos os sistemas operacionais. Um exemplo é o grupo `Everyone`.

## Distinguished Name (DN)

Um Distinguished Name (DN) descreve o caminho completo para um objeto no AD (como cn=bjones, ou=IT, ou=Funcionários, dc=inlanefreight, dc=local). Neste exemplo, o usuário bjones trabalha no departamento de TI da empresa Inlanefreight, e sua conta é criada em uma Unidade Organizacional (OU) que mantém as contas dos funcionários da empresa. O Common Name (CN) bjones é apenas uma maneira pela qual o objeto do usuário pode ser pesquisado ou acessado dentro do domínio.

## Relative Distinguished Name (RDN)

Um Relative Distinguished Name (RDN) é um único componente do Distinguished Name que identifica o objeto como único de outros objetos no nível atual na hierarquia de nomenclatura. Em nosso exemplo, bjones é o Relative Distinguished Name do objeto. O AD não permite dois objetos com o mesmo nome no mesmo contêiner pai, mas pode haver dois objetos com os mesmos RDNs que ainda são exclusivos no domínio porque têm DNs diferentes. Por exemplo, o objeto cn=bjones,dc=dev,dc=inlanefreight,dc=local seria reconhecido como diferente de cn=bjones,dc=inlanefreight,dc=local.

![[Pasted image 20230601000950.png]]

## sAMAccountName

O sAMAccountName é o nome de logon do usuário. Aqui seria apenas bjones. Deve ser um valor exclusivo e 20 caracteres ou menos.

## userPrincipalName

O atributo userPrincipalName é outra maneira de identificar usuários no AD. Esse atributo consiste em um prefixo (o nome da conta do usuário) e um sufixo (o nome do domínio) no formato bjones@inlanefreight.local. Este atributo não é obrigatório.

## FSMO Roles

Nos primeiros dias do AD, se você tivesse vários DCs em um ambiente, eles brigariam para saber qual DC faria as alterações e, às vezes, as alterações não seriam feitas corretamente. A Microsoft então implementou "o último escritor vence", o que poderia apresentar seus próprios problemas se a última mudança quebrasse as coisas. Em seguida, eles introduziram um modelo no qual um único controlador de domínio "mestre" poderia aplicar alterações ao domínio, enquanto os outros apenas atenderiam às solicitações de autenticação. Este foi um projeto falho porque se o controlador de domínio mestre caísse, nenhuma alteração poderia ser feita no ambiente até que ele fosse restaurado. Para resolver esse modelo de ponto único de falha, a Microsoft separou as várias responsabilidades que um DC pode ter em funções FSMO (Flexible Single Master Operation). Isso dá aos controladores de domínio (DC) a capacidade de continuar autenticando usuários e concedendo permissões sem interrupção (autorização e autenticação). Existem cinco funções de FMSO: Schema Master e Domain Naming Master (um de cada por floresta), Relative ID (RID) Master (um por domínio), Primary Domain Controller(PDC) Emulator (um por domínio) e Infrastructure Master (um por domínio). Todas as cinco funções são atribuídas ao primeiro DC no domínio raiz da floresta em uma nova floresta do AD. As funções FSMO são geralmente definidas quando os controladores de domínio são criados, mas os administradores de sistema podem transferir essas funções, se necessário. Essas funções ajudam a replicação no AD a funcionar sem problemas e garantem que os serviços críticos estejam operando corretamente. Analisaremos cada uma dessas funções em detalhes mais adiante nesta seção.

## Global Catalog

Um catálogo global (GC) é um controlador de domínio que armazena cópias de TODOS os objetos em uma floresta do Active Directory. O GC armazena uma cópia completa de todos os objetos no domínio atual e uma cópia parcial dos objetos que pertencem a outros domínios na floresta. Os controladores de domínio padrão mantêm uma réplica completa de objetos pertencentes a seu domínio, mas não de domínios diferentes na floresta. O GC permite que usuários e aplicativos encontrem informações sobre quaisquer objetos em QUALQUER domínio na floresta. GC é um recurso habilitado em um controlador de domínio e executa as seguintes funções:
- Authentication (autorização fornecida para todos os grupos aos quais uma conta de usuário pertence, incluída quando um token de acesso é gerado)
- Object search (tornando transparente a estrutura de diretório dentro de uma floresta, permitindo que uma pesquisa seja realizada em todos os domínios de uma floresta, fornecendo apenas um atributo sobre um objeto.)

## Read-Only Domain Controller (RODC)

Um Read-Only Domain Controller(RODC) tem um banco de dados do Active Directory somente leitura. Nenhuma senha de conta AD é armazenada em cache em um RODC (além da conta de computador RODC e senhas RODC KRBTGT). Nenhuma alteração é enviada via banco de dados AD do RODC, SYSVOL ou DNS. Os RODCs também incluem um servidor DNS somente leitura, permitem a separação da função do administrador, reduzem o tráfego de replicação no ambiente e impedem que as modificações do SYSVOL sejam replicadas para outros DCs.

## Replication

A replicação acontece no AD quando os objetos do AD são atualizados e transferidos de um controlador de domínio para outro. Sempre que um controlador de domínio é adicionado, objetos de conexão são criados para gerenciar a replicação entre eles. Essas conexões são feitas pelo serviço Knowledge Consistency Checker (KCC), que está presente em todos os DCs. A replicação garante que as alterações sejam sincronizadas com todos os outros controladores de domínio em uma floresta, ajudando a criar um backup caso um controlador de domínio falhe.

## Service Principal Name (SPN)

Um service principal name (SPN) identifica exclusivamente uma instância de serviço. Eles são usados ​​pela autenticação Kerberos para associar uma instância de um serviço a uma conta de logon, permitindo que um aplicativo cliente solicite o serviço para autenticar uma conta sem precisar saber o nome da conta.

## Group Policy Object (GPO)

Group Policy Objects (GPOs) são coleções virtuais de configurações de política. Cada GPO tem um GUID exclusivo. Um GPO pode conter configurações do sistema de arquivos local ou configurações do Active Directory. As configurações de GPO podem ser aplicadas a objetos de usuário e de computador. Eles podem ser aplicados a todos os usuários e computadores no domínio ou definidos de forma mais granular no nível da UO.

## Access Control List (ACL)

Uma Access Control List (ACL) é a coleção ordenada de Entidades de Controle de Acesso (ACEs) que se aplicam a um objeto.

## Access Control Entities (ACEs)

Cada Access Control Entity (ACE) em uma ACL identifica um trustee (conta de usuário, conta de grupo ou sessão de logon) e lista os direitos de acesso que são permitidos, negados ou auditados para o trustee em questão.

## Discretionary Access Control List (DACL)

DACLs definem quais princípios de segurança são concedidos ou negados acesso a um objeto; ele contém uma lista de ACEs. Quando um processo tenta acessar um objeto protegível, o sistema verifica as ACEs na DACL do objeto para determinar se deve ou não conceder acesso. Se um objeto NÃO tiver uma DACL, o sistema concederá acesso total a todos, mas se a DACL não tiver entradas ACE, o sistema negará todas as tentativas de acesso. As ACEs na DACL são verificadas em sequência até que seja encontrada uma correspondência que permita os direitos solicitados ou até que o acesso seja negado.

## System Access Control Lists (SACL)

Permite que os administradores registrem tentativas de acesso feitas a objetos protegidos. As ACEs especificam os tipos de tentativas de acesso que fazem com que o sistema gere um registro no log de eventos de segurança.

## Fully Qualified Domain Name (FQDN)

Um FQDN é o nome completo de um computador ou host específico. Ele é escrito com o nome do host e o nome do domínio no formato `[nome do host].[nome do domínio].[tld]`. Isso é usado para especificar a localização de um objeto na hierarquia da árvore do DNS. O FQDN pode ser usado para localizar hosts em um Active Directory sem conhecer o endereço IP, muito parecido com quando navega para um site como google.com em vez de digitar o endereço IP associado. Um exemplo seria o host DC01 no domínio INLANEFREIGHT.LOCAL. O FQDN aqui seria DC01.INLANEFREIGHT.LOCAL.

## Tombstone

Uma Tombstone é um objeto contêiner no AD que contém objetos excluídos do AD. Quando um objeto é excluído do AD, o objeto permanece por um determinado período de tempo conhecido como Tombstone Lifetime, e o atributo isDeleted é definido como TRUE. Uma vez que um objeto exceda o tempo de Tombstone Lifetime, ele será totalmente removido. A Microsoft recomenda um tombstone lifetime de 180 dias para aumentar a utilidade dos backups, mas esse valor pode diferir entre os ambientes. Dependendo da versão do sistema operacional DC, esse valor será padronizado para 60 ou 180 dias. Se um objeto for excluído em um domínio que não tenha uma AD Recycle Bin, ele se tornará um objeto tombstone. Quando isso acontece, o objeto é despojado da maioria de seus atributos e colocado no contêiner Deleted Objects durante o tombstoneLifetime. Ele pode ser recuperado, mas quaisquer atributos que foram perdidos não podem mais ser recuperados.

## AD Recycle Bin

O AD Recycle Bin foi introduzido pela primeira vez no Windows Server 2008 R2 para facilitar a recuperação de objetos AD excluídos. Isso tornou mais fácil para os administradores de sistema restaurar objetos, evitando a necessidade de restaurar a partir de backups, reiniciar o Active Directory Domain Services (AD DS) ou reinicializar um Domain Controller. Quando o AD Recycle Bin é habilitado, quaisquer objetos excluídos são preservados por um período de tempo, facilitando a restauração, se necessário. Os administradores de sistema podem definir por quanto tempo um objeto permanece em um estado excluído e recuperável. Se isso não for especificado, o objeto poderá ser restaurado por um valor padrão de 60 dias. A maior vantagem de usar o AD Recycle Bin é que a maioria dos atributos de um objeto excluído é preservada, o que torna muito mais fácil restaurar completamente um objeto excluído ao seu estado anterior.

## SYSVOL

A pasta SYSVOL, ou compartilhamento, armazena cópias de arquivos públicos no domínio, como políticas do sistema, configurações de política de grupo, scripts de logon/logoff, e geralmente contém outros tipos de scripts que são executados para realizar várias tarefas no ambiente AD. O conteúdo da pasta SYSVOL é replicado para todos os controladores de domínio no ambiente usando o FRS (File Replication Services). Você pode ler mais sobre a estrutura SYSVOL aqui(http://www.techiebird.com/Sysvol_structure.html).

## AdminSDHolder

O objeto AdminSDHolder é usado para gerenciar ACLs para membros de grupos integrados no AD marcados como privilegiados. Ele atua como um contêiner que contém o descritor de segurança aplicado a membros de grupos protegidos. O processo SDProp (SD Propagator) é executado em um schedule no Domain Controller do emulador PDC. Quando esse processo é executado, ele verifica os membros dos grupos protegidos para garantir que a ACL correta seja aplicada a eles. Ele é executado a cada hora por padrão. Por exemplo, suponha que um invasor seja capaz de criar uma entrada ACL maliciosa para conceder a um usuário certos direitos sobre um membro do grupo Admins do Domínio. Nesse caso, a menos que eles modifiquem outras configurações no AD, esses direitos serão removidos (e eles perderão qualquer persistência que esperavam alcançar) quando o processo SDProp for executado no intervalo definido.

## dsHeuristics

O atributo dsHeuristics é um valor de string definido no objeto Directory Service usado para definir várias definições de configuração em toda a floresta. Uma dessas configurações é excluir grupos integrados da lista de grupos protegidos. Os grupos nesta lista são protegidos contra modificação por meio do objeto AdminSDHolder. Se um grupo for excluído por meio do atributo dsHeuristics, quaisquer alterações que o afetem não serão revertidas quando o processo SDProp for executado.

## adminCount

O atributo adminCount determina se o processo SDProp protege ou não um usuário. Se o valor for definido como 0 ou não especificado, o usuário não está protegido. Se o valor do atributo for definido como 1, o usuário estará protegido. Os invasores geralmente procuram contas com o atributo adminCount definido como 1 para atingir um ambiente interno. Geralmente, essas contas são privilegiadas e podem levar a acesso adicional ou comprometimento total do domínio.

## Active Directory Users and Computers (ADUC)

ADUC é um console GUI comumente usado para gerenciar usuários, grupos, computadores e contatos no AD. As alterações feitas no ADUC também podem ser feitas por meio do PowerShell.

## ADSI Edit

ADSI Edit é uma ferramenta GUI usada para gerenciar objetos no AD. Ele fornece acesso a muito mais do que está disponível no ADUC e pode ser usado para definir ou excluir qualquer atributo disponível em um objeto, adicionar, remover e mover objetos também. É uma ferramenta poderosa que permite ao usuário acessar o AD em um nível muito mais profundo. Muito cuidado deve ser tomado ao usar esta ferramenta, pois alterações aqui podem causar grandes problemas no AD.

## sIDHistory

Esse atributo contém quaisquer SIDs atribuídos anteriormente a um objeto. Geralmente é usado em migrações para que um usuário possa manter o mesmo nível de acesso ao migrar de um domínio para outro. Este atributo pode ser potencialmente abusado se definido de forma insegura, permitindo que um invasor obtenha acesso elevado anterior que uma conta tinha antes de uma migração se a filtragem de SID (ou remover SIDs de outro domínio do token de acesso de um usuário que pode ser usado para acesso elevado) não está habilitado.

## NTDS.DIT

O arquivo NTDS.DIT ​​pode ser considerado o coração do Active Directory. Ele é armazenado em um controlador de domínio em `C:\Windows\NTDS\` e é um banco de dados que armazena dados do AD, como informações sobre usuários e objetos de grupo, associação de grupo e, mais importante para invasores e testadores de penetração, os hashes de senha para todos os usuários no domínio. Depois que o comprometimento total do domínio é alcançado, um invasor pode recuperar esse arquivo, extrair os hashes e usá-los para executar um ataque pass-the-hash ou quebrá-los usando uma ferramenta como Hashcat para acessar recursos adicionais no domínio. Se a configuração Store password with reversible encryption estiver ativada, o NTDS.DIT ​​também armazenará as senhas em texto não criptografado de todos os usuários criados ou que alteraram suas senhas após a definição dessa política. Embora raro, algumas organizações podem habilitar essa configuração se usarem aplicativos ou protocolos que precisam usar a senha existente de um usuário (e não Kerberos) para autenticação.

## MSBROWSE

MSBROWSE é um protocolo de rede da Microsoft usado em versões anteriores de redes locais (LANs) baseadas no Windows para fornecer serviços de navegação. Ele era usado para manter uma lista de recursos, como impressoras e arquivos compartilhados, que estavam disponíveis na rede e para permitir que os usuários navegassem e acessassem facilmente esses recursos.

Na versão mais antiga do Windows, poderíamos usar nbtstat -A ip-address para procurar o navegador mestre. Se virmos MSBROWSE, significa que é o navegador mestre. Além disso, poderíamos usar o utilitário nltest para consultar um navegador mestre do Windows em busca dos nomes dos controladores de domínio.

Hoje, o MSBROWSE está bastante obsoleto e não é mais amplamente utilizado. LANs modernas baseadas em Windows usam o protocolo Server Message Block (SMB) para compartilhamento de arquivos e impressoras, e o protocolo CIFS (Common Internet File System) para serviços de navegação.

