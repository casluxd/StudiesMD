Direitos e privilégios são os pilares do gerenciamento do AD e, se mal gerenciados, podem facilmente levar a abusos por parte de invasores ou testadores de penetração. Direitos e privilégios de acesso são dois tópicos importantes no AD(e infosec em geral), e devemos entender a diferença. Os direitos são normalmente atribuídos a usuários ou grupos e lidam com permissões para acessar um objeto, como um arquivo, enquanto os privilégios concedem ao usuário permissão para executar uma ação como executar um programa, desligar um sistema, redefinir senhas, etc. Os privilégios podem ser atribuídos individualmente aos usuários ou conferidos a eles por meio de associação de grupo personalizada ou integrada. Os computadores com Windows têm um conceito chamado Atribuição de Direitos do Usuário, que, embora referidos como direitos, são na verdade tipos de privilégios concedidos a um usuário. Discutiremos isso mais adiante nesta seção. Devemos ter uma compreensão firme das diferenças entre direitos e privilégios em um sentido mais amplo e precisamente como eles se aplicam a um ambiente AD.

## Built-in AD Groups

O AD contém muitos grupos de segurança default ou built-in(https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups), alguns dos quais concedem a seus membros direitos e privilégios poderosos que podem ser abusados ​​para escalar privilégios dentro de um domínio e, finalmente, obter privilégios de Administrador de Domínio ou SISTEMA em um Controlador de Domínio (DC). A associação em muitos desses grupos deve ser estritamente gerenciada, pois a associação/privilégios excessivos do grupo é uma falha comum em muitas redes AD que os invasores procuram abusar. Alguns dos grupos integrados mais comuns estão listados abaixo.

- Account Operators: Os membros podem criar e modificar a maioria dos tipos de contas, incluindo contas de usuários, grupos locais e globais, e os membros podem fazer login localmente nos controladores de domínio. Eles não podem gerenciar a conta Administrador, contas de usuário administrativo ou membros dos grupos Administrator, Server Operators, Account Operators, Backup Operators ou Print Operators.
- Administrators: Os membros têm acesso total e irrestrito a um computador ou a um domínio inteiro se estiverem nesse grupo em um controlador de domínio.
- Backup Operators: Os membros podem fazer backup e restaurar todos os arquivos em um computador, independentemente das permissões definidas nos arquivos. Os operadores de backup também podem fazer logon e desligar o computador. Os membros podem fazer logon em DCs localmente e devem ser considerados administradores de domínio. Eles podem fazer cópias de sombra do banco de dados SAM/NTDS, que, se obtidas, podem ser usadas para extrair credenciais e outras informações suculentas.
- DnsAdmins: Os membros têm acesso às informações de DNS da rede. O grupo só será criado se a função de servidor DNS estiver ou tiver sido instalada em um controlador de domínio no domínio.
- Domain Admins: Os membros têm acesso total para administrar o domínio e são membros do grupo do administrador local em todas as máquinas ingressadas no domínio.
- Domain Computers: Todos os computadores criados no domínio (além dos controladores de domínio) são adicionados a esse grupo.
- Domain Controllers: Contém todos os controladores de domínio em um domínio. Novos DCs são adicionados a este grupo automaticamente.
- Domain Guests: Este grupo inclui a conta Convidado interna do domínio. Os membros deste grupo têm um perfil de domínio criado ao entrar em um computador ingressado no domínio como um convidado local.
- Domain Users: Este grupo contém todas as contas de usuário em um domínio. Uma nova conta de usuário criada no domínio é adicionada automaticamente a este grupo.
- Enterprise Admins: A associação a esse grupo fornece acesso completo à configuração no domínio. O grupo existe apenas no domínio raiz de uma floresta do AD. Os membros desse grupo têm a capacidade de fazer alterações em toda a floresta, como adicionar um domínio filho ou criar uma relação de confiança. A conta Administrador do domínio raiz da floresta é o único membro desse grupo por padrão.
- Event Log Readers: Os membros podem ler logs de eventos em computadores locais. O grupo só é criado quando um host é promovido a controlador de domínio.
- Group Policy Creator Owners: Os membros criam, editam ou excluem Objetos de Diretiva de Grupo no domínio.
- Hyper-V Administrators: Os membros têm acesso completo e irrestrito a todos os recursos do Hyper-V. Se houver DCs virtuais no domínio, todos os administradores de virtualização, como membros dos administradores do Hyper-V, devem ser considerados administradores de domínio.
- IIS_IUSRS: Este é um grupo interno usado pelos Serviços de Informações da Internet (IIS), começando com o IIS 7.0.
- Pre-Windows 2000 Compatible Access: Este grupo existe para compatibilidade com versões anteriores de computadores que executam o Windows NT 4.0 e versões anteriores. A associação a esse grupo geralmente é uma configuração herdada restante. Isso pode levar a falhas em que qualquer pessoa na rede pode ler informações do AD sem exigir um nome de usuário e senha AD válidos.
- Print Operators: Os membros podem gerenciar, criar, compartilhar e excluir impressoras conectadas a controladores de domínio no domínio junto com qualquer objeto de impressora no AD. Os membros têm permissão para fazer logon em DCs localmente e podem ser usados ​​para carregar um driver de impressora mal-intencionado e aumentar os privilégios dentro do domínio.
- Protected Users: Os membros desse grupo recebem proteções adicionais contra roubo de credenciais e táticas como abuso de Kerberos.
- Read-only Domain Controllers: Contém todos os controladores de domínio somente leitura no domínio.
- Remote Desktop Users: Este grupo é usado para conceder aos usuários e grupos permissão para se conectar a um host via Remote Desktop (RDP). Este grupo não pode ser renomeado, excluído ou movido.
- Remote Management Users: Este grupo pode ser usado para conceder aos usuários acesso remoto a computadores via Windows Remote Management (WinRM)
- Schema Admins: Os membros podem modificar o esquema do Active Directory, que é a forma como todos os objetos com AD são definidos. Este grupo existe apenas no domínio raiz de uma floresta do AD. A conta Administrador do domínio raiz da floresta é o único membro desse grupo por padrão.
- Server Operators: Este grupo existe apenas em controladores de domínio. Os membros podem modificar serviços, acessar compartilhamentos SMB e fazer backup de arquivos em controladores de domínio. Por padrão, este grupo não tem membros.

