Os serviços são um componente importante do sistema operacional Windows. Eles permitem a criação e gerenciamento de processos de longa duração. Os serviços do Windows podem ser iniciados automaticamente na inicialização do sistema sem intervenção do usuário. Esses serviços podem continuar sendo executados em segundo plano mesmo depois que o usuário fizer logout de sua conta no sistema.

Aplicativos também podem ser criados para instalação como um serviço, como um aplicativo de monitoramento de rede instalado em um servidor. Os serviços no Windows são responsáveis ​​por muitas funções no sistema operacional Windows, como funções de rede, realização de diagnósticos do sistema, gerenciamento de credenciais de usuário, controle de atualizações do Windows e muito mais.

Os serviços do Windows são gerenciados por meio do sistema Service Control Manager (SCM), acessível por meio do suplemento `services.msc` MMC.

Este suplemento fornece uma interface GUI para interagir e gerenciar serviços e exibe informações sobre cada serviço instalado. Essas informações incluem o nome do serviço, descrição, status, tipo de inicialização e o usuário sob o qual o serviço é executado.

Também é possível consultar e gerenciar serviços por meio da linha de comando usando sc.exe usando cmdlets do PowerShell, como Get-Service.

```powershell-session
PS C:\htb> Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl


Name                : AdobeARMservice
DisplayName         : Adobe Acrobat Update Service
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess

Name                : Appinfo
DisplayName         : Application Information
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {RpcSs, ProfSvc}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess, Win32ShareProcess
```

Os status do serviço podem aparecer como Em execução, Parado ou Pausado, e podem ser configurados para iniciar manualmente, automaticamente ou com atraso na inicialização do sistema. Os serviços também podem ser mostrados no estado Iniciando ou Parando se alguma ação os tiver acionado para iniciar ou parar. O Windows tem três categorias de serviços: Serviços Locais, Serviços de Rede e Serviços do Sistema. Os serviços geralmente só podem ser criados, modificados e excluídos por usuários com privilégios administrativos. Erros de configuração em torno de permissões de serviço são um vetor de escalonamento de privilégio comum em sistemas Windows.

No Windows, temos alguns serviços críticos do sistema que não podem ser interrompidos e reiniciados sem a reinicialização do sistema. Se atualizarmos qualquer arquivo ou recurso em uso por um desses serviços, devemos reiniciar o sistema.

- smss.exe: Subsistema do Gerenciador de Sessões. Responsável pela manipulação de sessões no sistema.
- csrss.exe: Processo de tempo de execução do servidor cliente. A parte do modo de usuário do subsistema do Windows.
- wininit.exe: Inicia o arquivo Wininit .ini que lista todas as alterações a serem feitas no Windows quando o computador é reiniciado após a instalação de um programa.
- logonui.exe: Usado para facilitar o login do usuário em um PC
- lsass.exe: O Servidor de Autenticação de Segurança Local verifica a validade dos logons do usuário em um PC ou servidor. Ele gera o processo responsável por autenticar usuários para o serviço Winlogon.
- services.exe: Gerencia a operação de iniciar e parar serviços.
- winlogon.exe: Responsável por lidar com a sequência de atenção segura, carregar um perfil de usuário no logon e bloquear o computador quando um protetor de tela estiver em execução.
- System: Um processo do sistema em segundo plano que executa o kernel do Windows.
- svchost.exe com RPCSS: Gerencia os serviços do sistema executados a partir de bibliotecas de vínculo dinâmico (arquivos com a extensão .dll), como "Atualizações automáticas", "Firewall do Windows" e "Plug and Play". Usa o serviço Remote Procedure Call (RPC) (RPCSS).
- svchost com Dcom/PnP: Gerencia os serviços do sistema executados a partir de bibliotecas de vínculo dinâmico (arquivos com a extensão .dll), como "Atualizações automáticas", "Firewall do Windows" e "Plug and Play". Usa os serviços Distributed Component Object Model (DCOM) e Plug and Play (PnP).

