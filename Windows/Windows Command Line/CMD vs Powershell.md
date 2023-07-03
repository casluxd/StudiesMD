
Powershell é considerado o sucessor moderno do Windows para o CMD.

# Comparação

- Language
CMD: Apenas batch e comandos básicos do CMD.
PowerShell: Pode interpretar Batch, CMD, PS cmdlets e aliases.

- Command utilization
CMD: O output de um comando não pode ser para outro diretamente.
PowerShell: O output de um comando pode ser passado para outro diretamente.

- Command Output
CMD: Texto apenas
Powershell: Saídas do powershell em formatação de objeto

- Parallel Execution
CMD: Deve finalizar a execução de um comando para executar outro.
Powershell: O powershell pode executar comandos em multithread em paralelo.

Mais notavelmente, PowerShell foi criado para ser extensível e integrar-se a muitas outras ferramentas e funcionalidades conforme necessário. A maioria pensa nisso como apenas outro CLI, mas é muito mais, também é uma linguagem de script. Embora o CMD tenha sido o interpretador de linha de comando padrão apenas para hosts Windows, o PowerShell foi lançado como um projeto de código aberto e possui uma ampla oferta de recursos que também suportam seu uso com sistemas baseados em Linux. O uso da estrutura .NET também tornou o PowerShell capaz de utilizar um modelo de base de objeto de interação e saída em vez de apenas baseados em texto.

# Porque escolher o PowerShell em vez do cmd?

O PowerShell tornou-se cada vez mais proeminente entre os profissionais de TI e Infosec. Tem ampla utilidade para System Administrators, Penetration Testers, SOC Analysts e muitas outras disciplinas técnicas onde os sistemas Windows são administrados. Considere IT admins e Windows System Administrators administrando ambientes de TI compostos de servidores Windows, Desktops (Windows 10 & 11), Azure e aplicativos baseados em nuvem do Microsoft 365. Muitos deles estão usando o PowerShell para automatizar tarefas que devem realizar diariamente. Entre algumas dessas tarefas estão:

- Provisionando servidores e instalando server roles
- Criação de contas de usuário do Active Directory para novos funcionários
- Gerenciando permissões de grupo do Active Directory
- Desativando e excluindo contas de usuário do Active Directory
- Gerenciando permissões de compartilhamento de arquivos
- Interagindo com Azure AD e Azure VMs
- Criação, deleção e monitoramento de diretórios e arquivos
- Obter informações sobre workstations e servidores
- Configurações de caixas de entrada de e-mail do Microsoft Exchange para usuários (na nuvem e/ou no local)

Existem inúmeras maneiras de usar o PowerShell em um contexto de administração de TI e estar atento a esse contexto pode ser útil para nós como penetration testers e até mesmo defensores. Como administrador de sistema, o PowerShell pode nos fornecer muito mais recursos do que o CMD. Ele é expansível, criado para automação e script, tem uma implementação de segurança muito mais robusta e pode lidar com muitas tarefas diferentes que o CMD simplesmente não consegue. Como pentester, muitos recursos conhecidos são incorporados ao PowerShell. O recurso de importação de módulos do PowerShell facilita trazer nossas ferramentas para o ambiente e garantir que funcionem. No entanto, de uma perspectiva furtiva, o recurso de registro e histórico do PowerShell é poderoso e registrará mais de nossas interações com o host. Portanto, se não precisarmos do PowerShell e quisermos ser mais discretos, devemos utilizar o CMD.

Similar ao CMD, PowerShell nos dá diversos comandos e cmdlets para utilizar. Quase todos os comandos que funcionam no CMD funcionarão no PowerShell. É essencial entender que há muita pouca utilidade em memorizar comandos. É importante concentrar-se em entender mais o contexto, os conceitos e o que é possível. A memorização acontecerá naturalmente com o tempo gasto praticando e repetindo.

## Get-Help

Usando a função Help, se quisermos ver as opçõs e funcionalidades disponíveis para nós com um cmdlet específico, podemos usar o cmdlet Get-Help

```powershell-session
PS C:\Users\htb-student> Get-Help Test-Wsman

NAME
    Test-WSMan

SYNTAX
    Test-WSMan [[-ComputerName] <string>] [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-Port <int>] [-UseSSL] [-ApplicationName <string>] [-Credential <pscredential>]
    [-CertificateThumbprint <string>]  [<CommonParameters>]


ALIASES
    None


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
        -- To view the Help topic for this cmdlet online, type: "Get-Help Test-WSMan -Online" or
           go to https://go.microsoft.com/fwlink/?LinkId=141464.
```

Get-Help pode fornecer informações úteis sobre um cmdlet. Observe que a saída da Syntax nos mostra várias opções disponíveis e palavras-chaves adicionais que podem ser usadas com cada opção. Aliases também são mencionados, nomes essencialmente mais curtos para nossos comandos. A saída Remarks nos forece mais informações sobre o cmdlet e até mesmo opções adicionais que podemos usar para aprender mais sobre o cmdlet. Uma dessas opções adicionais é -online, que abrirá uma página da Web de documentos da Microsoft para o cmdlet correspondente se o host tiver acesso à Internet.