```powershell
PS C:\htb>  Get-ADGroup -Identity "Server Operators" -Properties *

adminCount                      : 1
CanonicalName                   : INLANEFREIGHT.LOCAL/Builtin/Server Operators
CN                              : Server Operators
Created                         : 10/27/2021 8:14:34 AM
createTimeStamp                 : 10/27/2021 8:14:34 AM
Deleted                         : 
Description                     : Members can administer domain servers
DisplayName                     : 
DistinguishedName               : CN=Server Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL
dSCorePropagationData           : {10/28/2021 1:47:52 PM, 10/28/2021 1:44:12 PM, 10/28/2021 1:44:11 PM, 10/27/2021 
                                  8:50:25 AM...}
GroupCategory                   : Security
GroupScope                      : DomainLocal
groupType                       : -2147483643
HomePage                        : 
instanceType                    : 4
isCriticalSystemObject          : True
isDeleted                       : 
LastKnownParent                 : 
ManagedBy                       : 
MemberOf                        : {}
Members                         : {}
Modified                        : 10/28/2021 1:47:52 PM
modifyTimeStamp                 : 10/28/2021 1:47:52 PM
Name                            : Server Operators
nTSecurityDescriptor            : System.DirectoryServices.ActiveDirectorySecurity
ObjectCategory                  : CN=Group,CN=Schema,CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL
ObjectClass                     : group
ObjectGUID                      : 0887487b-7b07-4d85-82aa-40d25526ec17
objectSid                       : S-1-5-32-549
ProtectedFromAccidentalDeletion : False
SamAccountName                  : Server Operators
sAMAccountType                  : 536870912
sDRightsEffective               : 0
SID                             : S-1-5-32-549
SIDHistory                      : {}
systemFlags                     : -1946157056
uSNChanged                      : 228556
uSNCreated                      : 12360
whenChanged                     : 10/28/2021 1:47:52 PM
whenCreated                     : 10/27/2021 8:14:34 AM
```

