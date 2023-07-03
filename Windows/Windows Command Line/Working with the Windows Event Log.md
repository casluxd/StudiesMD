Do ponto de vista de um analista de SOC ou administrador de TI, monitorar, coletar e categorizar eventos que ocorrem em todas as máquinas da rede é uma fonte inestimável de informações para os defensores proativamente analisando e protegendo sua rede de atividades suspeitas. Por outro lado, os invasores podem ver isso como uma oportunidade de obter informações sobre o ambiente de destino, interromper o fluxo de informações e ocultar seus rastros. Como veremos em módulos posteriores, às vezes podemos encontrar informações suculentas, como credenciais ocultas em logs de eventos como um sistema de destino que comprometemos durante um teste de penetração. Outras vezes, enumerar logs de eventos pode nos ajudar a entender o nível de log no ambiente (são apenas os padrões em vigor ou a organização de destino configurou um log mais granular?). Nesta seção, discutiremos o seguinte:
- O que é o Log de Eventos do Windows?
- Quais informações ele registra e onde armazena essas informações?
- Interagindo com o log de eventos por meio do utilitário de linha de comando wevtutil
- Interagindo com o log de eventos usando cmdlets do PowerShell

# Interagindo com o log de eventos usando cmdlets do PowerShell

Uma compreensão clara do log de eventos é crucial para o sucesso na infosec. Para iniciar nossa jornada para obter uma compreensão completa do log de eventos do Windows, aqui estão alguns conceitos-chave que precisamos definir antes de mergulhar. Esses conceitos se tornarão a base sobre a qual todo o resto será construído. O primeiro que precisa ser explicado é uma definição de evento. Simplificando, um evento é qualquer ação ou ocorrência que pode ser identificada e classificada pelo hardware ou software de um sistema. Os eventos podem ser gerados ou acionados de várias maneiras diferentes, incluindo algumas das seguintes:

- Eventos gerados pelo usuário
  - Movimento de um mouse, digitação em um teclado, outros periféricos controlados pelo usuário, etc.
- Eventos gerados pelo aplicativo
 - Atualizações de aplicativos, travamentos, uso/consumo de memória, etc.
- Eventos gerados pelo sistema
 - Tempo de atividade do sistema, atualizações do sistema, carregamento/descarregamento do driver, login do usuário, etc.

Com tantos eventos ocorrendo em diferentes intervalos de tempo de várias fontes, como um sistema Windows rastreia e categoriza todos eles? É aqui que nosso segundo conceito-chave, conhecido como log de eventos, entra em ação.

Log de eventos conforme definido pela Microsoft: `...fornece uma maneira padrão e centralizada para os aplicativos (e o sistema operacional) registrarem eventos importantes de software e hardware.`

Esta definição resume a questão muito bem. No entanto, vamos tentar decompô-lo um pouco. Como discutimos anteriormente, há muitos eventos que estão sendo acionados ou gerados simultaneamente em um sistema. Cada evento terá sua própria fonte que fornece as informações e detalhes por trás do evento em seu próprio formato. Então, como ele lida com todas essas informações?

O Windows tenta resolver esse problema fornecendo uma abordagem padronizada para registrar, armazenar e gerenciar eventos e informações de eventos por meio de um serviço conhecido como Log de eventos do Windows. Como o próprio nome sugere, o Log de eventos gerencia eventos e logs de eventos, porém, além dessa funcionalidade, ele também abre uma API especial que permite que os aplicativos mantenham e gerenciem seus próprios logs separados. É essencial entender que o Log de Eventos é um serviço obrigatório do Windows iniciado na inicialização do sistema que é executado no contexto de outro executável e não no seu próprio.

## Categorias e tipos de registro de eventos

As quatro principais categorias de log incluem aplicativo, segurança, configuração e sistema. Outro tipo de categoria também existe chamada de eventos encaminhados.

