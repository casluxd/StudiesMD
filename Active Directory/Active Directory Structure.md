
Active Directory (AD) é um serviço de diretório para ambientes de rede Windows. É uma estrutura hierárquica distribuída que permite o gerenciamento centralizado dos recursos de uma organização, incluindo usuários, computadores, grupos, dispositivos de rede e compartilhamentos de arquivos, políticas de grupo, servidores e estações de trabalho e trusts. O AD fornece funções de autenticação e autorização em um ambiente de domínio do Windows. Um serviço de diretório, como o Active Directory Domain Services (AD DS), oferece a uma organização maneiras de armazenar dados de diretório e disponibilizá-los para usuários padrão e administradores na mesma rede. O AD DS armazena informações como nomes de usuário e senhas e gerencia os direitos necessários para que usuários autorizados acessem essas informações. Ele foi apresentado pela primeira vez com o Windows Server 2000 e tem sofrido ataques crescentes nos últimos anos. Ele foi projetado para ser compatível com versões anteriores e muitos recursos provavelmente não são "seguros por padrão". É difícil gerenciá-lo adequadamente, especialmente em grandes ambientes onde pode ser facilmente configurado incorretamente.

As falhas e configurações incorretas do Active Directory geralmente podem ser usadas para obter uma posição (acesso interno), mover-se lateral e verticalmente em uma rede, e obter acesso não autorizado a recursos protegidos, como bancos de dados, compartilhamentos de arquivos, código-fonte e muito mais. O AD é essencialmente um grande banco de dados acessível a todos os usuários do domínio, independentemente de seu nível de privilégio. Uma conta de usuário básica do AD sem privilégios adicionais pode ser usada para enumerar a maioria dos objetos contidos no AD, incluindo, entre outros:

|   |   |
|---|---|
|Domain Computers|Domain Users|
|Domain Group Information|Organizational Units (OUs)|
|Default Domain Policy|Functional Domain Levels|
|Password Policy|Group Policy Objects (GPOs)|
|Domain Trusts|Access Control Lists (ACLs)|

Por esse motivo, devemos entender como o Active Directory é configurado e os fundamentos da administração antes de tentar atacá-lo. É sempre mais fácil "quebrar" as coisas se já sabemos como construí-las.

O Active Directory é organizado em uma estrutura de árvore hierárquica, com uma floresta no topo contendo um ou mais domínios, que podem ter subdomínios aninhados. Uma floresta é o limite de segurança dentro do qual todos os objetos estão sob controle administrativo. Uma floresta pode conter vários domínios e um domínio pode incluir outros filhos ou subdomínios. Um domínio é uma estrutura dentro da qual objetos contidos (usuários, computadores e grupos) são acessíveis. Ele possui muitas Unidades Organizacionais (OUs) integradas, como Controladores de Domínio, Usuários, Computadores e novas OUs podem ser criadas conforme necessário. OUs podem conter objetos e sub-OUs, permitindo a atribuição de diferentes políticas de grupo.

Em um nível muito alto (simplista), uma estrutura AD pode ter a seguinte aparência:

```shell-session
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```

Aqui podemos dizer que INLANEFREIGHT.LOCAL é o domínio raiz e contém os subdomínios (domínios filho ou raiz da árvore) ADMIN.INLANEFREIGHT.LOCAL, CORP.INLANEFREIGHT.LOCAL e DEV.INLANEFREIGHT.LOCAL assim como os demais objetos que compõem um domínio, como usuários, grupos, computadores e outros, conforme veremos em detalhes a seguir. É comum ver vários domínios (ou florestas) vinculados por meio de relações de confiança em organizações que realizam muitas aquisições. Muitas vezes, é mais rápido e fácil criar uma relação de confiança com outro domínio/floresta do que recriar todos os novos usuários no domínio atual. Como veremos em módulos posteriores, os trusts de domínio podem introduzir uma série de problemas de segurança se não forem administrados adequadamente.

![[Pasted image 20230531231335.png]]

O gráfico abaixo mostra duas florestas, INLANEFREIGHT.LOCAL e FREIGHTLOGISTICS.LOCAL. A seta bidirecional representa uma confiança bidirecional entre as duas florestas, significando que os usuários em INLANEFREIGHT.LOCAL podem acessar recursos em FREIGHTLOGISTICS.LOCAL e vice-versa. Também podemos ver vários domínios filhos em cada domínio raiz. Neste exemplo, podemos ver que o domínio raiz confia em cada um dos domínios filhos, mas os domínios filhos na floresta A não têm necessariamente relações de confiança estabelecidas com os domínios filhos na floresta B. Isso significa que um usuário que faz parte de admin.dev.freightlogistics.local NÃO poderá se autenticar em máquinas no domínio wh.corp.inlanefreight.local por padrão mesmo que exista uma relação de confiança bidirecional entre os domínios inlanefreight.local e Freightlogistics.local de nível superior. Para permitir a comunicação direta de admin.dev.freightlogistics.local e wh.corp.inlanefreight.local, outro trust precisaria ser configurado.

![[Pasted image 20230531233647.png]]