Como podemos ver acima, o estado padrão do grupo Server Operators é não ter membros e é um grupo local de domínio por padrão. Por outro lado, o grupo Domain Admins visto abaixo tem vários membros e contas de serviço atribuídos a ele. Domain Admins também são grupos globais em vez de domínio local. Mais informações sobre associação de grupo podem ser encontradas posteriormente neste módulo. Desconfie de quem, se alguém, você dá acesso a esses grupos. Um invasor pode facilmente obter as chaves da empresa se obtiver acesso a um usuário atribuído a esses grupos.

```powershell
PS C:\htb>  Get-ADGroup -Identity "Domain Admins" -Properties * | select DistinguishedName,GroupCategory,GroupScope,Name,Members

DistinguishedName : CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
GroupCategory     : Security
GroupScope        : Global
Name              : Domain Admins
Members           : {CN=htb-student_adm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=sharepoint
                    admin,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=FREIGHTLOGISTICSUSER,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL, CN=PROXYAGENT,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL...}
```

## User Rights Assignment

Dependendo de sua associação de grupo atual e de outros fatores, como privilégios que os administradores podem atribuir por meio da Diretiva de Grupo (GPO), os usuários podem ter vários direitos atribuídos à sua conta. Este artigo da Microsoft(https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment) sobre atribuição de direitos do usuário fornece uma explicação detalhada de cada um dos direitos do usuário que podem ser definidos no Windows. Nem todos os direitos listados aqui são importantes para nós do ponto de vista da segurança como testadores de penetração ou defensores, mas alguns direitos concedidos a uma conta podem levar a consequências não intencionais, como escalonamento de privilégios ou acesso a arquivos confidenciais. Por exemplo, digamos que podemos obter acesso de gravação em um objeto de política de grupo (GPO) aplicado a uma UO contendo um ou mais usuários que controlamos. Neste exemplo, poderíamos aproveitar uma ferramenta como SharpGPOAbuse para atribuir direitos direcionados a um usuário. Podemos realizar muitas ações no domínio para promover nosso acesso com esses novos direitos. Alguns exemplos incluem:
- SeRemoteInteractiveLogonRight: Esse privilégio pode dar ao nosso usuário de destino o direito de fazer logon em um host via Área de Trabalho Remota (RDP), que pode ser usado para obter dados confidenciais ou aumentar privilégios.
- SeBackupPrivilege: Isso concede ao usuário a capacidade de criar backups do sistema e pode ser usado para obter cópias de arquivos confidenciais do sistema que podem ser usados ​​para recuperar senhas, como as seções SAM e SYSTEM Registry e o arquivo de banco de dados NTDS.dit Active Directory.
- SeDebugPrivilege: Isso permite que um usuário depure e ajuste a memória de um processo. Com esse privilégio, os invasores podem utilizar uma ferramenta como Mimikatz para ler o espaço de memória do processo LSASS (Local System Authority) e obter quaisquer credenciais armazenadas na memória.
- SeImpersonatePrivilege: Esse privilégio nos permite representar um token de uma conta privilegiada, como `NT AUTHORITY\SYSTEM`. Isso pode ser aproveitado com uma ferramenta como JuicyPotato, RogueWinRM, PrintSpoofer, etc., para escalar privilégios em um sistema de destino.
- SeLoadDriverPrivilege: Um usuário com esse privilégio pode carregar e descarregar drivers de dispositivo que podem ser usados ​​para aumentar privilégios ou comprometer um sistema.
- SeTakeOwnershipPrivilege: Isso permite que um processo tome posse de um objeto. Em seu nível mais básico, poderíamos usar esse privilégio para obter acesso a um compartilhamento de arquivo ou a um arquivo em um compartilhamento que, de outra forma, não estaria acessível para nós.