- System Log: O log do sistema contém eventos relacionados ao sistema Windows e seus componentes. Um evento no nível do sistema pode ser um serviço falhando na inicialização.
- Security Log: Auto-explicativo; isso inclui eventos relacionados à segurança, como logins com falha e bem-sucedidos e criação/exclusão de arquivos. Eles podem ser usados ​​para detectar vários tipos de ataques que abordaremos em módulos posteriores.
- Application Log: Isso armazena eventos relacionados a qualquer software/aplicativo instalado no sistema. Por exemplo, se o Slack tiver problemas para iniciar, isso será registrado neste log.
- Setup Log: Esse log contém todos os eventos gerados quando o sistema operacional Windows é instalado. Em um ambiente de domínio, os eventos relacionados ao Active Directory serão registrados nesse log nos hosts do controlador de domínio.
- Forwarded Events: Logs que são encaminhados de outros hosts dentro da mesma rede.

## Tipos de Eventos

- Error: Indica que ocorreu um problema grave, como falha ao carregar um serviço durante a inicialização.
- Warning: Um log menos significativo, mas que pode indicar um possível problema no futuro. Um exemplo é pouco espaço em disco. Um evento de Aviso será registrado para observar que um problema pode ocorrer no futuro. Um evento de Aviso geralmente ocorre quando um aplicativo pode se recuperar do evento sem perder funcionalidade ou dados.
- Information: Registrado após a operação bem-sucedida de um aplicativo, driver ou serviço, como quando um driver de rede é carregado com êxito. Normalmente, nem todos os aplicativos de desktop registram um evento a cada vez que são iniciados, pois isso pode levar a uma quantidade considerável de "ruído" extra nos logs.
- Success Audit: Registrado quando uma tentativa de acesso de segurança auditada é bem-sucedida, como quando um usuário faz logon em um sistema.
- Failure Audit: Registrado quando uma tentativa de acesso de segurança auditada falha, como quando um usuário tenta fazer login, mas digita sua senha incorretamente. Muitos eventos de falha de auditoria podem indicar um ataque, como o Password Spraying.

# Levels de severidade de Eventos

- Verbose -> 5: Mensagens de progresso ou sucesso.
- Information -> 4: Um evento que ocorreu no sistema, mas não causou nenhum problema.
- Warning -> 3: Um problema potencial que um administrador de sistema deve investigar.
- Error -> 2: Um problema relacionado ao sistema ou serviço que não requer atenção imediata.
- Critical -> 1: Isso indica um problema significativo relacionado a um aplicativo ou sistema que requer atenção urgente de um administrador de sistema que, se não for resolvido, pode levar à instabilidade do sistema ou do aplicativo.

# Elementos de um log de eventos do Windows

O log de eventos do Windows fornece informações sobre eventos de hardware e software em um sistema Windows. Todos os logs de eventos são armazenados em um formato padrão e incluem os seguintes elementos:

- Log name: Conforme discutido acima, o nome do log de eventos onde os eventos serão gravados. Por padrão, os eventos são registrados para sistema, segurança e aplicativos.
- Event date/time: Data e hora em que ocorreu o evento
- Task Category: O tipo de log de eventos gravado
- Event ID: Um identificador exclusivo para administradores de sistema para identificar um evento registrado específico
- Source: De onde o log se originou, geralmente o nome de um programa ou aplicativo de software
- Level: Nível de gravidade do evento. Isso pode ser informação, erro, detalhado, aviso, crítico
- User: Nome de usuário de quem se conectou ao host quando o evento ocorreu
- Computer: Nome do computador onde o evento é registrado

Existem muitos IDs de evento que uma organização pode monitorar para detectar vários problemas. Em um ambiente do Active Directory, essa lista(https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor) inclui os principais eventos que devem ser monitorados para procurar sinais de comprometimento. Vale a pena examinar esse banco de dados pesquisável de IDs de eventos(https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/) para entender a profundidade do log possível em um sistema Windows.

# Windows Event Log Technical Details

O log de eventos do Windows é tratado pelos serviços EventLog. Em um sistema Windows, o nome de exibição do serviço é Log de eventos do Windows e é executado dentro do processo de host do serviço svchost.exe. Ele é definido para iniciar automaticamente na inicialização do sistema por padrão. É difícil interromper o serviço EventLog, pois ele possui vários serviços de dependência. Se for interrompido, provavelmente causará instabilidade significativa no sistema. Por padrão, os logs de eventos do Windows são armazenados em `C:\Windows\System32\winevt\logs` com a extensão de arquivo .evtx.