Este link(https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_components#Services) possui uma lista de componentes do Windows, incluindo os principais serviços.

# Processos

Os processos são executados em segundo plano nos sistemas Windows. Eles são executados automaticamente como parte do sistema operacional Windows ou são iniciados por outros aplicativos instalados.

Os processos associados aos aplicativos instalados geralmente podem ser encerrados sem causar um impacto severo no sistema operacional. Certos processos são críticos e, se encerrados, impedirão que certos componentes do sistema operacional funcionem corretamente. Alguns exemplos incluem o processo de Windows Logon Application, System, System Idle Process, Windows Start-up Application, Client Server Runtime, Windows Session Manager, Local Security Authority Subsystem Service (LSASS).


## Local Security Authority Subsystem Service (LSASS)

lsass.exe é o processo responsável por impor a política de segurança em sistemas Windows. Quando um usuário tenta fazer logon no sistema, esse processo verifica a tentativa de logon e cria tokens de acesso com base nos níveis de permissão do usuário. O LSASS também é responsável pelas alterações de senha da conta do usuário. Todos os eventos associados a esse processo (tentativas de logon/logoff etc.) são registrados no Log de segurança do Windows. O LSASS é um high-value target extremamente alto, pois existem várias ferramentas para extrair credenciais de texto não criptografado e hash armazenadas na memória por esse processo.

## Sysinternal tools

A suíte de ferramentas Sysinternal(https://docs.microsoft.com/en-us/sysinternals) é um conjunto de aplicativos portáteis do Windows que podem ser usados ​​para administrar sistemas Windows (na maior parte sem exigir instalação). As ferramentas podem ser baixadas do site da Microsoft ou carregadas diretamente de um compartilhamento de arquivo acessível pela Internet digitando `\\live.sysinternals.com\tools` em uma janela do Windows Explorer.

Por exemplo, podemos executar procdump.exe diretamente deste compartilhamento sem baixá-lo diretamente para o disco:

```cmd-session
C:\htb> \\live.sysinternals.com\tools\procdump.exe -accepteula

ProcDump v9.0 - Sysinternals process dump utility
Copyright (C) 2009-2017 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Monitors a process and writes a dump file when the process exceeds the
specified criteria or has an exception.

Capture Usage:
   procdump.exe [-mm] [-ma] [-mp] [-mc Mask] [-md Callback_DLL] [-mk]
                [-n Count]
                [-s Seconds]
                [-c|-cl CPU_Usage [-u]]
                [-m|-ml Commit_Usage]
                [-p|-pl Counter_Threshold]
                [-h]
                [-e [1 [-g] [-b]]]
                [-l]
                [-t]
                [-f  Include_Filter, ...]
                [-fx Exclude_Filter, ...]
                [-o]
                [-r [1..5] [-a]]
                [-wer]
                [-64]
                {
                 {{[-w] Process_Name | Service_Name | PID} [Dump_File | Dump_Folder]}
                |
                 {-x Dump_Folder Image_File [Argument, ...]}
                }
				
<SNIP>
```

A suíte inclui ferramentas como o Process Explorer, uma versão aprimorada do Task Manager e o Process Monitor, que podem ser usados ​​para monitorar o sistema de arquivos, o registro e a atividade de rede relacionada a qualquer processo em execução no sistema. Algumas ferramentas adicionais são o TCPView, que é usado para monitorar a atividade da Internet, e o PSExec, que pode ser usado para gerenciar/conectar remotamente a sistemas via protocolo SMB.

Essas ferramentas podem ser úteis para testadores de penetração, por exemplo, descobrir processos interessantes e possíveis caminhos de escalonamento de privilégios, bem como para movimentação lateral.

# Task Manager

Windows Task Manager é uma ferramenta poderosa para gerenciar sistemas Windows. Ele fornece informações sobre processos em execução, desempenho do sistema, serviços em execução, programas de inicialização, processos de usuários/usuários conectados e serviços.

- Process Tab: Mostra uma lista de aplicativos em execução e processos em segundo plano junto com a CPU, memória, disco, rede e uso de energia para cada um.
- Perfomance Tab: Mostra gráficos e dados como utilização de CPU, tempo de atividade do sistema, uso de memória, disco e rede e uso de GPU. Também podemos abrir o Monitor de recursos, que nos dá uma visão muito mais aprofundada do uso atual de recursos de CPU, memória, disco e rede.

![image](https://academy.hackthebox.com/storage/modules/49/resource_monitor.png)

- App history tab: Mostra o uso de recursos para a conta de usuário atual para cada aplicativo por um período de tempo.
- Startup tab: Mostra quais aplicativos estão configurados para iniciar na inicialização, bem como o impacto no processo de inicialização.
- Users tab: Mostra usuários logados e o uso de processos/recursos associados à sua sessão.
- Details tab: Mostra o nome, ID do processo (PID), status, nome de usuário associado, CPU e uso de memória para cada aplicativo em execução.
- Services tab: Mostra o nome, PID, descrição e status de cada serviço instalado. O suplemento Serviços também pode ser acessado nesta guia.

# Process Explorer 

O Process Explorer faz parte do conjunto de ferramentas Sysinternals. Esta ferramenta pode mostrar quais manipuladores e processos DLL são carregados quando um programa é executado. O Process Explorer mostra uma lista de processos atualmente em execução e, a partir daí, podemos ver quais identificadores o processo selecionou em uma exibição ou as DLLs e arquivos de memória trocada que foram carregados em outra exibição. Também podemos pesquisar na ferramenta para mostrar quais processos estão vinculados a um identificador ou DLL específico. A ferramenta também pode ser usada para analisar relacionamentos de processo pai-filho para ver quais processos filho são gerados por um aplicativo e ajudar a solucionar quaisquer problemas, como processos órfãos que podem ser deixados para trás quando um processo é finalizado.