Nós também podemos usar um cmdlet útil chamado Update-Help para garantir que tenhamos as informações mais atualizadas para cada cmdlet no sistema Windows.

```powershell-session
PS C:\Windows\system32> Update-Help
```

Observe quantas informações adicionais foram preenchidas em relação ao Test-Wsmam após a execução do Update-Help. 

```powershell-session
PS C:\Windows\system32> Get-Help  Test-Wsman

NAME
    Test-WSMan

SYNOPSIS
    Tests whether the WinRM service is running on a local or remote computer.


SYNTAX
    Test-WSMan [[-ComputerName] <System.String>] [-ApplicationName <System.String>]
    [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-CertificateThumbprint <System.String>]
    [-Credential <System.Management.Automation.PSCredential>] [-Port <System.Int32>]
    [-UseSSL] [<CommonParameters>]


DESCRIPTION
    The `Test-WSMan` cmdlet submits an identification request that determines
    whether the WinRM service is running on a local or remote computer. If the
    tested computer is running the service, the cmdlet displays the WS-Management
    identity schema, the protocol version, the product vendor, and the product
    version of the tested service.


RELATED LINKS
    Online Version: https://docs.microsoft.com/powershell/module/microsoft.wsman.mana
    gement/test-wsman?view=powershell-5.1&WT.mc_id=ps-gethelp
    Connect-WSMan
    Disable-WSManCredSSP
    Disconnect-WSMan
    Enable-WSManCredSSP
    Get-WSManCredSSP
    Get-WSManInstance
    Invoke-WSManAction
    New-WSManInstance
    New-WSManSessionOption
    Remove-WSManInstance
    Set-WSManInstance
    Set-WSManQuickConfig

REMARKS
    To see the examples, type: "get-help Test-WSMan -examples".
    For more information, type: "get-help Test-WSMan -detailed".
    For technical information, type: "get-help Test-WSMan -full".
    For online help, type: "get-help Test-WSMan -online"
```

# Como se locomover no PowerShell

Agora que abordamos o que é o PowerShell e os fundamentos dos recursos de ajuda integrados, vamos entrar na navegação básica e no uso do PowerShell.

## Onde estamos?

Só podemos nos movimentar se soubermos onde já estamos. Para isso, podemos determinar nosso diretório de trabalho atual (em relação ao sistema host) utilziando o cmdlet Get-Location.

```powershell-session
PS C:\htb> Get-Location

Path
----
C:\Users\DLarusso
```

Podemos vê-lo impresso com o caminho completo do diretório no qual estamos trabalhando; neste caso, seria `C:\Users\DLarusso`. Agora que já nos orientamos, vamos ver quais objetos e arquivos existem nesse diretório.

## Listar o diretório

O cmdlet **Get-ChildItem** pode exibir o conteúdo de nosso diretório atual ou daquele que especificamos.

```powershell-session
PS C:\htb> Get-ChildItem 

Directory: C:\Users\DLarusso


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/26/2021  10:26 PM                .ssh
d-----         1/28/2021   7:05 PM                .vscode
d-r---         1/27/2021   2:44 PM                3D Objects
d-r---         1/27/2021   2:44 PM                Contacts
d-r---         9/18/2022  12:35 PM                Desktop
d-r---         9/18/2022   1:01 PM                Documents
d-r---         9/26/2022  12:27 PM                Downloads
d-r---         1/27/2021   2:44 PM                Favorites
d-r---         1/27/2021   2:44 PM                Music
dar--l         9/26/2022  12:03 PM                OneDrive
d-r---         5/22/2022   2:00 PM                Pictures
```

Podemos ver vários outros diretórios dentro do nosso diretório de trabalho atual.

## Mover para um novo diretório

```powershell-session
PS C:\htb>  Set-Location .\Documents\

PS C:\Users\tru7h\Documents> Get-Location

Path
----
C:\Users\DLarusso\Documents
```

## Mostrar o conteúdo de um arquivo

```powershell-session
PS C:\htb> Get-Content Readme.md  

# ![logo][] PowerShell

Welcome to the PowerShell GitHub Community!
PowerShell Core is a cross-platform (Windows, Linux, and macOS) automation and configuration tool/framework that works well with your existing tools and is optimized
for dealing with structured data (e.g., JSON, CSV, XML, etc.), REST APIs, and object models.
It includes a command-line shell, an associated scripting language and a framework for processing cmdlets. 

<SNIP> 
```

# Dicas e truques para o uso do PowerShell

Get-Command é uma ótima maneira de encontrar um comando irritante que pode estar escapando de nossa memória no momento em que precisamos usá-lo. Com o PowerShell usando a convenção verbo-substantivo para cmdlets, podemos pesquisar em qualquer um deles.