```powershell
PS C:\htb> s C:\Windows\System32\winevt\logs

    Directory: C:\Windows\System32\winevt\logs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/16/2022   2:19 PM        7409664 Application.evtx
-a----         6/14/2022   8:20 PM          69632 HardwareEvents.evtx
-a----         6/14/2022   8:20 PM          69632 Internet Explorer.evtx
-a----         6/14/2022   8:20 PM          69632 Key Management Service.evtx        
-a----         8/23/2022   7:01 PM          69632 Microsoft-Client-License-Flexible-P
                                                  latform%4Admin.evtx
-a----        11/16/2022   2:19 PM        1052672 Microsoft-Client-Licensing-Platform
                                                  %4Admin.evtx

```

Podemos interagir com o log de eventos do Windows usando o aplicativo Windows Event Viewer GUI por meio do utilitário de linha de comando wevtutil ou usando o cmdlet Get-WinEvent PowerShell. Wevtutil e Get-WinEvent podem ser usados ​​para consultar logs de eventos em sistemas Windows locais e remotos via cmd.exe ou PowerShell.

# Interagindo com o log de eventos do Windows - wevtutil

O utilitário de linha de comando wevtutil pode ser usado para recuperar informações sobre logs de eventos. Também pode ser usado para exportar, arquivar e limpar logs, entre outros comandos.

```cmd
C:\htb> wevtutil /?

Windows Events Command Line Utility.

Enables you to retrieve information about event logs and publishers, install
and uninstall event manifests, run queries, and export, archive, and clear logs.

Usage:

You can use either the short (for example, ep /uni) or long (for example,
enum-publishers /unicode) version of the command and option names. Commands,
options and option values are not case-sensitive.

Variables are noted in all upper-case.

wevtutil COMMAND [ARGUMENT [ARGUMENT] ...] [/OPTION:VALUE [/OPTION:VALUE] ...]

Commands:

el | enum-logs          List log names.
gl | get-log            Get log configuration information.
sl | set-log            Modify configuration of a log.
ep | enum-publishers    List event publishers.
gp | get-publisher      Get publisher configuration information.
im | install-manifest   Install event publishers and logs from manifest.
um | uninstall-manifest Uninstall event publishers and logs from manifest.
qe | query-events       Query events from a log or log file.
gli | get-log-info      Get log status information.
epl | export-log        Export a log.
al | archive-log        Archive an exported log.
cl | clear-log          Clear a log.

<SNIP>
```

Podemos usar o parâmetro el para enumerar os nomes de todos os logs presentes em um sistema Windows.

```cmd
C:\htb> wevtutil el

AMSI/Debug
AirSpaceChannel
Analytic
Application
DirectShowFilterGraph
DirectShowPluginControl
Els_Hyphenation/Analytic
EndpointMapper
FirstUXPerf-Analytic
ForwardedEvents
General Logging
HardwareEvents

<SNIP>
```

Com o parâmetro gl, podemos exibir as informações de configuração de um log específico, principalmente se o log está habilitado ou não, o tamanho máximo, as permissões e onde o log está armazenado no sistema.

```cmd-session
C:\htb> wevtutil gl "Windows PowerShell"

name: Windows PowerShell
enabled: true
type: Admin
owningPublisher:
isolation: Application
channelAccess: O:BAG:SYD:(A;;0x2;;;S-1-15-2-1)(A;;0x2;;;S-1-15-3-1024-3153509613-960666767-3724611135-2725662640-12138253-543910227-1950414635-4190290187)(A;;0xf0007;;;SY)(A;;0x7;;;BA)(A;;0x7;;;SO)(A;;0x3;;;IU)(A;;0x3;;;SU)(A;;0x3;;;S-1-5-3)(A;;0x3;;;S-1-5-33)(A;;0x1;;;S-1-5-32-573)
logging:
  logFileName: %SystemRoot%\System32\Winevt\Logs\Windows PowerShell.evtx
  retention: false
  autoBackup: false
  maxSize: 15728640
publishing:
  fileMax: 1
```

O parâmetro gli nos dará informações de status específicas sobre o log ou arquivo de log, como hora de criação, último acesso e tempo de gravação, tamanho do arquivo, número de registros de log e muito mais.

```cmd-session
C:\htb> wevtutil gli "Windows PowerShell"

creationTime: 2020-10-06T16:57:38.617Z
lastAccessTime: 2022-10-26T19:05:21.533Z
lastWriteTime: 2022-10-26T19:05:21.533Z
fileSize: 11603968
attributes: 32
numberOfLogRecords: 9496
oldestRecordNumber: 1
```

Há muitas maneiras de consultar eventos. Por exemplo, digamos que queremos exibir os últimos 5 eventos mais recentes do registro de segurança em formato de texto. O acesso de administrador local é necessário para este comando.

```cmd-session
C:\htb> wevtutil qe Security /c:5 /rd:true /f:text

Event[0]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.2270000Z
  Event ID: 4799
  Task: Security Group Management
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description: 
A security-enabled local group membership was enumerated.

Subject:
        Security ID:            S-1-5-18
        Account Name:           ICL-WIN11$
        Account Domain:         GREENHORN
        Logon ID:               0x3E7

Group:
        Security ID:            S-1-5-32-544
        Group Name:             Administrators
        Group Domain:           Builtin

Process Information:
        Process ID:             0x56c
        Process Name:           C:\Windows\System32\svchost.exe

Event[1]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.0160000Z
  Event ID: 4672
  Task: Special Logon
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description:
Special privileges assigned to new logon.

Subject:
        Security ID:            S-1-5-21-4125911421-2584895310-3954972028-1001        
        Account Name:           htb-student
        Account Domain:         ICL-WIN11
        Logon ID:               0x8F211

Privileges:             SeSecurityPrivilege
                        SeTakeOwnershipPrivilege
                        SeLoadDriverPrivilege
                        SeBackupPrivilege
                        SeRestorePrivilege
                        SeDebugPrivilege
                        SeSystemEnvironmentPrivilege
                        SeImpersonatePrivilege
                        SeDelegateSessionUserImpersonatePrivilege

Event[2]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.0160000Z
  Event ID: 4624
  Task: Logon
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description:
An account was successfully logged on.

Subject:
        Security ID:            S-1-5-18
        Account Name:           ICL-WIN11$
        Account Domain:         GREENHORN
        Logon ID:               0x3E7


<SNIP>
```

Também podemos exportar eventos de um log específico para processamento offline. O administrador local também é necessário para realizar esta exportação.

```cmd-session
C:\htb> wevtutil epl System C:\system_export.evtx
```

# Interagindo com o log de eventos do Windows - PowerShell

Da mesma forma, podemos interagir com os logs de eventos do Windows usando o cmdlet Get-WinEvent do PowerShell. Assim como nos exemplos do wevtutil, alguns comandos requerem acesso de nível de administrador local.

Para começar, podemos listar todos os logs do computador, dando-nos o número de registros em cada log.

```powershell-session
PS C:\htb> Get-WinEvent -ListLog *

LogMode   MaximumSizeInBytes RecordCount LogName
-------   ------------------ ----------- -------
Circular            15728640         657 Windows PowerShell
Circular            20971520       10713 System
Circular            20971520       26060 Security
Circular            20971520           0 Key Management Service
Circular             1052672           0 Internet Explorer
Circular            20971520           0 HardwareEvents
Circular            20971520        6202 Application
Circular             1052672             Windows Networking Vpn Plugin Platform/Op...
Circular             1052672             Windows Networking Vpn Plugin Platform/Op... 
Circular             1052672           0 SMSApi
Circular             1052672          61 Setup
Circular            15728640          24 PowerShellCore/Operational
Circular             1052672          99 OpenSSH/Operational
Circular             1052672          46 OpenSSH/Admin

<SNIP>
```

Também podemos listar informações sobre um log específico. Aqui podemos ver o tamanho do log de segurança.

```powershell-session
PS C:\htb> Get-WinEvent -ListLog Security

LogMode   MaximumSizeInBytes RecordCount LogName
-------   ------------------ ----------- -------
Circular            20971520       26060 Security
```

Podemos consultar o último número X de eventos, procurando especificamente os últimos cinco eventos usando o parâmetro -MaxEvents. Aqui listaremos os últimos cinco eventos registrados no log de segurança. Por padrão, os logs mais recentes são listados primeiro. Se quisermos obter os logs mais antigos primeiro, podemos inverter a ordem para listar os mais antigos primeiro usando o parâmetro -Oldest.