Existem muitas técnicas disponíveis para abusar dos direitos do usuário detalhadas aqui(https://blog.palantir.com/windows-privilege-abuse-auditing-detection-and-defense-3078a403d74e) e aqui(https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens). Embora fora do escopo deste módulo, é essencial compreender o impacto que a atribuição do privilégio errado a uma conta pode ter no Active Directory. Um pequeno erro administrativo pode levar a um comprometimento completo do sistema ou da empresa.

## Viewing a User's Privileges

Depois de fazer login em um host, digitar o comando whoami /priv nos fornecerá uma lista de todos os direitos de usuário atribuídos ao usuário atual. Alguns direitos estão disponíveis apenas para usuários administrativos e só podem ser listados/aproveitados ao executar uma sessão elevada de CMD ou PowerShell. Esses conceitos de direitos elevados e Controle de Conta de Usuário (UAC) são recursos de segurança introduzidos no Windows Vista cujo padrão é restringir a execução de aplicativos com permissões totais, a menos que seja absolutamente necessário. Se compararmos e contrastarmos os direitos disponíveis para nós como administrador em um console não elevado versus um console elevado, veremos que eles diferem drasticamente. Primeiro, vamos ver os direitos disponíveis para um usuário padrão do Active Directory.

### Standard Domain User's Rights

```powershell
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

Podemos ver que os direitos são muito limitados e nenhum dos direitos "perigosos" descritos acima está presente. Em seguida, vamos dar uma olhada em um usuário privilegiado. Abaixo estão os direitos disponíveis para um usuário Admin do Domínio.

### Domain Admin Rights Non-Elevated

Podemos ver o seguinte em um console não elevado que não parece ser nada mais do que disponível para o usuário de domínio padrão. Isso ocorre porque, por padrão, os sistemas Windows não nos habilitam todos os direitos, a menos que executemos o console CMD ou PowerShell em um contexto elevado. Isso evita que todos os aplicativos sejam executados com os privilégios mais altos possíveis. Isso é controlado por algo chamado Controle de Conta de Usuário (UAC), que é abordado em detalhes no módulo Escalação de Privilégios do Windows.

```powershell
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

### Domain Admin Rights Elevated

Se inserirmos o mesmo comando em um console elevado do PowerShell, podemos ver a lista completa de direitos disponíveis para nós:

```powershell
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeMachineAccountPrivilege                 Add workstations to domain                                         Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeEnableDelegationPrivilege               Enable computer and user accounts to be trusted for delegation     Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
```

Os direitos do usuário aumentam com base nos grupos em que são colocados ou em seus privilégios atribuídos. Abaixo está um exemplo dos direitos concedidos a um membro do grupo Backup Operators. Os usuários neste grupo têm outros direitos atualmente restritos pelo UAC (direitos adicionais, como o poderoso SeBackupPrivilege, não são ativados por padrão em uma sessão de console padrão). Esse privilégio por si só não pode ser usado para obter acesso a dados confidenciais, mas pode causar uma interrupção massiva do serviço caso eles façam logon em um controlador de domínio localmente (não remotamente via RDP ou WinRM).

```powershell
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

Como atacantes e defensores, precisamos entender os direitos que são concedidos aos usuários por meio da associação de grupos de segurança integrados no Active Directory. Não é incomum encontrar usuários aparentemente com poucos privilégios adicionados a um ou mais desses grupos, que podem ser usados ​​para acessar ou comprometer o domínio. O acesso a esses grupos deve ser rigorosamente controlado. Normalmente, é uma prática recomendada deixar a maioria desses grupos vazios e apenas adicionar uma conta a um grupo se uma ação única precisar ser executada ou uma tarefa repetitiva precisar ser configurada. Quaisquer contas adicionadas a um dos grupos discutidos nesta seção ou concedidas privilégios extras devem ser rigorosamente controladas e monitoradas, atribuídas a uma senha ou frase secreta muito forte, e deve ser separado de uma conta usada por um administrador de sistema para executar suas tarefas diárias.

Agora que começamos a abordar algumas considerações de segurança no AD relacionadas a privilégios de usuário e associação de grupo integrada, vamos examinar alguns pontos críticos para proteger uma instalação do Active Directory.