```powershell-session
PS C:\htb> Get-Command

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Add-AppPackage                                     2.0.1.0    Appx
Alias           Add-AppPackageVolume                               2.0.1.0    Appx
Alias           Add-AppProvisionedPackage                          3.0        Dism
Alias           Add-ProvisionedAppPackage                          3.0        Dism
Alias           Add-ProvisionedAppxPackage                         3.0        Dism
Alias           Add-ProvisioningPackage                            3.0        Provisioning
Alias           Add-TrustedProvisioningCertificate                 3.0        Provisioning
Alias           Apply-WindowsUnattend                              3.0        Dism
Alias           Disable-PhysicalDiskIndication                     2.0.0.0    Storage
Alias           Disable-StorageDiagnosticLog                       2.0.0.0    Storage
Alias           Dismount-AppPackageVolume                          2.0.1.0    Appx
Alias           Enable-PhysicalDiskIndication                      2.0.0.0    Storage
Alias           Enable-StorageDiagnosticLog                        2.0.0.0    Storage
Alias           Flush-Volume                                       2.0.0.0    Storage
Alias           Get-AppPackage                                     2.0.1.0    Appx

<SNIP>  
```

A saída acima foi cortada para economizar espaço na tela. O uso de Get-Command sem modificadores adicionais executará uma saída completa de cada cmetlet atualmente carregado na sessão do PowerShell. Podemos reduzir isso mais filtrando o verbo ou parte do substantivo do cmdlet.

```powershell-session
PS C:\htb> Get-Command -verb get

<SNIP>
Cmdlet          Get-Acl                                            3.0.0.0    Microsoft.Pow...
Cmdlet          Get-Alias                                          3.1.0.0    Microsoft.Pow...
Cmdlet          Get-AppLockerFileInformation                       2.0.0.0    AppLocker
Cmdlet          Get-AppLockerPolicy                                2.0.0.0    AppLocker
Cmdlet          Get-AppvClientApplication                          1.0.0.0    AppvClient  
<SNIP>  
```

Usando o modificador -verb e procurando qualquer cmdlet, alias ou função com o termo get no nome, recebemos uma lista detalhada de tudo o que o PowerShell está ciente atualmente. Também podemos realizar a busca exata usando o filtro get* ao inveś do -verb get. O cmdlet Get-Command reconhece o * como um curinga e mostra cada variante de get. Podemos fazer algo semelhante usando o substantivo(noun).

```powershell-session
PS C:\htb> Get-Command -noun windows*  

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Apply-WindowsUnattend                              3.0        Dism
Function        Get-WindowsUpdateLog                               1.0.0.0    WindowsUpdate
Cmdlet          Add-WindowsCapability                              3.0        Dism
Cmdlet          Add-WindowsDriver                                  3.0        Dism
Cmdlet          Add-WindowsImage                                   3.0        Dism
Cmdlet          Add-WindowsPackage                                 3.0        Dism
Cmdlet          Clear-WindowsCorruptMountPoint                     3.0        Dism
Cmdlet          Disable-WindowsErrorReporting                      1.0        WindowsErrorR...
Cmdlet          Disable-WindowsOptionalFeature                     3.0        Dism
Cmdlet          Dismount-WindowsImage                              3.0        Dism
Cmdlet          Enable-WindowsErrorReporting                       1.0        WindowsErrorR...
Cmdlet          Enable-WindowsOptionalFeature                      3.0        Dism
```

## History

O PowerShell mantém um histórico dos comandos executados de duas maneiras diferentes. O primeiro é o histórico de sessão integrado que é implementado e excluído no início e final de cada sessão do console. A outra é através do módulo PSReadLine. O módulo PSReadLine rastreia o histórico de quaisquer comandos do PowerShell usados em todas as sessões no host, entre muitos outros recursos. Por padrão, o PowerShell mantém os últimos 4096 comandos inseridos, mas essa configuração pode ser modificada alterando a variável $MaximumHistoryCount.

```powershell-session
PS C:\htb> Get-History

 Id CommandLine
  -- -----------
   1 Get-Command
   2 clear
   3 get-command -verb set
   4 get-command set*
   5 clear
   6 get-command -verb get
   7 get-command -noun windows
   8 get-command -noun windows*
   9 get-module
  10 clear
  11 get-history
  12 clear
  13 ipconfig /all
  14 arp -a
  15 get-help
  16 get-help get-module
```

Por padrão, Get-History mostrará apenas os comandos que foram executados durante esta sessão ativa. Observe como os comandos são numerados; podemos recuperar esses comandos usando o alias `r` seguido do número para executar esse comando novamente. Por exemplo, se quiséssemos executar novamente o comando `arp -a`, poderíamos emitir `r 14` e o PowerShell o executará. Lembre-se de que se fecharmos a janela do shell ou na instância de um shell remoto por meio de comando e controle, assim que matarmos a sessão ou processo que estamos executando, nosso histórico de PowerShell desaparecerá. Com o PSReadLine, no entanto, esse não é o caso. PSReadLine armazena tudo em um arquivo chamado `$($host.Name)_history.txt` localizado em `$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine`

## Visualizando PSReadLine History

```powershell-session
PS C:\htb> get-content C:\Users\DLarusso\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

get-module
Get-ChildItem Env: | ft Key,Value
Get-ExecutionPolicy
clear
ssh administrator@10.172.16.110.55
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('https://download.sysinternals.com/files/PSTools.zip')"
Get-ExecutionPolicy

<SNIP>
```

Se executássemos o comando acima e fôssemos usuários frequentes da CLI, teríamos um extenso arquivo de histórico para classificar. A saída acima foi cortada para economizar tempo e espaço na tela. Um ótimo recurso do PSReadLine do ponto de vista do administrador é que ele tentará filtrar automaticamente todas as entradas que incluam as strings:

