WMI é um subsistema do PowerShell que fornece aos administradores do sistema ferramentas poderosas para monitoramento do sistema. O objetivo do WMI é consolidar o gerenciamento de dispositivos e aplicativos nas redes corporativas. O WMI é uma parte essencial do sistema operacional Windows e vem pré-instalado desde o Windows 2000. Ele é composto pelos seguintes componentes:
- WMI service: O processo de instrumentação de gerenciamento do Windows, que é executado automaticamente na inicialização e atua como um intermediário entre os provedores WMI, o repositório WMI e os aplicativos de gerenciamento.
- Managed objects: Quaisquer componentes lógicos ou físicos que possam ser gerenciados pelo WMI.
- WMI providers: Objetos que monitoram eventos/dados relacionados a um objeto específico.
- Classes: Eles são usados ​​pelos provedores WMI para passar dados para o serviço WMI.
- Methods: Estes são anexados às classes e permitem que ações sejam executadas. Por exemplo, métodos podem ser usados ​​para iniciar/parar processos em máquinas remotas.
- WMI repository: Um banco de dados que armazena todos os dados estáticos relacionados ao WMI.
- CMI Object Manager: O sistema que solicita dados de provedores WMI e os retorna ao aplicativo que os solicitou.
- WMI API: Permite que os aplicativos acessem a infraestrutura WMI.
- WMI Consumer: Envia consultas a objetos por meio do CMI Object Manager.

Alguns dos usos do WMI são:
- Informações de status para sistemas locais/remotos
- Definição de configurações de segurança em máquinas/aplicativos remotos
- Definir e alterar permissões de usuários e grupos
- Configurando/modificando as propriedades do sistema
- Execução de código
- Agendamento de processos
- Configurando o log

Todas essas tarefas podem ser executadas usando uma combinação de PowerShell e a interface de linha de comando WMI(WMIC). O WMI pode ser executado por meio do prompt de comando do Windows digitando WMIC para abrir um shell interativo ou executando um comando diretamente, como wmic computersystem get name para obter o nome do host.

Podemos visualizar uma lista de comandos e aliases WMIC digitando `WMIC /?`.

```cmd-session
C:\htb> wmic /?

WMIC is deprecated.

[global switches] <command>

The following global switches are available:
/NAMESPACE           Path for the namespace the alias operate against.
/ROLE                Path for the role containing the alias definitions.
/NODE                Servers the alias will operate against.
/IMPLEVEL            Client impersonation level.
/AUTHLEVEL           Client authentication level.
/LOCALE              Language id the client should use.
/PRIVILEGES          Enable or disable all privileges.
/TRACE               Outputs debugging information to stderr.
/RECORD              Logs all input commands and output.
/INTERACTIVE         Sets or resets the interactive mode.
/FAILFAST            Sets or resets the FailFast mode.
/USER                User to be used during the session.
/PASSWORD            Password to be used for session login.
/OUTPUT              Specifies the mode for output redirection.
/APPEND              Specifies the mode for output redirection.
/AGGREGATE           Sets or resets aggregate mode.
/AUTHORITY           Specifies the <authority type> for the connection.
/?[:<BRIEF|FULL>]    Usage information.

For more information on a specific global switch, type: switch-name /?

Press any key to continue, or press the ESCAPE key to stop
```

O exemplo de comando a seguir lista informações sobre o sistema operacional.

```cmd-session
C:\htb> wmic os list brief

BuildNumber  Organization  RegisteredUser  SerialNumber             SystemDirectory      Version
19041                      Owner           00123-00123-00123-AAOEM  C:\Windows\system32  10.0.19041
```

O WMIC usa aliases e verbos, advérbios e opções associados. O exemplo de comando acima usa LIST para mostrar dados e o advérbio BRIEF para fornecer apenas o conjunto principal de propriedades. Uma lista detalhada de verbos, interruptores e advérbios está disponível aqui(https://learn.microsoft.com/en-us/windows/win32/wmisdk/wmic). O WMI pode ser usado com o PowerShell usando o módulo Get-WmiObject. Este módulo é usado para obter instâncias de classes WMI ou informações sobre classes disponíveis. Este módulo pode ser usado em máquinas locais ou remotas.

Aqui podemos obter informações sobre o sistema operacional.

```powershell-session
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version | ft

SystemDirectory     BuildNumber SerialNumber            Version
---------------     ----------- ------------            -------
C:\Windows\system32 19041       00123-00123-00123-AAOEM 10.0.19041
```

Também podemos usar o módulo Invoke-WmiMethod, que é usado para chamar os métodos dos objetos WMI. Um exemplo simples é renomear um arquivo. Podemos ver que o comando foi concluído corretamente porque o ReturnValue está definido como 0.

```powershell-session
PS C:\htb> Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" -Name Rename -ArgumentList "C:\Users\Public\kerberoasted_users.csv"


__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
ReturnValue      : 0
PSComputerName   :
```


