
Monitorar e controlar serviços em um host é essencial para ser um administrador. Como um invasor, a capacidade de interrogar serviços, encontrar pontos sólidos para se conectar e ativar ou desativar serviços é uma habilidade muito procurada ao aterrissar em um host. Nesta seção, veremos o uso de **sc**, o utilitário controlador de serviço de linha de comando do Windows, mas vendo da perspectiva do invasor.

Acabamos de pousar em um host de vítimas e precisamos:

- Determinar quais serviços estão rodando.
- Tentar desativar o antivírus.
- Modificar serviços existentes no sistema.

## Service Controller

**SC** é um utilitário de executável do Windows que permite a pesquisa, modificação e gerenciamento de serviços do host localmente e através da rede. Na maior parte desta seção, utilizaremos o **SC** como o padrão para gerenciar serviços. Nós temos outros serviços, como Windows Management Instrumentation (WMIC) e Tasklist que também podem pesquisar e gerenciar serviços em hosts locais e remotos.

```cmd-session
C:\htb> sc  

DESCRIPTION:
        SC is a command line program used for communicating with the
        Service Control Manager and services.
USAGE:
        sc <server> [command] [service name] <option1> <option2>...


        The option <server> has the form "\\ServerName"
        Further help on commands can be obtained by typing: "sc [command]"
        Commands:
          query-----------Queries the status for a service, or
                          enumerates the status for types of services.
          queryex---------Queries the extended status for a service, or
                          enumerates the status for types of services.
          start-----------Starts a service.
          pause-----------Sends a PAUSE control request to a service.

<SNIP>  

SYNTAX EXAMPLES
sc query                - Enumerates status for active services & drivers
sc query eventlog       - Displays status for the eventlog service
sc queryex eventlog     - Displays extended status for the eventlog service
sc query type= driver   - Enumerates only active drivers
sc query type= service  - Enumerates only Win32 services
sc query state= all     - Enumerates all services & drivers
sc query bufsize= 50    - Enumerates with a 50 byte buffer
sc query ri= 14         - Enumerates with resume index = 14
sc queryex group= ""    - Enumerates active services not in a group
sc query type= interact - Enumerates all interactive services
sc query type= driver group= NDIS     - Enumerates all NDIS drivers
```

Como pode ser visto, SC sem parâmetros funciona como a maioria dos comandos e nos fornece o contexto de ajuda e alguns ótimos exemplos para começar a imprimir na saída do terminal.

### Pesquisando serviços

Ser capaz de consultar serviços para obter informações como o estado do processo, id do processo (pid) e tipo de serviço é uma ferramenta valiosa para ter em nosso arsenal como invasor. Podemos usar isso para verificar se determinados serviços estão em execução ou verificar todos os serviços e drivers existentes no sistema para obter mais informações. Antes de examinarmos especificamente a verificação do serviço Windows Defender, vamos ver quais serviços estão sendo executados ativamente no sistema. Podemos fazer isso emitindo o seguinte comando:

```cmd-session
C:\htb> sc query type= service

SERVICE_NAME: Appinfo
DISPLAY_NAME: Application Information
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

SERVICE_NAME: AppXSvc
DISPLAY_NAME: AppX Deployment Service (AppXSVC)
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

SERVICE_NAME: AudioEndpointBuilder
DISPLAY_NAME: Windows Audio Endpoint Builder
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

SERVICE_NAME: Audiosrv
DISPLAY_NAME: Windows Audio
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

SERVICE_NAME: BFE
DISPLAY_NAME: Base Filtering Engine
        TYPE               : 20  WIN32_SHARE_PROCESS
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

SERVICE_NAME: BITS
DISPLAY_NAME: Background Intelligent Transfer Service
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_PRESHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

<SNIP>
```

Podemos ver uma lista completa dos serviços em execução em ativa neste sistema. Usando essas informações, podemos avaliar minuciosamente o que está sendo executado no sistema e procurar qualquer coisa que desejemos desativar ou, em alguns casos serviços que podemos tentar assumir nossos próprios propósitos, seja para escalonamento ou persistência.

Retornando ao nosso cenário, recentemente chegamos a um host e precisamos consultar o host e determinar se o Windows Defender está ativo.

```cmd-session
C:\htb> sc query windefend    

SERVICE_NAME: windefend
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
                                (NOT_STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

Agora, o que vemos acima? Podemos dizer que o Windows Defender está em execução e, com nosso conjunto de permissões atual (aquele em que utilizamos para a consulta), não tem permissão para interromper ou pausar o serviço (provavelmente porque nosso usuário é um usuário padrão e não um administrador). Podemos testar isso tentando interromper o serviço.

# Parando e iniciando serviços

## Parando um serviço elevado

```cmd-session
C:\htb> sc stop windefend