- password
- asplaintext
- token
- apikey
- secret

Esse comportamento é excelente para nós como administradores, pois ajudará a limpar todas as entradas do arquivo de histórico do PSReadLine que contenham chaves, credenciais ou outras informações confidenciais. O histórico de sessão integrado não faz isso.

## Clear Screen

Esta dica é de conveniência. Se nos incomoda ter uma tonelada de saída em nossa tela o tempo todo, podemos remover o texto de nossa janela de console usando o comando Clear-Host. Isso afetará apenas nossa exibição atual e não eliminará quaisquer variáveis ou outros objetos que possamos ter definido ou feito durante a sessão. Também podemos usar clear ou cls se preferirmos usar comandos curtos ou aliases.

## Hotkeys

A menos que estejamos trabalhando na CLI a partir de um ambiente GUI, nosso mouse não funcionará com frequência. Por exemplo, digamos que colocamos um shell em um host durante um pentest. Teremos acesso ao CMD ou PowerShell a partir deste shell, mas não poderemos utilizar a GUI. Portanto, precisamos nos sentir confortáveis usando apenas um teclado. As hotkeys podem nos permitir executar ações mais complexas que normalmente requerem um mouse apenas com nossas teclas. Abaixo está uma lista rápida de algumas das tecladas de atalho mais úteis:

