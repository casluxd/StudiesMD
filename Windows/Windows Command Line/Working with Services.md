`Cenário: O Sr. Tanaka enviou uma mensagem ao Helpdesk informando que notou uma janela pop-up no início do dia e pensou que eram apenas atualizações do Windows em execução, pois muitas informações passavam pela janela. No entanto, agora ele relata que alertas informando que o Defender está desligado também apareceram e seu host está lento. Precisamos investigar isso, determinar quais serviços relacionados ao Defender estão desativados e ativá-los novamente, se pudermos. Mais tarde, examinaremos os logs de eventos e veremos o que aconteceu.`

A administração de serviços é crucial para gerenciar hosts e garantir que nossa postura de segurança permaneça inalterada. Esta seção abordará como consultar, iniciar, parar e editar serviços e suas permissões conforme necessário. Também discutiremos maneiras de interagir com eles local e remotamente. É hora de mergulhar e adquirir nosso próximo CLI Kung-Fu Belt.

# O que são serviços e como interagimos com eles usando o Powershell?

Os serviços no sistema operacional Windows, em sua essência, são instâncias singulares de um componente executado em segundo plano que gerencia e mantém processos e outros componentes necessários para aplicativos usados ​​no host. Os serviços geralmente não exigem interação do usuário e não possuem uma interface tangível para interagir. Eles também existem como uma instância singular do serviço no host, enquanto um serviço pode manter várias instâncias de um processo. Um processo pode ser considerado um contêiner temporário para um usuário ou aplicativo executar tarefas. O Windows tem três categorias de serviços: Serviços Locais, Serviços de Rede e Serviços do Sistema. Muitos serviços diferentes (incluindo os principais componentes do sistema operacional Windows) lidam com várias instâncias de processos simultaneamente. O PowerShell nos fornece o módulo Microsoft.PowerShell.Management, que contém vários cmdlets para interagir com os Serviços. Assim como tudo no PowerShell, se você não tiver certeza de por onde começar ou de qual cmdlet precisa, aproveite a ajuda integrada para começar.

## Obtendo ajuda (services)

```powershell-session
PS C:\htb> Get-Help *-Service  

Name                              Category  Module                    Synopsis
----                              --------  ------                    --------
Get-Service                       Cmdlet    Microsoft.PowerShell.Man… …
New-Service                       Cmdlet    Microsoft.PowerShell.Man… …
Remove-Service                    Cmdlet    Microsoft.PowerShell.Man… …
Restart-Service                   Cmdlet    Microsoft.PowerShell.Man… …
Resume-Service                    Cmdlet    Microsoft.PowerShell.Man… …
Set-Service                       Cmdlet    Microsoft.PowerShell.Man… …
Start-Service                     Cmdlet    Microsoft.PowerShell.Man… …
Stop-Service                      Cmdlet    Microsoft.PowerShell.Man… …
Suspend-Service                   Cmdlet    Microsoft.PowerShell.Man… …
```

Agora, vamos começar nossa triagem do anfitrião do Sr. Tanaka e ver o que está acontecendo.

`Observação: lembre-se de que, para gerenciar ou modificar serviços fora das consultas em execução, precisaremos ter as permissões corretas para isso. Isso significa que nosso usuário deve, idealmente, ser um administrador local no host ou receber as permissões dos grupos de domínio dos quais é membro. Abrir o PowerShell em um contexto administrativo também funcionaria.`

## Investigando serviços rodando

Primeiro, precisamos obter uma lista rápida de serviços de nosso host de destino. Os serviços podem ter um status definido como Em execução, Parado ou Pausado e podem ser configurados para iniciar manualmente (interação do usuário), automaticamente (na inicialização do sistema) ou em um atraso após a inicialização do sistema. Os usuários com privilégios administrativos geralmente podem criar, modificar e excluir serviços.

```powershell-session
PS C:\htb> Get-Service | ft DisplayName,Status 

DisplayName                                                                         Status
-----------                                                                         ------

Adobe Acrobat Update Service                                                       Running
OpenVPN Agent agent_ovpnconnect                                                    Running
Adobe Genuine Monitor Service                                                      Running
Adobe Genuine Software Integrity Service                                           Running
Application Layer Gateway Service                                                  Stopped
Application Identity                                                               Stopped
Application Information                                                            Running
Application Management                                                             Stopped
App Readiness                                                                      Stopped
Microsoft App-V Client                                                             Stopped
AppX Deployment Service (AppXSVC)                                                  Running
AssignedAccessManager Service                                                      Stopped
Windows Audio Endpoint Builder                                                     Running
Windows Audio                                                                      Running
ActiveX Installer (AxInstSV)                                                       Stopped
GameDVR and Broadcast User Service_172433                                          Stopped
BitLocker Drive Encryption Service                                                 Running
Base Filtering Engine                                                              Running
<SNIP> 

PS C:\htb> Get-Service | measure  

Count             : 321
```

Para tornar a execução um pouco mais clara, canalizamos nossa listagem de serviços para format-table e escolhemos as propriedades DisplayName e Status para exibir em nosso console. No segundo comando emitido, medimos a quantidade de serviços que aparecem na listagem apenas para ter uma noção de quantos estamos trabalhando. 321 serviços são muito para percorrer e trabalhar ao mesmo tempo, então precisamos reduzi-los um pouco mais. A pedido do Sr. Tanaka, ele mencionou um possível problema com o Windows Defender, então vamos filtrar quaisquer serviços não relacionados a isso.

```powershell-session
PS C:\htb> Get-Service | where DisplayName -like '*Defender*' | ft DisplayName,ServiceName,Status

DisplayName                                             ServiceName  Status
-----------                                             -----------  ------
Windows Defender Firewall                               mpssvc      Running
Windows Defender Advanced Threat Protection Service     Sense       Stopped
Microsoft Defender Antivirus Network Inspection Service WdNisSvc    Running
Microsoft Defender Antivirus Service                    WinDefend   Stopped
```

Agora podemos ver apenas os serviços relacionados ao Defender e podemos ver que, por algum motivo, o Microsoft Defender Antivirus Service (WinDefend) está realmente desativado. Por enquanto, para garantir a proteção do host do Sr. Tanaka, vamos tentar ligá-lo novamente usando o cmdlet Start-Service.

```powershell-session
PS C:\htb> Start-Service WinDefend
```

Ao executarmos o cmdlet Start-Service, contanto que não obtivéssemos uma mensagem de erro como "ParserError: este script contém conteúdo malicioso e foi bloqueado por seu software antivírus" ou outros, o comando foi executado com sucesso. Podemos verificar novamente consultando o serviço.

```powershell-session
PS C:\htb>  get-service WinDefend

Status   Name               DisplayName
------   ----               -----------
Running  WinDefend          Microsoft Defender Antivirus Service
```

Observe como utilizamos o nome do serviço para iniciar e consultar o serviço em vez de qualquer coisa no DisplayName. Por enquanto, o Defender está de volta e funcionando, então a primeira missão está cumprida. Enquanto estivermos aqui, vamos olhar em volta e ver o que mais está acontecendo. À medida que examinamos os serviços um pouco mais para ver o que há lá, notamos um serviço com um DisplayName estranho.

```powershell-session
PS C:\htb> get-service 

Stopped  SmsRouter          Microsoft Windows SMS Router Service.
Stopped  SNMPTrap           SNMP Trap
Stopped  spectrum           Windows Perception Service
Running  Spooler            Totally still used for Print Spooli...
Stopped  sppsvc             Software Protection
Running  SSDPSRV            SSDP Discovery
```


Não conseguimos encontrar nenhuma informação sobre este serviço em particular, e a alteração do DisplayName é estranha. Portanto, para garantir, interromperemos o serviço por enquanto e deixaremos que um dos membros da nossa equipe de segurança o investigue.

```powershell-session
PS C:\htb> Stop-Service Spooler 

PS C:\htb> Get-Service Spooler 

Status   Name               DisplayName
------   ----               -----------
Stopped  spooler            Totally still used for Print Spooli...
```

Agora podemos ver que usando o Stop-Service, paramos o status operacional do serviço Spooler. Agora que paramos o serviço, vamos definir o tipo de inicialização do serviço agora de Automático para Desativado até que uma investigação mais aprofundada possa ser realizada.

```powershell-session
PS C:\htb> get-service spooler | Select-Object -Property Name, StartType, Status, DisplayName

Name    StartType  Status DisplayName
----    ---------  ------ -----------
spooler Automatic Stopped Totally still used for Print Spooling...


PS C:\htb> Set-Service -Name Spooler -StartType Disabled

PS C:\htb> Get-Service -Name Spooler | Select-Object -Property StartType 

StartType
---------
 Disabled
```

Ok, agora nosso serviço Spooler foi interrompido e sua inicialização alterada para Desativada por enquanto. Modificar um serviço em execução é razoavelmente simples. Certifique-se de que, se tentar fazer qualquer modificação, você é um Administrador do host ou do domínio. Remover serviços no PowerShell é difícil agora. Remover serviços no PowerShell é difícil agora. O cmdlet Remove-Service só funciona se você estiver usando o PowerShell versão 7. Por padrão, nossos hosts abrirão e executarão o PowerShell versão 5.1. Por enquanto, se você deseja remover um serviço e suas entradas, use a ferramenta sc.exe.

# Como interagimos com serviços remotos usando o PowerShell?

Agora que sabemos como trabalhar com serviços, vejamos como podemos interagir com hosts remotos. Como o host do Sr. Tanaka está em um domínio, podemos facilmente consultar e verificar os serviços em execução em outros hosts. O parâmetro -ComputerName nos permite especificar que queremos consultar um host remoto.

```powershell-session
PS C:\htb> get-service -ComputerName ACADEMY-ICL-DC

Status   Name               DisplayName
------   ----               -----------
Running  ADWS               Active Directory Web Services
Stopped  AppIDSvc           Application Identity
Stopped  AppMgmt            Application Management
Stopped  AppReadiness       App Readiness
Stopped  AppXSvc            AppX Deployment Service (AppXSVC)
Running  BFE                Base Filtering Engine
Stopped  BITS               Background Intelligent Transfer Ser...
<SNIP>  
```

```powershell-session
PS C:\htb> Get-Service -ComputerName ACADEMY-ICL-DC | Where-Object {$_.Status -eq "Running"}

Status   Name               DisplayName
------   ----               -----------
Running  ADWS               Active Directory Web Services
Running  BFE                Base Filtering Engine
Running  COMSysApp          COM+ System Application
Running  CoreMessagingRe... CoreMessaging
Running  CryptSvc           Cryptographic Services
Running  DcomLaunch         DCOM Server Process Launcher
Running  Dfs                DFS Namespace
Running  DFSR               DFS Replication
```

Uma observação interessante aqui é que, como o PowerShell lida com tudo como um objeto, até mesmo a saída de um comando remoto, podemos usar o pipeline do PowerShell para dissecar as propriedades de um objeto com Where-Object. Nossos resultados retornaram apenas os serviços com status quando executado de Running. Podemos usar essas combinações para inúmeras coisas. Um ótimo exemplo seria consultar nossos hosts para uma propriedade específica, como se o status fosse Em execução, se um DisplayName fosse definido para algo específico, etc. Em relação às interações remotas, também podemos usar o cmdlet Invoke-Command. Vamos tentar consultar vários hosts e ver o status do serviço UserManager.

```powershell-session
PS C:\htb> invoke-command -ComputerName ACADEMY-ICL-DC,LOCALHOST -ScriptBlock {Get-Service -Name 'windefend'}

Status   Name               DisplayName                            PSComputerName
------   ----               -----------                            --------------
Running  windefend          Microsoft Defender Antivirus Service   LOCALHOST
Running  windefend          Windows Defender Antivirus Service     ACADEMY-ICL-DC
```

- Invoke-Command: Estamos informando ao PowerShell que queremos executar um comando em um computador local ou remoto.
- ComputerName: Fornecemos uma lista definida por vírgulas de nomes de computadores para consulta.
- ScriptBlock {command}: Esta parte é o comando incluso que queremos executar no computador. Para que ele seja executado, precisamos que ele esteja entre {}.

`Cenário: Anteriormente nesta seção, vimos um serviço (Spooler) que tinha um DisplayName que foi modificado. Isso pode nos indicar um problema em nosso ambiente. Usando o parâmetro -ComputerName ou o cmdlet Invoke-Command para consultar todos os hosts em nosso ambiente e verificar as propriedades DisplayName para ver se algum outro host foi afetado. Como administrador, ter acesso a esse tipo de poder é inestimável e muitas vezes pode ajudar a reduzir o tempo que uma ameaça permanece no host e ajudar a antecipar o problema e trabalhar para expulsá-la.`