
Como administrador de sistema, o gerenciamento de usuários e grupos é uma habilidade fundamental, pois nossos usuários geralmente são nosso principal ativo a ser gerenciado e, geralmente, o maior vetor de ataque de uma organização. Como pentesters, entender como enumerar, interpretar e tirar proveito de usuários e grupos é uma das maneiras mais fáceis de obter acesso e elevar nossos privilégios durante um pentest. Esta seção abordará o que são usuários e grupos, como gerenciá-los com o PowerShell e apresentar brevemente o conceito de domínios do Active Directory e usuários de domínio.

# O que são User Accounts?

As user accounts são uma forma de o pessoal acessar e usar os recursos de um host. Em certas circunstâncias, o sistema também utilizará uma user account especialmente provisionada para executar ações. Ao pensar em accounts, geralmente nos deparamos com quatro tipos diferentes:

- Service Accounts
- Built-in accounts
- Local users
- Domain users

# Default Local User Accounts

Várias contas são criadas em todas as instâncias do Windows à medida que o sistema operacional é instalado para ajudar no gerenciamento do host e no uso básico.

Abaixo está uma lista das contas internas padrão:

- Administrator: Essa conta é usada para realizar tarefas administrativas no host local.
- Default Account: A conta padrão é usada pelo sistema para executar aplicativos de autenticação multiusuário, como o utilitário Xbox.
- Guest Account: Essa conta é uma conta de direitos limitados que permite que usuários sem uma conta de usuário normal acessem o host. Ele está desabilitado por padrão e deve permanecer assim.
- WDAGUtility Account: Essa conta está em vigor para o Defender Application Guard, que pode proteger sessões de aplicativos.

# Breve introdução ao Active Directory

Resumindo, Active Directory (AD) é um serviço de diretório para ambientes Windows que fornece um ponto central de gerenciamento para usuários, computadores, grupos, dispositivos de rede, compartilhamento de arquivos, group policies, dispositivos e confia em outras organizações. Pense nisso como o guardião de um ambiente corporativo. Qualquer pessoa que faça parte do domínio pode acessar os recursos livremente, enquanto quem não faz não tem acesso a esses mesmos recursos ou, no mínimo, fica esperando no centro de visitantes.

Nesta seção, nos preocupamos com o AD no contexto de usuários e grupos. Podemos administrá-los a partir do PowerShell em qualquer host ingressado no domínio utilizando o módulo ActiveDirectory.

# Local vs Domain Joined Users

Os usuários de domínio diferem dos usuários locais porque recebem direitos do domínio para acessar recursos como servidores de arquivos, impressoras, hosts de intranet e outros objetos com base na associação de usuários e grupos. As contas de usuário de domínio podem fazer login em qualquer host no domínio, enquanto o usuário local só tem permissão para acessar o host específico em que foram criadas.

Vale a pena examinar a documentação sobre contas(https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/local-accounts) para entender melhor como as várias contas funcionam juntas em um sistema Windows individual e em uma rede de domínio. Reserve algum tempo para examiná-los e entender as nuances entre eles. Entender seus usos e a utilidade de cada tipo de conta pode fazer ou quebrar uma tentativa de pentester de acesso privilegiado ou lateral movement durante um teste de penetração.

# O que são User Groups?

Os grupos são uma forma de classificar contas de usuário logicamente e, ao fazer isso, fornecem permissões granulares e acesso a recursos sem precisar gerenciar cada usuário manualmente. Por exemplo, podemos restringir o acesso a um diretório ou compartilhamento específico para que apenas os usuários que precisam de acesso possam visualizar os arquivos. Em um host singular, isso não significa muito para nós. No entanto, o agrupamento lógico é essencial para manter uma postura de segurança adequada em um domínio de centenas, senão milhares, de usuários. De uma perspectiva de domínio, temos vários tipos diferentes de grupos que podem conter não apenas usuários, mas também dispositivos finais como PCs, impressoras e até mesmo outros grupos.