- CTRL + R: Isso cria um histórico pesquisável. Podemos começar a digitar depois e ele nos mostrará resultados que correspondem aos comandos anteriores.
- CTRL + L: Limpa a tela
- CTRL + ALT + SHIFT + ?: Imprimirá toda a lista de atalhos de teclado que o PowerShell reconhecerá.
- Escape(`\`): Ao digitar na CLI, se desejar limpar a linha inteira, em vez de segurar a tecla backspace, basta pressionar escape, o que apagará a linha.
- F7: Traz uma TUI com um histórico interativo rolável de nossa sessão.

## Tab Completion

Uma das melhores funcionalidades do PowerShell deve ser a conclusão de comandos por tabulação. Podemos usar tab e SHIFT+tab para percorrer as opções que podem completar o comando que estamos digitando.

![text](https://academy.hackthebox.com/storage/modules/167/tab.gif)

### Aliases

Nossa última dica a mencionar são os Aliases. Um alias do PowerShell é outro nome para um cmdlet, comando ou arquivo executável. Podemos ver uma lista de aliases padrão usando o cmdlet Get-Alias.

```powershell-session
PS C:\Windows\system32> Get-Alias

CommandType     Name                                               Version    Source
                                                                              
-----------     ----                                               -------    -----
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Mi...
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
Alias           cls -> Clear-Host
Alias           clv -> Clear-Variable
Alias           cnsn -> Connect-PSSession
Alias           compare -> Compare-Object
Alias           copy -> Copy-Item
Alias           cp -> Copy-Item
Alias           cpi -> Copy-Item
Alias           cpp -> Copy-ItemProperty
Alias           curl -> Invoke-WebRequest
Alias           cvpa -> Convert-Path
Alias           dbp -> Disable-PSBreakpoint
Alias           del -> Remove-Item
Alias           diff -> Compare-Object
Alias           dir -> Get-ChildItem

<SNIP>
```

É uma prática excelente tornar os aliases mais curtos do que o nome do cmdlet, comando ou executável real. Até mesmo o cmdlet Get-Alias ​​tem um alias padrão de gal, como visto no clipe abaixo.

![Gal Alias](https://academy.hackthebox.com/storage/modules/167/GalAlias.gif)

Também podemos definir um alias para um cmdlet específico usando Set-Alias. Vamos praticar criando um alias para o cmdlet Get-Help.

```powershell-session
PS C:\Windows\system32> Set-Alias -Name gh -Value Get-Help
```

Ao usar Set-Alias, precisamos especificar o nome do alias (-Name gh) e o cmdlet correspondente (-Value Get-Help).

![Set Alias](https://academy.hackthebox.com/storage/modules/167/SetAlias.gif)

Abaixo, também incluímos uma lista de vários aliases que consideramos mais úteis. Alguns comandos também têm mais de um alias. Certifique-se de consultar a lista completa de outros aliases que podem ser úteis.

- pwd: gl também pode ser usado. Esse alias pode ser usado no lugar de Get-Location.
- ls: dir e gci também podem ser usados ​​no lugar de ls. Este é um alias para Get-ChildItem.
- cd: sl e chdir podem ser usados ​​no lugar de cd. Este é um alias para Set-Location.
- cat: type e gc também podem ser usados. Este é um alias para Get-Content.
- clear: Pode ser usado no lugar de Clear-Host.
- curl: Curl é um alias para Invoke-WebRequest, que pode ser usado para baixar arquivos. wget também pode ser usado.
- fl e ft: Esses aliases podem ser usados ​​para formatar a saída em saídas de lista e tabela.
- man: Pode ser usado no lugar de help.

# Tudo sobre cmdlets e módulos

## Cmdlets

Uma cmdlet é definido pela Microsoft como: um comando de recurso único que manipula objetos no PowerShell.

Os cmdlets seguem uma estrutura verbo-substantivo que geralmente facilita a compreensão do que um determinado cmdlet faz. Com Test-WSMan, podemos ver que o verbo é Test e o substantivo é Wsman. O verbo e o substantivo são separados por um traço (-). Após o verbo e o substantivo, usaríamos as opções disponíveis com um determinado cmdlet para executar a ação desejada. Os cmdlets são semelhantes às funções usadas no código do PowerShell ou em outras linguagens de programação, mas têm uma diferença significativa. Os cmdlets não são escritos no PowerShell. Eles são escritos em C# ou outra linguagem e depois compilados para uso.

Se quisermos ver as opções e funcionalidades disponíveis para nós com um cmdlet específico, podemos usar o cmdlet Get-Help, bem como o cmdlet Get-Member.

## Módulos PowerShell

Um módulo PowerShell é um código PowerShell estruturado que é facil de usar e compartilhar. Conforme mencionado nos documentos oficiais da Micrisoft, um módulo pode ser composto por:

- cmdlets
- arquivos de script
- funções
- assemblies
- recursos relatados (manifestos e arquivos de ajuda)

Nesta seção, vamos usar o projeto PowerView para examinar o que compõe um módulo e como interagir com eles. PowerView.ps1 faz parte de uma coleção de módulos do PowerShell organizado em um projeto chamado PowerSploit criado pela PowerShellMafia para fornecer penetration testers com muitas ferramentas valiosas para usar ao testar ambientes Windows Domain/Active Directory. Embora possamos notar que este projeto foi arquivado, muitas das ferramentas incluídas ainda são relevantes e úteis no teste de penetração.

![PowerSploit](https://academy.hackthebox.com/storage/modules/167/ImportModulePowerSploit.png)

## PowerSploit.psd1

Um arquivo de dados do PowerShell (.psd1) é um arquivo de manifesto do Módulo. Contido em um arquivo de manifesto, muitas vezes podemos encontrar:
- Referência ao módulo que será processado
- Números de versão para acompanhar as principais mudanças
- O GUID
- O autor do módulo
- Copyright
- Informações de compatibilidade do PowerShell
- Módulos e cmdlets inclusos
- Metadata
![PowerSploitpsd1](https://academy.hackthebox.com/storage/modules/167/PowerSploitpsd1.gif)


## PowerSploit.psm1

Um arquivo de módulo de script do PowerShell (.psm1) é simplesmente um script contendo o código do PowerSHell. 

### Conteúdo do PowerSploit.psm1

```powershell-session
Get-ChildItem $PSScriptRoot | ? { $_.PSIsContainer -and !('Tests','docs' -contains $_.Name) } | % { Import-Module $_.FullName -DisableNameChecking }
```

O cmdlet Get-ChildItem obtém os itens no diretório atual (representado pela variável automática $PSScriptRoot) e o cmdlet Where-Object (com o alias do caractere "?") filtra-os apenas para os itens que são pastas e não têm os nomes "Tests" ou "docs". Finalmente, o cmdlet ForEach-Object (alias como o caractere "%") execute o cmdlet Import-Module em cada um dos itens restantes, passando o parâmetro DisableNameChecking para evitar erros se o módulo contiver cmdlets ou funções com os mesmos nomes de cmdlets ou funções na sessão atual.

# Usando módulos PowerShell

Depois de decidir qual módulo do PowerShell queremos usar, teremos que determinar como e de onde o executaremos. Também devemos considerador se o módulo escolhido e os scripts já estão no host ou se precisamos colocá-los no host. Get-Module pode nos ajudar a determinar quais módulos já estão carregados.

```powershell-session
PS C:\htb> Get-Module 

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     0.0        chocolateyProfile                   {TabExpansion, Update-SessionEnvironment, refreshenv}
Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Checkpoint-Computer, Clear-Con...
Manifest   3.1.0.0    Microsoft.PowerShell.Utility        {Add-Member, Add-Type, Clear-Variable, Compare-Object...}
Script     0.7.3.1    posh-git                            {Add-PoshGitToProfile, Add-SshKey, Enable-GitColors, Expan...
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Get-PSReadLineOption, Remove-PS...
```

```powershell-session
PS C:\htb> Get-Module -ListAvailable 

 Directory: C:\Users\tru7h\Documents\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     1.1.0      PSSQLite                            {Invoke-SqliteBulkCopy, Invoke-SqliteQuery, New-SqliteConn...


    Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     1.0.1      Microsoft.PowerShell.Operation.V... {Get-OperationValidation, Invoke-OperationValidation}
Binary     1.0.0.1    PackageManagement                   {Find-Package, Get-Package, Get-PackageProvider, Get-Packa...
Script     3.4.0      Pester                              {Describe, Context, It, Should...}
Script     1.0.0.1    PowerShellGet                       {Install-Module, Find-Module, Save-Module, Update-Module...}
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Set-PSReadLineKeyHandler, Remov...
```

O modificador -ListAvailable nos mostrará todos os módulos que instalamos, mas não carregamos em nossa sessão.

Já transferimos o módulo ou scripts desejados para um host Windows de destino. Em seguida, precisaremos executá-los. Podemos começar com o uso do cmdlet Import-Module

```powershell-session
PS C:\Users\htb-student> Get-Help Import-Module

NAME
    Import-Module

SYNOPSIS
    Adds modules to the current session.


SYNTAX
    Import-Module [-Assembly] <System.Reflection.Assembly[]> [-Alias <System.String[]>] [-ArgumentList
    <System.Object[]>] [-AsCustomObject] [-Cmdlet <System.String[]>] [-DisableNameChecking] [-Force] [-Function
    <System.String[]>] [-Global] [-NoClobber] [-PassThru] [-Prefix <System.String>] [-Scope {Local | Global}]
    [-Variable <System.String[]>] [<CommonParameters>]

    Import-Module [-Name] <System.String[]> [-Alias <System.String[]>] [-ArgumentList <System.Object[]>]
    [-AsCustomObject] [-CimNamespace <System.String>] [-CimResourceUri <System.Uri>] -CimSession
    <Microsoft.Management.Infrastructure.CimSession> [-Cmdlet <System.String[]>] [-DisableNameChecking] [-Force]
    [-Function <System.String[]>] [-Global] [-MaximumVersion <System.String>] [-MinimumVersion <System.Version>]
    [-NoClobber] [-PassThru] [-Prefix <System.String>] [-RequiredVersion <System.Version>] [-Scope {Local | Global}]
    [-Variable <System.String[]>] [<CommonParameters>]

<SNIP>
```


Para entender a ideia de importar o módulo para nossa sessão atual do PowerShell, podemos tentar executar um cmdlet (Get-NetLocalgroup) que faz parte do PowerSploit. Receberemos uma mensagem de erro ao tentar fazer isso sem importar um módulo. Depois de importar com sucesso o módulo PowerSploit(ele foi colocado na área de trabalho do host de destino para nosso uso), muitos cmdlets estarão disponíveis para nós, incluindo Get-NetLocalgroup.

```powershell-session
PS C:\Users\htb-student\Desktop\PowerSploit> Import-Module .\PowerSploit.psd1
PS C:\Users\htb-student\Desktop\PowerSploit> Get-NetLocalgroup

ComputerName GroupName                           Comment
------------ ---------                           -------
WS01         Access Control Assistance Operators Members of this group can remotely query authorization attributes a...
WS01         Administrators                      Administrators have complete and unrestricted access to the compute...
WS01         Backup Operators                    Backup Operators can override security restrictions for the sole pu...
WS01         Cryptographic Operators             Members are authorized to perform cryptographic operations.
WS01         Distributed COM Users               Members are allowed to launch, activate and use Distributed COM obj...
WS01         Event Log Readers                   Members of this group can read event logs from local machine
WS01         Guests                              Guests have the same access as members of the Users group by defaul...
WS01         Hyper-V Administrators              Members of this group have complete and unrestricted access to all ...
WS01         IIS_IUSRS                           Built-in group used by Internet Information Services.
WS01         Network Configuration Operators     Members in this group can have some administrative privileges to ma...
WS01         Performance Log Users               Members of this group may schedule logging of performance counters,...
WS01         Performance Monitor Users           Members of this group can access performance counter data locally a...
WS01         Power Users                         Power Users are included for backwards compatibility and possess li...
WS01         Remote Desktop Users                Members in this group are granted the right to logon remotely
WS01         Remote Management Users             Members of this group can access WMI resources over management prot...
WS01         Replicator                          Supports file replication in a domain
WS01         System Managed Accounts Group       Members of this group are managed by the system.
WS01         Users                               Users are prevented from making accidental or intentional system-wi...
```

![Import-Module](https://academy.hackthebox.com/storage/modules/167/Import-Module.gif)

Observe como no início do clipe, Get-NetLocalgroup não foi reconhecido. Este evento aconteceu porque não está incluído no caminho do módulo padrão. Vemos onde está o caminho do módulo padrão listando a variável de ambiente PSModulePath.

```powershell-session
PS C:\Users\htb-student> $env:PSModulePath

C:\Users\htb-student\Documents\WindowsPowerShell\Modules;C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules
```

Quando o módulo PowerSploit.psd1 é importado, a função Get-NetLocalgroup é reconhecida. Isso acontece porque vários módulos são incluídos quando carregamos o PowerSploit.psd1. É possível adicionar permanentemente um módulo ou vários módulos adicionando os arquivos aos diretórios referenciados no PSModulePath. Essa ação faz sentido se estivéssemos usando um sistema operacional Windows como nosso host de ataque primário, mas em um engajamento, nosso tempo seria melhor apenas transferindo scripts específicos para o host de ataque e importando-os conforme necessário.

# Execution Policy

Um fator essencial a ser considerado ao tentar usar scripts e módulos do PowerShell é a política de execução do PowerShell. Conforme descrito na documentação oficial da Microsoft, uma política de execução não é um controle de segurança. Ele foi projetado para fornecer aos administradores uma ferramenta para definir parâmetros e salvaguardas para eles mesmos.

### Impacto da Execution Policiy

```powershell-session
PS C:\Users\htb-student\Desktop\PowerSploit> Import-Module .\PowerSploit.psd1

Import-Module : File C:\Users\Users\htb-student\PowerSploit.psm1
cannot be loaded because running scripts is disabled on this system. For more information, see
about_Execution_Policies at https:/go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:1
+ Import-Module .\PowerSploit.psd1
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : SecurityError: (:) [Import-Module], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.PowerShell.Commands.ImportModuleCommand
```

A política de execução do host faz com que não possamos executar nosso script. Podemos contornar isso, no entanto. Primeiro, vamos verificar nossas configurações de execution policy.

```powershell-session
PS C:\htb> Get-ExecutionPolicy 

Restricted  
```

Nossa configuração atual restringe o que o usuário pode fazer. Se quisermos alterar a configuração, podemos fazê-lo com o cmdlet Set-ExecutionPolicy.

```powershell-session
PS C:\htb> Set-ExecutionPolicy undefined 
```

Ao definir a política como undefined, estamos informando ao PowerShell que não desejamos limitar nossas interações. Agora devemos ser capazes de importar e executar nosso script.

```powershell-session
PS C:\htb> Import-Module .\PowerSploit.psd1

Import-Module .\PowerSploit.psd1
PS C:\Users\htb> get-module

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Check...
Manifest   3.0.0.0    Microsoft.PowerShell.Security       {ConvertFrom-SecureString, Conver...
Manifest   3.1.0.0    Microsoft.PowerShell.Utility        {Add-Member, Add-Type, Clear-Vari...
Script     3.0.0.0    PowerSploit                         {Add-Persistence, Add-ServiceDacl...
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Get-PS...
```

Observação: como sysadmin, esses tipos de alterações são comuns e sempre devem ser revertidos assim que terminar o trabalho. Como pentester, fazer uma alteração como essa e não revertê-la pode indicar a um defensor que o host foi comprometido. Certifique-se de verificar se limpamos após nossas ações. Outra maneira de ignorar a execution policy e não deixar uma alteração persistente é alterá-la no nível do processo usando -scope.

```powershell-session
PS C:\htb> Set-ExecutionPolicy -scope Process 
PS C:\htb> Get-ExecutionPolicy -list

Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine          Bypass  
```

Ao alterá-lo no nível do processo, nossa alteração será revertida assim que fecharmos a sessão do PowerShell. Lembre-se da execution policy ao trabalhar com scripts e novos módulos. 

Obviamente, queremos verificar primeiro os scripts que estamos tentando carregar para garantir que sejam seguros para uso. Como penetration testers, podemos nos deparar com momentos em que precisamos ser criativos sobre como ignorar a Execution Policy em um host. Este post https://www.netspi.com/blog/technical/network-penetration-testing/15-ways-to-bypass-the-powershell-execution-policy/ possui maneiras criativas que usamos em engajamentos do mundo real com grande sucesso.

# Chamando cmdlets e funções dentro de um módulo

Se quisermos ver quais aliases, cmdlets e funções um módulo importado trouxe para a sessão, podemos usar Get-Command -Module `<modulename>` para nos esclarecer. 

```powershell-session
PS C:\htb> Get-Command -Module PowerSploit

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Invoke-ProcessHunter                               3.0.0.0    PowerSploit
Alias           Invoke-ShareFinder                                 3.0.0.0    PowerSploit
Alias           Invoke-ThreadedFunction                            3.0.0.0    PowerSploit
Alias           Invoke-UserHunter                                  3.0.0.0    PowerSploit
Alias           Request-SPNTicket                                  3.0.0.0    PowerSploit
Alias           Set-ADObject                                       3.0.0.0    PowerSploit
Function        Add-Persistence                                    3.0.0.0    PowerSploit
Function        Add-ServiceDacl                                    3.0.0.0    PowerSploit
Function        Find-AVSignature                                   3.0.0.0    PowerSploit
Function        Find-InterestingFile                               3.0.0.0    PowerSploit
Function        Find-LocalAdminAccess                              3.0.0.0    PowerSploit
Function        Find-PathDLLHijack                                 3.0.0.0    PowerSploit
Function        Find-ProcessDLLHijack                              3.0.0.0    PowerSploit
Function        Get-ApplicationHost                                3.0.0.0    PowerSploit
Function        Get-GPPPassword                                    3.0.0.0    PowerSploit
```

Agora podemos ver o que foi carregado pelo PowerSploit. A partir deste ponto, podemos usar os scripts e funções conforme necessário. Esta é a parte fácil, escolha a função e deixe-a funcionar.

# Deep Dive: Encontrando e instalando módulos da galeria PowerShell e Github

Nos dias de hoje, compartilhar informações é extremamente fácil. Isso vale para soluções e novas criações também. Quando se trata de módulos do PowerShell, a PowerShell Galery(https://powershellgalery.com) é o melhor local para isso. É um repositório que contém scripts, módulos e muito mais do PowerShell criados pela Microsoft e outros usuários. Eles podem variar de algo tão simples como lidar com atributos do usuário até resolver problemas complexos de armazenamento em nuvem.

![text](https://academy.hackthebox.com/storage/modules/167/powershellg.png)

Conveniente para nós, já existe um módulo integrado ao PowerShell destinado a nos ajudar a interagir com a PowerShell Galery chamada PowerShellGet.

```powershell-session
PS C:\htb> Get-Command -Module PowerShellGet 

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Find-Command                                       1.0.0.1    PowerShellGet
Function        Find-DscResource                                   1.0.0.1    PowerShellGet
Function        Find-Module                                        1.0.0.1    PowerShellGet
Function        Find-RoleCapability                                1.0.0.1    PowerShellGet
Function        Find-Script                                        1.0.0.1    PowerShellGet
Function        Get-InstalledModule                                1.0.0.1    PowerShellGet
Function        Get-InstalledScript                                1.0.0.1    PowerShellGet
Function        Get-PSRepository                                   1.0.0.1    PowerShellGet
Function        Install-Module                                     1.0.0.1    PowerShellGet
Function        Install-Script                                     1.0.0.1    PowerShellGet
Function        New-ScriptFileInfo                                 1.0.0.1    PowerShellGet
Function        Publish-Module                                     1.0.0.1    PowerShellGet
Function        Publish-Script                                     1.0.0.1    PowerShellGet
Function        Register-PSRepository                              1.0.0.1    PowerShellGet
Function        Save-Module                                        1.0.0.1    PowerShellGet
Function        Save-Script                                        1.0.0.1    PowerShellGet
Function        Set-PSRepository                                   1.0.0.1    PowerShellGet
Function        Test-ScriptFileInfo                                1.0.0.1    PowerShellGet
Function        Uninstall-Module                                   1.0.0.1    PowerShellGet
Function        Uninstall-Script                                   1.0.0.1    PowerShellGet
Function        Unregister-PSRepository                            1.0.0.1    PowerShellGet
Function        Update-Module                                      1.0.0.1    PowerShellGet
Function        Update-ModuleManifest                              1.0.0.1    PowerShellGet
Function        Update-Script                                      1.0.0.1    PowerShellGet
Function        Update-ScriptFileInfo                              1.0.0.1    PowerShellGet
```

Este módulo tem muitas funções diferentes para nos ajudar a trabalhar e baixar módulos existentes da galeria e criar e carregar nossos próprios. Em nossa lista de funções, vamos experimentar Find-Module. Um módulo que será extremamente útil para administradores de sistema é o módulo AdminToolbox. É uma coleção de vários outros módulos com ferramentas destinadas ao gerenciamento do Active Directory, Microsoft Exchange, virtualização e muitas outras tarefas que um administrador precisaria em um determinado dia.

```powershell-session
PS C:\htb> Find-Module -Name AdminToolbox 

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
11.0.8     AdminToolbox                        PSGallery            Master module for a col...
```

Como em muitos outros cmdlets do PowerShell, também podemos pesquisar usando curingas. Depois de encontrar um módulo que desejamos utilizar, instalá-lo é tão fácil quanto Install-Module. Lembre-se de que são necessários direitos administrativos para instalar módulos dessa maneira.

![text](https://academy.hackthebox.com/storage/modules/167/admintoolbox.gif)

Na imagem acima, encadeamos Find-Module com Install-Module para executar simultaneamente as duas ações. Este exemplo aproveita a funcionalidade Pipeline do PowerShell. Abordaremos isso mais profundamente em outra seção, mas, por enquanto, isso nos permitiu encontrar e instalar o módulo com uma string de comando. Lembre-se de que as instâncias modernas do PowerShell importarão automaticamente um módulo instalado na primeira vez que executamos um cmdlet ou função a partir dele, portanto não há necessidade de importar o módulo após instalá-lo. Isso difere dos módulos personalizados ou módulos que trazemos para o host (do GitHub, por exemplo). Teremos que importá-lo manualmente sempre que quisermos usá-lo, a menos que modifiquemos nosso perfil do PowerShell. Podemos encontrar os locais para cada perfil específico do PowerShell em https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2.
Além de criar nossos próprios módulos e scripts ou importá-los da PowerShell Gallery, também podemos aproveitar o Github e todo o conteúdo incrível que a comunidade de TI criou externamente.

# Ferramentas para estar ciente

- AdminToolBox: AdminToolbox é uma coleção de módulos úteis que permitem aos administradores de sistema executar qualquer número de ações relacionadas a itens como Active Directory, Exchange, gerenciamento de rede, problemas de arquivo e armazenamento e muito mais. https://www.powershellgallery.com/packages/AdminToolbox/11.0.8
- ActiveDirectory: Este módulo é uma coleção de ferramentas de administração local e remota para todas as coisas do Active Directory. Podemos gerenciar usuários, grupos, permissões e muito mais com ele. https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps
- Empire / Situational Awareness: É uma coleção de módulos e scripts do PowerShell que podem nos fornecer consciência situacional em um host e no domínio do qual fazem parte. Este projeto está sendo mantido pela BC Security como parte de seu Empire Framework. https://github.com/BC-SECURITY/Empire/tree/main/empire/server/data/module_source/situational_awareness
- Inveigh: Inveigh é uma ferramenta criada para executar ataques de falsificação de rede e Man-in-the-middle. https://github.com/Kevin-Robertson/Inveigh
- BloodHound / SharpHound: Bloodhound/Sharphound nos permite mapear visualmente um ambiente Active Directory usando ferramentas de análise gráfica e coletores de dados escritos em C# e PowerShell. https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors