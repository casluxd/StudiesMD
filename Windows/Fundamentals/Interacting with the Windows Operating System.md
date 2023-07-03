
## Graphical User Interface

O conceito de interface gráfica do usuário (GUI) foi introduzido no final dos anos 1970 pelo laboratório de pesquisa Xerox Palo Alto. Ele foi adicionado aos sistemas operacionais da Apple e da Microsoft para resolver problemas de usabilidade para usuários comuns que provavelmente teriam dificuldade em navegar na linha de comando. A maioria dos usuários casuais de computador com Windows não precisa interagir com o sistema operacional por meio da linha de comando. Como o nome indica, uma GUI fornece aos usuários uma interface interativa de apontar e clicar para interagir com o sistema operacional e aplicativos e serviços instalados.

A introdução da GUI abriu um amplo apelo e acesso a computadores em muitos grupos demográficos, pois os usuários seriam capazes de interagir com seus computadores sem ter que memorizar comandos ou conhecer qualquer linguagem de programação. Os administradores de sistemas geralmente usam sistemas baseados em GUI para administrar o Active Directory, configurar o IIS ou interagir com bancos de dados.

## Remote Desktop Protocol (RDP)

O RDP é um protocolo proprietário da Microsoft que permite que um usuário se conecte a um sistema remoto por meio de uma conexão de rede e obtenha uma interface gráfica do usuário. O usuário se conecta usando o software cliente RDP a um sistema de destino executando o software servidor RDP. O RDP usa a porta 3389 para abrir um canal de rede dedicado para enviar e receber dados. Ao conectar via RDP, um usuário pode acessar a GUI como se estivesse realmente sentado no computador e fazendo login localmente. O RDP é frequentemente usado por administradores de sistema para administrar sistemas remotos rapidamente. Ele também pode permitir que os usuários acessem seus computadores de trabalho quando viajam ou trabalham em casa depois de se conectarem a uma rede privada virtual (VPN).

## Windows Command Line

As interfaces de linha de comando oferecem aos usuários maior controle sobre seus sistemas e podem ser usadas para executar uma ampla variedade de tarefas diárias, administrativas e de solução de problemas. Ele pode ser aproveitado para introduzir automação para executar determinadas tarefas rapidamente (como adicionar muitos usuários a um domínio de uma só vez). Nos sistemas operacionais Windows, as duas principais formas de interagir com o sistema a partir da linha de comando são por meio do prompt de comando (CMD) e do PowerShell.