```powershell-session
PS C:\htb> get-localgroup

Name                                Description
----                                -----------
__vmware__                          VMware User Group
Access Control Assistance Operators Members of this group can remotely query authorization attributes and permission...
Administrators                      Administrators have complete and unrestricted access to the computer/domain
Backup Operators                    Backup Operators can override security restrictions for the sole purpose of back...
Cryptographic Operators             Members are authorized to perform cryptographic operations.
Device Owners                       Members of this group can change system-wide settings.
Distributed COM Users               Members are allowed to launch, activate and use Distributed COM objects on this ...
Event Log Readers                   Members of this group can read event logs from local machine
Guests                              Guests have the same access as members of the Users group by default, except for...
Hyper-V Administrators              Members of this group have complete and unrestricted access to all features of H...
IIS_IUSRS                           Built-in group used by Internet Information Services.
Network Configuration Operators     Members in this group can have some administrative privileges to manage configur...
Performance Log Users               Members of this group may schedule logging of performance counters, enable trace...
Performance Monitor Users           Members of this group can access performance counter data locally and remotely
Power Users                         Power Users are included for backwards compatibility and possess limited adminis...
Remote Desktop Users                Members in this group are granted the right to logon remotely
Remote Management Users             Members of this group can access WMI resources over management protocols (such a...
Replicator                          Supports file replication in a domain
System Managed Accounts Group       Members of this group are managed by the system.
Users                               Users are prevented from making accidental or intentional system-wide changes an...  
```

Acima está um exemplo dos grupos locais para um host independente. Podemos ver que existem grupos para coisas simples, como Administradores e guest accounts, mas também grupos para funções específicas como administradores para aplicativos de virtualização, usuários remotos, etc.

# Adicionando/Removendo/Editando User Accounts & Groups

Como a maioria das outras coisas no PowerShell, usamos o verbos get, new e set para localizar, criar e modificar usuários e grupos. Se estiver lidando com usuários e grupos locais, localuser e localgroup podem fazer isso. Para ativos de domínio, aduser e adgroup, fazem o truque. Se não tivéssemos certeza, sempre poderíamos usar o cmdlet `Get-Command *user*` para ver a que temos acesso. 

## Identificando Local Users

```powershell-session
PS C:\htb> Get-LocalUser  
  
Name               Enabled Description
----               ------- -----------
Administrator      False   Built-in account for administering the computer/domain
DefaultAccount     False   A user account managed by the system.
DLarusso           True    High kick specialist.
Guest              False   Built-in account for guest access to the computer/domain
sshd               True
WDAGUtilityAccount False   A user account managed and used by the system for Windows Defender A...
```

Get-LocalUser exibirá os usuários em nosso host. Esses usuários só tem acesso a esse host específico. Digamos que queremos criar um novo usuário local chamado JLawrence. Podemos realizar a tarefa usando New-LocalUser. Se não tiver certeza da sintaxe adequada, não se esqueça do comando Get-Help. Ao criar um novo usuário local, o único requisito real de uma perspectiva de sintaxe é inserir um nome e especificar uma senha (ou -NoPassword). Todas as outras configurações, como descrição ou expiração da conta, são opcionais.

# Criando um novo usuário

```powershell-session
PS C:\htb>  New-LocalUser -Name "JLawrence" -NoPassword

Name      Enabled Description
----      ------- -----------
JLawrence True
```

Acima, criamos o usuário JLawrence e não definimos uma senha. Portanto, esta conta está ativa e pode ser acessada sem uma senha. Dependendo da versão do Windows que estamos usando, ao não definir uma senha, estamos sinalizando para o Windows que esta é uma conta ativa da Microsoft e ela tenta fazer login dessa maneira em vez de usar uma senha local.

Se quisermos modificar um usuário, podemos usar o cmdlet Set-LocalUser. Para este exemplo, modificaremos JLawrence e definiremos uma senha e uma descrição em sua conta.

```powershell-session
PS C:\htb> $Password = Read-Host -AsSecureString
****************
PS C:\htb> Set-LocalUser -Name "JLawrence" -Password $Password -Description "CEO EagleFang"
PS C:\htb> Get-LocalUser  

Name               Enabled Description
----               ------- -----------
Administrator      False   Built-in account for administering the computer/domain
DefaultAccount     False   A user account managed by the system.
demo               True
Guest              False   Built-in account for guest access to the computer/domain
JLawrence          True    CEO EagleFang
```