```powershell-session
PS C:\htb> Get-WinEvent -LogName 'Security' -MaxEvents 5 | Select-Object -ExpandProperty Message

An account was logged off.

Subject:
        Security ID:            S-1-5-111-3847866527-469524349-687026318-516638107-1125189541-6052
        Account Name:           sshd_6052
        Account Domain:         VIRTUAL USERS
        Logon ID:               0x8E787

Logon Type:                     5

This event is generated when a logon session is destroyed. It may be positively correlated with a logon event using the Logon ID value. Logon IDs are only unique between reboots on the same computer.
Special privileges assigned to new logon.

Subject:
        Security ID:            S-1-5-18
        Account Name:           SYSTEM
        Account Domain:         NT AUTHORITY
        Logon ID:               0x3E7

Privileges:             SeAssignPrimaryTokenPrivilege
                        SeTcbPrivilege
                        SeSecurityPrivilege
                        SeTakeOwnershipPrivilege
                        SeLoadDriverPrivilege
                        SeBackupPrivilege
                        SeRestorePrivilege
                        SeDebugPrivilege
                        SeAuditPrivilege
                        SeSystemEnvironmentPrivilege
                        SeImpersonatePrivilege
                        SeDelegateSessionUserImpersonatePrivilege
An account was successfully logged on.

<SNIP>
```

Podemos nos aprofundar e examinar IDs de eventos específicos em logs específicos. Digamos que queremos apenas examinar as falhas de logon no log de segurança, verificando a ID de evento 4625: Falha ao fazer logon em uma conta. A partir daqui, poderíamos usar o parâmetro -ExpandProperty para aprofundar eventos específicos, listar logs do mais antigo para o mais recente, etc.

```powershell-session
PS C:\htb> Get-WinEvent -FilterHashTable @{LogName='Security';ID='4625 '}

   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
11/16/2022 2:53:16 PM          4625 Information      An account failed to log on....  
11/16/2022 2:53:16 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:53:12 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:36 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:29 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:21 PM          4625 Information      An account failed to log on....

<SNIP>
```

Também podemos olhar apenas para eventos com um nível de informação específico. Vamos verificar todos os logs do sistema apenas para eventos críticos com nível de informação 1. Aqui vemos apenas uma entrada de log em que o sistema não reinicializou corretamente.

```powershell-session
PS C:\htb> Get-WinEvent -FilterHashTable @{LogName='System';Level='1'} | select-object -ExpandProperty Message

The system has rebooted without cleanly shutting down first. This error could be caused if the system stopped responding, crashed, or lost power unexpectedly.
```

Pratique mais com wevtutil e Get-WinEvent para se sentir mais confortável com a pesquisa de logs. A Microsoft fornece alguns exemplos para Get-WinEvent(https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.3), enquanto este site mostra exemplos para wevtutil(https://www.thewindowsclub.com/what-is-wevtutil-and-how-do-you-use-it), e este site tem alguns exemplos adicionais para usar Get-WinEvent(https://4sysops.com/archives/search-the-event-log-with-the-get-winevent-powershell-cmdlet/).

Esta seção apresentou o log de eventos do Windows, um tópico vasto que abordaremos com mais profundidade em módulos posteriores. Experimente os vários exemplos nesta seção e fique à vontade usando ambas as ferramentas para consultar informações específicas. Em módulos posteriores, veremos como às vezes podemos encontrar dados confidenciais, como senhas, em logs de eventos. O log no Windows é muito poderoso quando configurado corretamente. Cada sistema gera uma grande quantidade de logs e, como vimos com todos os Event IDs possíveis, podemos obter bastante granularidade com o que exatamente escolhemos registrar. Todos esses dados por conta própria seriam muito difíceis de consultar constantemente e são mais eficazes quando encaminhados para uma ferramenta SIEM que pode ser usada para configurar alertas em IDs de eventos específicos que pode ser indicativo de um ataque, como Kerberoasting, Password Spraying ou outros ataques menos comuns. Como testadores de penetração, devemos estar familiarizados com o log de eventos do Windows, como podemos usá-lo para obter informações sobre o ambiente e, às vezes, até mesmo extrair dados confidenciais. Para blue teamers, o conhecimento profundo do Windows Event Log e como aproveitá-lo para alertas e monitoramento eficazes é fundamental.