Access is denied.  
```

Como podemos ver na saída acima, nosso usuário atual não tem as permissões adequadas para interromper ou pausar esse serviço específico. Para executar esta ação, provavelmente precisaríamos das permissões de uma conta de administrador e, em alguns casos, determinados serviços só podem ser gerenciados pelo próprio sistema. Idealmente, tentar interromper um serviço elevado como esse não é a melhor maneira de testar permissões, pois isso provavelmente levará a sermos pegos devidos ao tráfego que será gerado ao executar um comando como esse.

Agora que tentamos e falhamos em interromper o serviço **windefend** sob um usuário com permissões padrão, vamos mostrar o que aconteceria se realmente tivéssemos acesso a uma conta contendo privilégios de administrador local para a máquina. Podemos tentar interromper os serviços por meio do comando `sc stop <nome do serviço>`. Vamos tentar o exemplo anterior mais uma vez com permissões elevados como o usuário `Administrador`.

```cmd-session
C:\WINDOWS\system32> sc stop windefend

Access is denied.
```

Parece que ainda não temos o acesso adequado para interromper este serviço em particular. Esta é uma boa lição para aprendermos, pois certos processos são protegidos sob requisitos de acesso mais rígidos do que as contas de administrador local. Nesse cenário, a única coisa que pode interromper e iniciar o serviço Defender é a conta da máquina `SYSTEM`.

Como um invasor, aprender as restrições por trás do que certas contas têm acesso ou falta de acesso é muito importante porque tentar cegamente irá preencher os logs com erros e disparar quaisquer alertas mostrando que um usuário com privilégios insuficientes está tentando acessar um processo protegido no sistema. Isso chamará a atenção do blue team para nossas atividades e iniciará uma tentativa de triagem para nos expulsar do sistema e nos bloquear permanentemente.

## Parando serviços

```cmd-session
C:\WINDOWS\system32> sc stop Spooler

SERVICE_NAME: Spooler
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 3  STOP_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x3
        WAIT_HINT          : 0x4e20

C:\WINDOWS\system32> sc query Spooler

SERVICE_NAME: Spooler
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

## Iniciando serviços

```cmd-session
C:\WINDOWS\system32> sc start Spooler

SERVICE_NAME: Spooler
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 34908
        FLAGS              :

C:\WINDOWS\system32> sc query Spooler

SERVICE_NAME: Spooler
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

## Modificando serviços

Além de poder iniciar e interromper serviços, também podemos tentar modificar os serviços existentes. É aqui que os invasores podem prosperar enquanto tentamos modificar os serviços existentes para atender a qualquer finalidade que precisarmos.

Em alguns casos, podemos alterá-los para serem desativados na inicialização ou modificar o caminho do serviço para o próprio binário. Esteja ciente de que esses exemplos são apenas algumas das possibilidades de ações que podemos realizar. Com um comando tão versátil, temos muitas opções para manipular os serviços para fazer o que precisamos.

### Desabilitando Windows Updates usando sc

Para configurar serviços, devemos usar o parâmetro config no sc. Isso nos permitirá modificar os valores dos serviços existentes, independentemente de estarem em execução ou não. Todas as alterações feitas com este comando são refletidas no registro do Windows, bem como no banco de dados do Service Control Manager (SCM). Lembre-se de que todas as alterações nos serviços existentes só serão totalmente atualizadas após a reinicialização do serviço.

Infelizmente, o recurso Windows Update (versão 10 e superior) não depende apenas de um serviço para executar sua funcionalidade. As atualizações do Windows contam com os seguintes serviços:

- wuauserv: Windows Update Service
- bits: Background Intelligent Transfer Service

Vamos consultar todos os serviços necessários e ver o que está em execução no momento e precisa ser interrompido antes de fazer as alterações necessárias.

```cmd-session
C:\WINDOWS\system32> sc query wuauserv

SERVICE_NAME: wuauserv
        TYPE               : 30  WIN32
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

C:\WINDOWS\system32> sc query bits

SERVICE_NAME: bits
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_PRESHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

A partir das informações fornecidas acima, podemos ver que o serviço wuauserv não está ativo no momento, pois o sistema não está em processo de atualização. No entanto, o serviço de bits (necessário para baixar atualizações) está em execução em nosso sistema. Podemos interromper este serviço fazendo o seguinte:

```cmd-session
C:\WINDOWS\system32> sc stop bits

SERVICE_NAME: bits
        TYPE               : 30  WIN32
        STATE              : 3  STOP_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x1
        WAIT_HINT          : 0x0
```

```cmd-session
C:\WINDOWS\system32> sc config wuauserv start= disabled

[SC] ChangeServiceConfig SUCCESS
```

```cmd-session
C:\WINDOWS\system32> sc config bits start= disabled

[SC] ChangeServiceConfig SUCCESS
```


```cmd-session
C:\WINDOWS\system32> sc start wuauserv 

[SC] StartService FAILED 1058:

The service cannot be started, either because it is disabled or because it has no enabled devices associated with it.

C:\WINDOWS\system32> sc start bits

[SC] StartService FAILED 1058:

The service cannot be started, either because it is disabled or because it has no enabled devices associated with it.
```


# Usando tasklist para pesquisar serviços

```cmd-session
C:\htb> tasklist /svc


Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                       108 N/A
smss.exe                       412 N/A
csrss.exe                      612 N/A
wininit.exe                    684 N/A
csrss.exe                      708 N/A
services.exe                   768 N/A
lsass.exe                      796 KeyIso, SamSs, VaultSvc
winlogon.exe                   856 N/A
svchost.exe                    984 BrokerInfrastructure, DcomLaunch, PlugPlay,
                                   Power, SystemEventsBroker
fontdrvhost.exe               1012 N/A
fontdrvhost.exe               1020 N/A
svchost.exe                    616 RpcEptMapper, RpcSs
svchost.exe                    996 LSM
dwm.exe                       1068 N/A
svchost.exe                   1236 CoreMessagingRegistrar
svchost.exe                   1244 lmhosts
svchost.exe                   1324 NcbService
svchost.exe                   1332 TimeBrokerSvc
svchost.exe                   1352 Schedule
<SNIP>
```

# Usando net start

```cmd-session
C:\htb> net start

These Windows services are started:

   Application Information
   AppX Deployment Service (AppXSVC)
   AVCTP service
   Background Tasks Infrastructure Service
   Base Filtering Engine
   BcastDVRUserService_3321a
   Capability Access Manager Service
   cbdhsvc_3321a
   CDPUserSvc_3321a
   Client License Service (ClipSVC)
   CNG Key Isolation
   COM+ Event System
   COM+ System Application
   Connected Devices Platform Service
   Connected User Experiences and Telemetry
   CoreMessaging
   Credential Manager
   Cryptographic Services
   Data Usage
   DCOM Server Process Launcher
   Delivery Optimization
   Device Association Service
   DHCP Client
```

# Usando wmic

```cmd-session
C:\htb> wmic service list brief

ExitCode  Name                                      ProcessId  StartMode  State    Status
1077      AJRouter                                  0          Manual     Stopped  OK
1077      ALG                                       0          Manual     Stopped  OK
1077      AppIDSvc                                  0          Manual     Stopped  OK
0         Appinfo                                   5016       Manual     Running  OK
1077      AppMgmt                                   0          Manual     Stopped  OK
1077      AppReadiness                              0          Manual     Stopped  OK
1077      AppVClient                                0          Disabled   Stopped  OK
0         AppXSvc                                   9996       Manual     Running  OK
1077      AssignedAccessManagerSvc                  0          Manual     Stopped  OK
0         AudioEndpointBuilder                      2076       Auto       Running  OK
0         Audiosrv                                  2332       Auto       Running  OK
1077      autotimesvc                               0          Manual     Stopped  OK
1077      AxInstSV                                  0          Manual     Stopped  OK
1077      BDESVC                                    0          Manual     Stopped  OK
0         BFE                                       2696       Auto       Running  OK
0         BITS                                      0          Manual     Stopped  OK
0         BrokerInfrastructure                      984        Auto       Running  OK
1077      BTAGService                               0          Manual     Stopped  OK
0         BthAvctpSvc                               4448       Manual     Running  OK
1077      bthserv                                   0          Manual     Stopped  OK
0         camsvc                                    5676       Manual     Running  OK
0         CDPSvc                                    4724       Auto       Running  OK
1077      CertPropSvc                               0          Manual     Stopped  OK
0         ClipSVC                                   9156       Manual     Running  OK
1077      cloudidsvc                                0          Manual     Stopped  OK
0         COMSysApp                                 3668       Manual     Running  OK
0         CoreMessagingRegistrar                    1236       Auto       Running  OK
0         CryptSvc                                  2844       Auto       Running  OK
<SNIP>
```