Quanto a criar e modificar usuários, é tão simples quanto o que vemos acima. Agora, vamos verificar os grupos. Se parece um echo.. bem, é. Os comandos são semelhantes em uso.

# Get-Localgroup

```powershell-session
PS C:\htb> Get-LocalGroup  

Name                                Description
----                                -----------
Access Control Assistance Operators Members of this group can remotely query authorization attr...
Administrators                      Administrators have complete and unrestricted access to the...
Backup Operators                    Backup Operators can override security restrictions for the...
Cryptographic Operators             Members are authorized to perform cryptographic operations.
Device Owners                       Members of this group can change system-wide settings.
Distributed COM Users               Members are allowed to launch, activate and use Distributed...
Event Log Readers                   Members of this group can read event logs from local machine
Guests                              Guests have the same access as members of the Users group b...
Hyper-V Administrators              Members of this group have complete and unrestricted access...
IIS_IUSRS                           Built-in group used by Internet Information Services.
Network Configuration Operators     Members in this group can have some administrative privileg...
Performance Log Users               Members of this group may schedule logging of performance c...
Performance Monitor Users           Members of this group can access performance counter data l...
Power Users                         Power Users are included for backwards compatibility and po...
Remote Desktop Users                Members in this group are granted the right to logon remotely
Remote Management Users             Members of this group can access WMI resources over managem...
Replicator                          Supports file replication in a domain
System Managed Accounts Group       Members of this group are managed by the system.
Users                               Users are prevented from making accidental or intentional s...

PS C:\Windows\system32> Get-LocalGroupMember -Name "Users"

ObjectClass Name                             PrincipalSource
----------- ----                             ---------------
User        DESKTOP-B3MFM77\demo             Local
User        DESKTOP-B3MFM77\JLawrence        Local
Group       NT AUTHORITY\Authenticated Users Unknown
Group       NT AUTHORITY\INTERACTIVE         Unknown
```

Na saída acima, executamos o cmdlet Get-LocalGroup para obter uma impressão de cada grupo no host. No segundo comando, decidimos inspecionar o grupo Users e ver quem é membro desse grupo. Fizemos isso com o comando Get-LocalGroupMember. Agora, se quisermos adicionar outro grupo ou usuário a um grupo, podemos usar o comando Add-LocalGroupMember. Adicionaremos, JLawrence ao grupo Remote Desktop Users no exemplo abaixo:

```powershell-session
PS C:\htb> Add-LocalGroupMember -Group "Remote Desktop Users" -Member "JLawrence"
PS C:\htb> Get-LocalGroupMember -Name "Remote Desktop Users" 

ObjectClass Name                      PrincipalSource
----------- ----                      ---------------
User        DESKTOP-B3MFM77\JLawrence Local
```

Depois de executar o comando, verificamos a associação do grupo e vimos que nosso usuário foi realmente adicionado ao grupo Remote Desktop Users. A manutenção de usuários e grupos locais é simples e não requer módulos externos. Gerenciar usuários e grupos do Active Directory requer um pouco mais de trabalho.

# Gerenciando Domain Users e Groups

Antes de podermos acessar os cmdlets que precisamos e trabalhar com o Active Directory, devemos instalar o ActiveDirectory PowerShell Module. Se você instalou o AdminToolbox, o módulo AD pode já estar em seu host.

```powershell-session
PS C:\htb> Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online

Path          :  
Online        : True  
RestartNeeded : False  
```

O comando acima instalará TODOS os recursos do RSAT no Catálogo da Microsoft. Se quisermos ficar leves, podemos instalar o pacote chamado Rsat.ActiveDirectory.DS-LDS.Tools~~~~ 0.0.1.0. Agora devemos ter o módulo ActiveDirectory instalado. Vamos verificar.