O Windows Command Reference(https://download.microsoft.com/download/5/8/9/58911986-D4AD-4695-BF63-F734CD4DF8F2/ws-commands.pdf) da Microsoft é uma referência de comando A-Z abrangente que inclui uma visão geral, exemplos de uso e sintaxe de comando para a maioria dos comandos do Windows, e familiaridade com ele é recomendada.

## CMD

O prompt de comando (cmd.exe) é usado para inserir e executar comandos. Um usuário pode inserir comandos únicos, como ipconfig, para visualizar informações de endereço IP ou executar tarefas mais avançadas, como configurar tarefas agendadas ou criar scripts e arquivos em lote. O prompt de comando pode ser aberto no menu Iniciar, digitando cmd na caixa de diálogo Executar ou iniciando diretamente o binário em `C:\Windows\system32\cmd.exe`.

Depois de iniciar o cmd.exe, podemos digitar help para ver uma lista de comandos disponíveis.

```cmd-session
C:\htb> help
For more information on a specific command, type HELP command-name
ASSOC          Displays or modifies file extension associations.
ATTRIB         Displays or changes file attributes.
BREAK          Sets or clears extended CTRL+C checking.
BCDEDIT        Sets properties in boot database to control boot loading.
CACLS          Displays or modifies access control lists (ACLs) of files.
CALL           Calls one batch program from another.
CD             Displays the name of or changes the current directory.
CHCP           Displays or sets the active code page number.
CHDIR          Displays the name of or changes the current directory.
CHKDSK         Checks a disk and displays a status report.
CHKNTFS        Displays or modifies the checking of disk at boot time.
CLS            Clears the screen.
CMD            Starts a new instance of the Windows command interpreter.
COLOR          Sets the default console foreground and background colors.
COMP           Compares the contents of two files or sets of files.
COMPACT        Displays or alters the compression of files on NTFS partitions.
CONVERT        Converts FAT volumes to NTFS.  You cannot convert the
               current drive.
COPY           Copies one or more files to another location.

<SNIP>
```

Para obter mais informações sobre um comando específico, podemos digitar `help <nome do comando>`.

```cmd-session
C:\htb> help schtasks

SCHTASKS /parameter [arguments]

Description:
    Enables an administrator to create, delete, query, change, run and
    end scheduled tasks on a local or remote system.

Parameter List:
    /Create         Creates a new scheduled task.

    /Delete         Deletes the scheduled task(s).

    /Query          Displays all scheduled tasks.

    /Change         Changes the properties of scheduled task.

    /Run            Runs the scheduled task on demand.

    /End            Stops the currently running scheduled task.

    /ShowSid        Shows the security identifier corresponding to a scheduled task name.

    /?              Displays this help message.

Examples:
    SCHTASKS
    SCHTASKS /?
    SCHTASKS /Run /?
    SCHTASKS /End /?
    SCHTASKS /Create /?
    SCHTASKS /Delete /?
    SCHTASKS /Query  /?
    SCHTASKS /Change /?
    SCHTASKS /ShowSid /?
```

Observe que determinados comandos possuem seus próprios menus de ajuda, que podem ser acessados ​​digitando <command> /?. Por exemplo, informações sobre o comando ipconfig podem ser vistas abaixo.

```cmd-session
C:\htb> ipconfig /?

USAGE:
    ipconfig [/allcompartments] [/? | /all |
                                 /renew [adapter] | /release [adapter] |
                                 /renew6 [adapter] | /release6 [adapter] |
                                 /flushdns | /displaydns | /registerdns |
                                 /showclassid adapter |
                                 /setclassid adapter [classid] |
                                 /showclassid6 adapter |
                                 /setclassid6 adapter [classid] ]

where
    adapter             Connection name
                       (wildcard characters * and ? allowed, see examples)

    Options:
       /?               Display this help message
       /all             Display full configuration information.
       /release         Release the IPv4 address for the specified adapter.
       /release6        Release the IPv6 address for the specified adapter.
       /renew           Renew the IPv4 address for the specified adapter.
       /renew6          Renew the IPv6 address for the specified adapter.
       /flushdns        Purges the DNS Resolver cache.
       /registerdns     Refreshes all DHCP leases and re-registers DNS names
       /displaydns      Display the contents of the DNS Resolver Cache.
       /showclassid     Displays all the dhcp class IDs allowed for adapter.
       /setclassid      Modifies the dhcp class id.
       /showclassid6    Displays all the IPv6 DHCP class IDs allowed for adapter.
       /setclassid6     Modifies the IPv6 DHCP class id.

<SNIP
```

## PowerShell

O Windows PowerShell é um shell de comando projetado pela Microsoft para ser mais voltado para administradores de sistema. O PowerShell, como a linha de comando do Windows, possui um prompt de comando interativo, bem como um poderoso ambiente de script. O PowerShell é construído sobre o .NET Framework, que é usado para criar e executar aplicativos no Windows. Isso o torna uma ferramenta muito poderosa para interface direta com o sistema operacional. 

Como o prompt de comando, o PowerShell nos dá acesso direto ao sistema de arquivos e executamos a maioria dos mesmos comandos que podemos dentro de um shell cmd.

### Cmdlets

O PowerShell utiliza cmdlets, que são pequenas ferramentas de função única incorporadas ao shell. Existem mais de 100 cmdlets principais e muitos adicionais foram escritos, ou podemos criar nossos próprios para executar tarefas mais complexas. O PowerShell também oferece suporte a scripts simples e complexos usados ​​para tarefas de administração do sistema, automação e muito mais.

Os cmdlets estão na forma de Verb-Noun. Por exemplo, o comando Get-ChildItem pode ser usado para listar nosso diretório atual. Os cmdlets também aceitam argumentos ou sinalizadores. Podemos digitar Get-ChildItem - e pressionar a tecla tab para percorrer os argumentos. Um comando como Get-ChildItem -Recurse nos mostrará o conteúdo de nosso diretório de trabalho atual e todos os subdiretórios. Outro exemplo seria Get-ChildItem -Path `C:\Users\Administrator\Documents` para obter o conteúdo de outro diretório.

Finalmente, podemos combinar argumentos como este para listar o conteúdo de todos os subdiretórios dentro de outro diretório recursivamente: Get-ChildItem -Path `C:\Users\Administrator\Downloads` -Recurse.

## Aliases

Muitos cmdlets no PowerShell também têm aliases. Por exemplo, os aliases para o cmdlet Set-Location, para alterar diretórios, são cd ou sl. Enquanto isso, os aliases para Get-ChildItem são ls e gci. Podemos visualizar todos os aliases disponíveis digitando Get-Alias.

```powershell-session
PS C:\htb> get-alias

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Microsoft.PowerShell.Utility
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
```

Também podemos configurar nossos próprios aliases com New-Alias ​​e obter o alias para qualquer cmdlet com Get-Alias ​​-Name.

```powershell-session
PS C:\htb> New-Alias -Name "Show-Files" Get-ChildItem
PS C:\> Get-Alias -Name "Show-Files"

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Show-Files
```


O PowerShell possui um sistema de ajuda para cmdlets, funções, scripts e conceitos. Isso não é instalado por padrão, mas podemos executar o comando `Get-Help <cmdlet-name> -Online` para abrir a ajuda online para um cmdlet ou função em nosso navegador da web. Podemos digitar Update-Help para baixar e instalar arquivos de ajuda localmente.

```powershell-session
PS C:\htb> help

TOPIC
    Windows PowerShell Help System

SHORT DESCRIPTION
    Displays help about Windows PowerShell cmdlets and concepts.

LONG DESCRIPTION
    Windows PowerShell Help describes Windows PowerShell cmdlets,
    functions, scripts, and modules, and explains concepts, including
    the elements of the Windows PowerShell language.

    Windows PowerShell does not include help files, but you can read the
    help topics online, or use the Update-Help cmdlet to download help files
    to your computer and then use the Get-Help cmdlet to display the help
    topics at the command line.

    You can also use the Update-Help cmdlet to download updated help files
    as they are released so that your local help content is never obsolete.

    Without help files, Get-Help displays auto-generated help for cmdlets,
    functions, and scripts.


  ONLINE HELP
    You can find help for Windows PowerShell online in the TechNet Library
    beginning at http://go.microsoft.com/fwlink/?LinkID=108518.

    To open online help for any cmdlet or function, type:

        Get-Help <cmdlet-name> -Online
		
<SNIP>
```

Digitar um comando como Get-Help Get-AppPackage retornará apenas a ajuda parcial, a menos que os arquivos de Ajuda estejam instalados.

```powershell-session
PS C:\htb>  Get-Help Get-AppPackage

NAME
    Get-AppxPackage

SYNTAX
    Get-AppxPackage [[-Name] <string>] [[-Publisher] <string>] [-AllUsers] [-PackageTypeFilter {None | Main |
    Framework | Resource | Bundle | Xap | Optional | All}] [-User <string>] [-Volume <AppxVolume>]
    [<CommonParameters>]


ALIASES
    Get-AppPackage


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
```

### Rodando scripts

O PowerShell ISE (Integrated Scripting Environment) permite que os usuários escrevam scripts do PowerShell em tempo real. Ele também possui uma função de preenchimento automático/pesquisa para comandos do PowerShell. O ISE do PowerShell nos permite escrever e executar scripts no mesmo console, o que permite uma depuração rápida.

Podemos executar scripts do PowerShell de várias maneiras. Se conhecermos as funções, podemos executar o script localmente ou depois de carregá-lo na memória com uma base de download como no exemplo abaixo.

```powershell-session
PS C:\htb> .\PowerView.ps1;Get-LocalGroup |fl

Description     : Users of Docker Desktop
Name            : docker-users
SID             : S-1-5-21-674899381-4069889467-2080702030-1004
PrincipalSource : Local
ObjectClass     : Group

Description     : VMware User Group
Name            : __vmware__
SID             : S-1-5-21-674899381-4069889467-2080702030-1003
PrincipalSource : Local
ObjectClass     : Group

Description     : Members of this group can remotely query authorization attributes and permissions for resources on
                  this computer.
Name            : Access Control Assistance Operators
SID             : S-1-5-32-579
PrincipalSource : Local
ObjectClass     : Group

Description     : Administrators have complete and unrestricted access to the computer/domain
Name            : Administrators
SID             : S-1-5-32-544
PrincipalSource : Local

<SNIP>
```

Uma maneira comum de trabalhar com um script no PowerShell é importá-lo para que todas as funções fiquem disponíveis em nossa sessão atual do console do PowerShell: `Import-Module .\PowerView.ps1`. 

Podemos então iniciar um comando e percorrer as opções ou digitar Get-Module para listar todos os módulos carregados e seus comandos associados.

```powershell-session
PS C:\htb> Get-Module | select Name,ExportedCommands | fl


Name             : Appx
ExportedCommands : {[Add-AppxPackage, Add-AppxPackage], [Add-AppxVolume, Add-AppxVolume], [Dismount-AppxVolume,
                   Dismount-AppxVolume], [Get-AppxDefaultVolume, Get-AppxDefaultVolume]...}

Name             : Microsoft.PowerShell.LocalAccounts
ExportedCommands : {[Add-LocalGroupMember, Add-LocalGroupMember], [Disable-LocalUser, Disable-LocalUser],
                   [Enable-LocalUser, Enable-LocalUser], [Get-LocalGroup, Get-LocalGroup]...}

Name             : Microsoft.PowerShell.Management
ExportedCommands : {[Add-Computer, Add-Computer], [Add-Content, Add-Content], [Checkpoint-Computer,
                   Checkpoint-Computer], [Clear-Content, Clear-Content]...}

Name             : Microsoft.PowerShell.Utility
ExportedCommands : {[Add-Member, Add-Member], [Add-Type, Add-Type], [Clear-Variable, Clear-Variable], [Compare-Object,
                   Compare-Object]...}

Name             : PSReadline
ExportedCommands : {[Get-PSReadLineKeyHandler, Get-PSReadLineKeyHandler], [Get-PSReadLineOption,
                   Get-PSReadLineOption], [Remove-PSReadLineKeyHandler, Remove-PSReadLineKeyHandler],
                   [Set-PSReadLineKeyHandler, Set-PSReadLineKeyHandler]...}
```

## Execution Policy

Às vezes, descobriremos que não podemos executar scripts em um sistema. Isso se deve a um recurso de segurança chamado política de execução, que tenta impedir a execução de scripts maliciosos. As políticas possíveis são:

- AllSigned: Todos os scripts podem ser executados, mas um editor confiável deve assinar scripts e arquivos de configuração. Isso inclui scripts remotos e locais. Recebemos uma solicitação antes de executar scripts assinados por editores que ainda não listamos como confiáveis ​​ou não confiáveis.
- Bypass: Nenhum script ou arquivo de configuração é bloqueado e o usuário não recebe avisos ou prompts.
- Default: Isso define a política de execução padrão, Restrito para máquinas de desktop Windows e RemoteSigned para servidores Windows.
- RemoteSigned: Os scripts podem ser executados, mas requerem uma assinatura digital em scripts baixados da Internet. As assinaturas digitais não são necessárias para scripts escritos localmente.
- Restricted: Isso permite comandos individuais, mas não permite a execução de scripts. Todos os tipos de arquivo de script, incluindo arquivos de configuração (.ps1xml), arquivos de script de módulo (.psm1) e perfis do PowerShell (.ps1) são bloqueados.
- Undefined: Nenhuma política de execução está definida para o escopo atual. Se a política de execução para TODOS os escopos for definida como indefinida, a política de execução padrão Restrita será usada.
- Unrestricted: Esta é a política de execução padrão para computadores não Windows e não pode ser alterada. Esta política permite que scripts não assinados sejam executados, mas avisa o usuário antes de executar scripts que não são da zona da intranet local.

```powershell-session
PS C:\htb> Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

A política de execução não pretende ser um controle de segurança que restrinja as ações do usuário. Um usuário pode ignorar facilmente a política digitando o conteúdo do script diretamente na janela do PowerShell, baixando e invocando o script ou especificando o script como um comando codificado. Também pode ser ignorado ajustando a política de execução (se o usuário tiver os direitos apropriados) ou definindo a política de execução para o escopo do processo atual que pode ser feito por quase qualquer usuário, pois não requer alteração de configuração e será definido apenas durante a sessão do usuário).

```powershell-session
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```

Agora podemos ver que a política de execução foi alterada.

```powershell-session
PS C:\htb>  Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

