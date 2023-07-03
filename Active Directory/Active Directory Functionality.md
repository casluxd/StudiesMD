
Conforme mencionado anteriormente, existem cinco funções FSMO (Flexible Single Master Operation). Esses papéis podem ser definidos da seguinte forma:

- Schema Master: Essa função gerencia a cópia de leitura/gravação do esquema AD, que define todos os atributos que podem ser aplicados a um objeto no AD.
- Domain Naming Master: Gerencia nomes de domínio e garante que dois domínios com o mesmo nome não sejam criados na mesma floresta.
- Relative ID (RID) Master: O RID Master atribui blocos de RIDs a outros DCs dentro do domínio que podem ser usados ​​para novos objetos. O mestre RID ajuda a garantir que vários objetos não sejam atribuídos ao mesmo SID. Os SIDs de objeto de domínio são o SID de domínio combinado com o número RID atribuído ao objeto para criar o SID exclusivo.
- PDC Emulator: O host com essa função seria o controlador de domínio autoritativo no domínio e responderia a solicitações de autenticação, alterações de senha e gerenciaria objetos de política de grupo (GPOs). O emulador PDC também mantém o tempo dentro do domínio.
- Infrastructure Master: Essa função converte GUIDs, SIDs e DNs entre domínios. Essa função é usada em organizações com vários domínios em uma única floresta. O Infrastructure Master os ajuda a se comunicar. Se essa função não estiver funcionando corretamente, as Listas de Controle de Acesso (ACLs) mostrarão SIDs em vez de nomes totalmente resolvidos.

Dependendo da organização, essas funções podem ser atribuídas a DCs específicos ou como padrão sempre que um novo DC é adicionado. Problemas com funções FSMO levarão a dificuldades de autenticação e autorização em um domínio.

# Domain and Forest Functional Levels

A Microsoft introduziu níveis funcionais para determinar os vários recursos e capacidades disponíveis nos Active Directory Domain Services (AD DS) no nível de domínio e floresta. Eles também são usados para especificar quais sistemas operacionais podem executar um Domain Controller em um domínio ou floresta. Esse(https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754918(v=ws.10)?redirectedfrom=MSDN) e esse (https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-functional-levels) artigo descrevem os domain e forest functional levels do Windows 2000 nativo ao Windows Server 2012 R2. Abaixo está uma rápida visão geral das diferenças nos domain functional levels do Windows 2000 nativo até o Windows Server 2016, além de todos os recursos padrão do Active Directory Directory Services do nível logo abaixo dele (ou apenas os recursos padrão do AD DS no caso do Windows 2000 nativo).

|Domain Functional Level|Features Available|Supported Domain Controller Operating Systems|
|---|---|---|
|Windows 2000 native|Universal groups for distribution and security groups, group nesting, group conversion (between security and distribution and security groups), SID history.|Windows Server 2008 R2, Windows Server 2008, Windows Server 2003, Windows 2000|
|Windows Server 2003|Netdom.exe domain management tool, lastLogonTimestamp attribute introduced, well-known users and computers containers, constrained delegation, selective authentication.|Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008, Windows Server 2003|
|Windows Server 2008|Distributed File System (DFS) replication support, Advanced Encryption Standard (AES 128 and AES 256) support for the Kerberos protocol, Fine-grained password policies|Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008|
|Windows Server 2008 R2|Authentication mechanism assurance, Managed Service Accounts|Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2|
|Windows Server 2012|KDC support for claims, compound authentication, and Kerberos armoring|Windows Server 2012 R2, Windows Server 2012|
|Windows Server 2012 R2|Extra protections for members of the Protected Users group, Authentication Policies, Authentication Policy Silos|Windows Server 2012 R2|
|Windows Server 2016|[Smart card required for interactive logon](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/interactive-logon-require-smart-card) new [Kerberos](https://docs.microsoft.com/en-us/windows-server/security/kerberos/whats-new-in-kerberos-authentication) features and new [credential protection](https://docs.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/whats-new-in-credential-protection) features|Windows Server 2019 and Windows Server 2016|

Um novo functional level não foi adicionado com o lançamento do Windows Server 2019. No entanto, o functional level do Windows Server 2008 é o requisito mínimo para adicionar controladores de domínio Server 2019 a um ambiente. Além disso, o domínio de destino deve usar DFS-R(https://learn.microsoft.com/en-us/windows-server/storage/dfs-replication/dfsr-overview) para replicação SYSVOL.

Forest functional levels introduziram alguns recursos importantes ao longo dos anos:
- Windows Server 2003: viu a introdução da confiança de floresta, renomeação de domínio, controladores de domínio somente leitura (RODC) e muito mais.
- Windows Server 2008: Todos os novos domínios adicionados ao padrão de floresta para o nível funcional de domínio do Server 2008. Sem novos recursos adicionais.
- Windows Server 2008 R2: Active Directory Recycle Bin fornece a capacidade de restaurar objetos excluídos quando o AD DS está em execução.
- Windows Server 2012 R2: Todos os novos domínios adicionados ao padrão de floresta para o nível funcional de domínio do Server 2012 R2. Sem novos recursos adicionais.
- Windows Server 2016: Gerenciamento de acesso privilegiado (PAM) usando o Microsoft Identity Manager (MIM). https://learn.microsoft.com/en-us/windows-server/identity/whats-new-active-directory-domain-services#privileged-access-management

## Trusts

Uma confiança é usada para estabelecer autenticação floresta-floresta ou domínio-domínio, permitindo que os usuários acessem recursos em (ou administrem) outro domínio fora do domínio em que sua conta reside. Uma confiança cria um link entre os sistemas de autenticação de dois domínios.

Existem vários tipos de confiança:
- Parent-child: Domínios dentro da mesma floresta. O domínio filho tem uma relação de confiança transitiva bidirecional com o domínio pai.
- Cross-link: uma relação de confiança entre domínios filhos para acelerar a autenticação.
- External: Uma relação de confiança não transitiva entre dois domínios separados em florestas separadas que ainda não foram unidas por uma relação de confiança de floresta. Esse tipo de confiança utiliza filtragem SID.
- Tree-root: uma confiança transitiva bidirecional entre um domínio raiz da floresta e um novo domínio raiz da árvore. Eles são criados por design quando você configura um novo domínio raiz de árvore em uma floresta.
- Forest: uma relação de confiança transitiva entre dois domínios raiz de floresta.

![[Pasted image 20230601161508.png]]

Trusts podem ser transitivos ou não transitivos.
- Uma confiança transitiva significa que a confiança é estendida a objetos nos quais o domínio filho confia.
- Em uma confiança não transitiva, apenas o próprio domínio filho é confiável.

Trusts podem ser configurados para serem unidirecionais ou bidirecionais.
- Em relações de confiança bidirecionais, os usuários de ambos os domínios confiáveis ​​podem acessar recursos.
- Em uma relação de confiança unidirecional, apenas usuários em um domínio confiável podem acessar recursos em um domínio confiável, e não vice-versa. A direção da confiança é oposta à direção do acesso.

Frequentemente, as relações de confiança de domínio são configuradas incorretamente e fornecem caminhos de ataque não intencionais. Além disso, confianças configuradas para facilidade de uso podem não ser revisadas posteriormente para possíveis implicações de segurança. Fusões e aquisições podem resultar em fundos bidirecionais com empresas adquiridas, introduzindo riscos inadvertidamente no ambiente da empresa adquirente. Não é incomum ser capaz de executar um ataque como Kerberoasting contra um domínio fora do domínio principal e obter um usuário que tenha acesso administrativo dentro do domínio principal.