```powershell-session
PS C:\htb> Get-Module -Name ActiveDirectory -ListAvailable 

    Directory: C:\Windows\system32\WindowsPowerShell\v1.0\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   1.0.1.0    ActiveDirectory                     {Add-ADCentralAccessPolicyMember, Add-ADComputerServiceAccount, Add-ADDomainControllerPasswordReplicationPolicy, Add-A...
```

Legal. Agora que temos o módulo, podemos começar com o gerenciamento de usuários e grupos do AD. A maneira mais fácil de localizar um usuário específico é pesquisando com o cmdlet Get-ADUser.

```powershell-session
PS C:\htb> Get-ADUser -Filter *

DistinguishedName : CN=user14,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : user14
ObjectClass       : user
ObjectGUID        : bef9787d-2716-4dc9-8e8f-f8037a72c3d9
SamAccountName    : user14
SID               : S-1-5-21-1480833693-1324064541-2711030367-1110
Surname           :
UserPrincipalName :

DistinguishedName : CN=sshd,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : sshd
ObjectClass       : user
ObjectGUID        : 7a324e98-00e4-480b-8a1a-fa465d558063
SamAccountName    : sshd
SID               : S-1-5-21-1480833693-1324064541-2711030367-1112
Surname           :
UserPrincipalName :

DistinguishedName : CN=TSilver,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : TSilver
ObjectClass       : user
ObjectGUID        : a19a6c8a-000a-4cbf-aa14-0c7fca643c37
SamAccountName    : TSilver
SID               : S-1-5-21-1480833693-1324064541-2711030367-1602
Surname           :
UserPrincipalName :  

<SNIP>
```

O parâmetro -Filter * nos permite pegar todos os usuários dentro do ActiveDirectory. Dependendo do tamanho da nossa organização, isso pode gerar uma tonelada de resultados.

Podemos usar o parâmetro -Identity para realizar uma pesquisa mais específica de um usuário por nome distinto, GUID, objectSid ou SamAccountName.

```powershell-session
PS C:\htb>  Get-ADUser -Identity TSilver


DistinguishedName : CN=TSilver,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : TSilver
ObjectClass       : user
ObjectGUID        : a19a6c8a-000a-4cbf-aa14-0c7fca643c37
SamAccountName    : TSilver
SID               : S-1-5-21-1480833693-1324064541-2711030367-1602
Surname           :
UserPrincipalName :
```

Podemos ver na saída várias informações sobre o usuário, incluindo:

- Object Class: que especifica se o objeto é um usuário, computador ou outro tipo de objeto.
- DistinguishedName: especifica o caminho relativo do objeto dentro do AD schema.
- Enabled: informa se o usuário está ativo e pode fazer login.
- SamAccountName: a representação do nome de usuário usado para fazer login nos hosts do ActiveDirectory.
- ObjectGUID: é o identificador exclusivo do objeto do usuário.

Os usuários têm muitos atributos diferentes (nem todos mostrados aqui) e podem ser usados ​​para identificá-los e agrupá-los. Também poderíamos usá-los para filtrar atributos específicos. Por exemplo, vamos filtrar o endereço de e-mail do usuário.

```powershell-session
PS C:\htb> Get-ADUser -Filter {EmailAddress -like '*greenhorn.corp'}


DistinguishedName : CN=TSilver,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : TSilver
ObjectClass       : user
ObjectGUID        : a19a6c8a-000a-4cbf-aa14-0c7fca643c37
SamAccountName    : TSilver
SID               : S-1-5-21-1480833693-1324064541-2711030367-1602
Surname           :
UserPrincipalName :
```

Em nossa saída, podemos ver que tivemos apenas um resultado para um usuário com um endereço de e-mail correspondente ao nosso contexto de nomenclatura `*greenhorn.corp`. Este é apenas um exemplo de atributos que podemos filtrar. Para obter uma lista mais detalhada, confira este artigo da Technet(https://social.technet.microsoft.com/wiki/contents/articles/12037.active-directory-get-aduser-default-and-extended-properties.aspx), que abrange as propriedades de objeto de usuário padrão e estendidas.

Precisamos criar um novo usuário para um funcionário chamado Mori Tanaka, que acabou de ingressar no GreenHorn. Vamos experimentar o cmdlet New-ADUser:

```powershell-session
PS C:\htb> New-ADUser -Name "MTanaka" -Surname "Tanaka" -GivenName "Mori" -Office "Security" -OtherAttributes @{'title'="Sensei";'mail'="MTanaka@greenhorn.corp"} -Accountpassword (Read-Host -AsSecureString "AccountPassword") -Enabled $true 

AccountPassword: ****************
PS C:\htb> Get-ADUser -Identity MTanaka -Properties * | Format-Table Name,Enabled,GivenName,Surname,Title,Office,Mail

Name    Enabled GivenName Surname Title  Office   Mail
----    ------- --------- ------- -----  ------   ----
MTanaka    True Mori      Tanaka  Sensei Security MTanaka@greenhorn.corp
```

- New-ADUser -Name "MTanaka" : emitimos o comando New-ADUser e definimos o SamAccountName do usuário como Mtanaka.
- -Surname "Tanaka" -GivenName "Mori": Esta parte define o sobrenome e o nome do usuário.
- -Office "Security": Define a propriedade estendida do Office para Security.
- -OtherAttributes `@{'title'="Sensei";'mail'="MTanaka@greenhorn.corp"}`: Aqui definimos outros atributos estendidos, como título e endereço de e-mail.
- -Accountpassword (Read-Host -AsSecureString "AccountPassword"): Com esta parte, definimos a senha do usuário fazendo com que o shell nos solicite uma nova senha. (podemos vê-lo na linha abaixo com as estrelas)
- -Enabled $true: Estamos habilitando a conta para uso. O usuário não poderia efetuar login se isso fosse definido como \$False.

A segunda é validar se o usuário que criamos e as propriedades que definimos existem:

- Get-ADUser -Identity MTanaka -Properties * : Aqui, estamos procurando as propriedades do usuário MTanaka.
- | : Este é o símbolo do Pipe. Ele pega nossa saída de Get-ADUser e a envia para o comando a seguir.
- Format-Table Name,Enabled,GivenName,Surname,Title,Office,Mail: Aqui, dizemos ao PowerShell para formatar nossos resultados como uma tabela, incluindo as propriedades padrão e estendidas listadas.

Ver os comentários divididos dessa maneira ajuda a desmistificar as strings. Agora, e se precisarmos modificar um usuário? Set-ADUser é o comando a ser utilizado. Muitos dos outros filtros vistos anteriormente também se aplicam aqui. Podemos alterar ou definir qualquer um dos atributos listados. Para este exemplo, vamos adicionar uma Description ao Tanaka.

```powershell-session
PS C:\htb> Set-ADUser -Identity MTanaka -Description " Sensei to Security Analyst's Rocky, Colt, and Tum-Tum"  

PS C:\htb> Get-ADUser -Identity MTanaka -Property Description


Description       :  Sensei to Security Analyst's Rocky, Colt, and Tum-Tum
DistinguishedName : CN=MTanaka,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         : Mori
Name              : MTanaka
ObjectClass       : user
ObjectGUID        : c19e402d-b002-4ca0-b5ac-59d416166b3a
SamAccountName    : MTanaka
SID               : S-1-5-21-1480833693-1324064541-2711030367-1603
Surname           : Tanaka
UserPrincipalName :
```

Consultando o AD, podemos ver que a descrição que definimos foi adicionada aos atributos do Mr.Tanaka. O gerenciamento de usuário e grupos é uma tarefa comum que podemos fazer como sysadmins. No entanto, porque devemos nos preocupar com isso como pentester?

# Porque enumerar Users & Groups é importante?

Usuários e grupos oferecem uma grande variedade de oportunidades relacionadas ao Pentesting em um ambiente Windows. Frequentemente veremos usuários mal configurados. Eles podem receber permissões excessivas, adicionados a grupos desnecessários ou ter senhas fracas/sem senhas definidas. Grupos podem ser igualmente valiosos. Frequentemente, os grupos terão membros aninhados, permitindo que os usuários obtenham privilégios de que talvez não precisem. Essas configurações incorretas podem ser facilmente encontradas e visualizadas com ferramentas como o Bloodhound.