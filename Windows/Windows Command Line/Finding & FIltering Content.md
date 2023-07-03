Ser capaz de pesquisar, encontrar e filtrar conteúdo para o que estamos procurando é um requisito absoluto para qualquer usuário que utilize a CLI (independentemente de qual shell ou sistema operacional). 

# Explicação da saída do PowerShell (Objects explicados)


Com o PowerShell, nem tudo são sequências de texto genéricas como em Bash ou cmd. No PowerShell, tudo é um Object. No entanto, o que é um Object?

Um Object é uma instância individual de uma classe no PowerShell.  Vamos usar o exemplo de um computador como nosso objeto. O total de tudo (peças, tempo, design, software, etc.) faz de um computador um computador.

Uma classe é um schema ou representação única de uma coisa (objeto) e como a soma de suas propriedades a define. O blueprint é usado para definir como esse computador deve ser montado e o que tudo dentro dele pode ser considerado uma Classe.

Propriedades são simplesmente os dados associados a um objeto no PowerShell. Para nosso exemplo de computador, as partes individuais que montamos para fazer o computador são suas propriedades. Cada parte serve a um propósito e tem uso único dentro do objeto.

O que são Métodos? Simplificando, métodos são todas as funções que nosso objeto possui. Nosso computador nos permite processar dados, navegar na internet, aprender novas habilidades, etc. Todos esses são os métodos para nosso objeto.

# Encontrando e Filtrando Objetos

Vejamos isso por meio de um contexto de objeto de usuário. Um usuário pode fazer coisas como acessar arquivos, executar aplicativos e dados de entrada

```powershell-session
PS C:\htb> Get-LocalUser administrator | get-member

   TypeName: Microsoft.PowerShell.Commands.LocalUser

Name                   MemberType Definition
----                   ---------- ----------
Clone                  Method     Microsoft.PowerShell.Commands.LocalUser Clone()
Equals                 Method     bool Equals(System.Object obj)
GetHashCode            Method     int GetHashCode()
GetType                Method     type GetType()
ToString               Method     string ToString()
AccountExpires         Property   System.Nullable[datetime] AccountExpires {get;set;}
Description            Property   string Description {get;set;}
Enabled                Property   bool Enabled {get;set;}
FullName               Property   string FullName {get;set;}
LastLogon              Property   System.Nullable[datetime] LastLogon {get;set;}
Name                   Property   string Name {get;set;}
ObjectClass            Property   string ObjectClass {get;set;}
PasswordChangeableDate Property   System.Nullable[datetime] PasswordChangeableDate {get;set;}
PasswordExpires        Property   System.Nullable[datetime] PasswordExpires {get;set;}
PasswordLastSet        Property   System.Nullable[datetime] PasswordLastSet {get;set;}
PasswordRequired       Property   bool PasswordRequired {get;set;}
PrincipalSource        Property   System.Nullable[Microsoft.PowerShell.Commands.PrincipalSource] PrincipalSource {ge...
SID                    Property   System.Security.Principal.SecurityIdentifier SID {get;set;}
UserMayChangePassword  Property   bool UserMayChangePassword {get;set;}
```

Agora que podemos ver todas as propriedades de um usuário, vamos ver como essas propriedades se parecem quando geradas pelo PowerShell. O cmdlet Select-Object nos ajudará a conseguir isso. Desta forma, agora entendemos o que compõe um objeto de usuário.

```powershell-session
PS C:\htb> Get-LocalUser administrator | Select-Object -Property *


AccountExpires         :
Description            : Built-in account for administering the computer/domain
Enabled                : False
FullName               :
PasswordChangeableDate :
PasswordExpires        :
UserMayChangePassword  : True
PasswordRequired       : True
PasswordLastSet        :
LastLogon              : 1/20/2021 5:39:14 PM
Name                   : Administrator
SID                    : S-1-5-21-3916821513-3027319641-390562114-500
PrincipalSource        : Local
ObjectClass            : User
```

Um usuário é um objeto pequeno realisticamente, mas pode ser muito olhar para a saída dessa maneira, especialmente de itens como listas ou tabelas grandes. E se quiséssemos filtrar esse conteúdo ou mostrá-lo de maneira mais precisa? Poderíamos filtrar as propriedades de um objeto que não queremos ver selecionando as poucas que queremos. Vamos olhar para nossos usuários e ver quais definiram uma senha recentemente. 

```powershell-session
PS C:\htb> Get-LocalUser * | Select-Object -Property Name,PasswordLastSet

Name               PasswordLastSet
----               ---------------
Administrator
DefaultAccount
Guest
MTanaka              1/27/2021 2:39:55 PM
WDAGUtilityAccount 1/18/2021 7:40:22 AM
```

Também podemos classificar(sort) e agrupar (group) nossos objetos nessas propriedades.

# Classificando (sorting) e agrupando (grouping)

```powershell-session
PS C:\htb> Get-LocalUser * | Sort-Object -Property Name | Group-Object -property Enabled

Count Name                      Group
----- ----                      -----
    4 False                     {Administrator, DefaultAccount, Guest, WDAGUtilityAccount}
    1 True                      {MTanaka}
```

Utilizamos os cmdlets Sort-Object e Group-Object para localizar todos os usuários, classificá-los por nome e agrupá-los com base em sua propriedade Enabled. Na saída, podemos ver que vários usuários estão desabilitados e não estão em uso para logon interativo. Este é apenas um exemplo rápido do que pode ser feito com objetos do PowerShell e a grande quantidade de informações armazenadas em cada objeto. À medida que nos aprofundamos no PowerShell e exploramos o sistema operacional Windows, notamos que as classes por trás de muitos objetos são extensas e frequentemente compartilhadas. Tenha isso em mente enquanto trabalhamos com eles cada vez mais.

# Por que precisamos filtrar nossos resultados?

Estamos mudando e usando um exemplo de get-service para esta demonstração. Olhar para usuários básicos e informações não produz muito em termos de resultados, mas outros objetos contêm uma quantidade extraordinária de dados. Abaixo está um exemplo de apenas um fragmento da saída de Get-Service:

```powershell-session
PS C:\htb> Get-Service | Select-Object -Property *

Name                : AarSvc_1ca8ea
RequiredServices    : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Agent Activation Runtime_1ca8ea
DependentServices   : {}
MachineName         : .
ServiceName         : AarSvc_1ca8ea
ServicesDependedOn  : {}
ServiceHandle       :
Status              : Stopped
ServiceType         : 224
StartType           : Manual
Site                :
Container           :

Name                : AdobeARMservice
RequiredServices    : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
DisplayName         : Adobe Acrobat Update Service
DependentServices   : {}
MachineName         : .
ServiceName         : AdobeARMservice
ServicesDependedOn  : {}
ServiceHandle       :
Status              : Running
ServiceType         : Win32OwnProcess
StartType           : Automatic
Site                :
Container           :

Name                : agent_ovpnconnect
RequiredServices    : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
DisplayName         : OpenVPN Agent agent_ovpnconnect
DependentServices   : {}
MachineName         : .
ServiceName         : agent_ovpnconnect
ServicesDependedOn  : {}
ServiceHandle       :
Status              : Running
ServiceType         : Win32OwnProcess
StartType           : Automatic
Site                :
Container           :

<SNIP>
```

São dados demais para filtrar, certo? Vamos dividi-lo ainda mais e formatar esses dados como uma lista. Podemos usar a string de comando get-service | Select-Object -Property DisplayName,Name,Status | Sort-Object DisplayName | fl para alterar nossa saída da seguinte forma:

```powershell-session
PS C:\htb> get-service | Select-Object -Property DisplayName,Name,Status | Sort-Object DisplayName | fl 

<SNIP>
DisplayName : ActiveX Installer (AxInstSV)
Name        : AxInstSV
Status      : Stopped

DisplayName : Adobe Acrobat Update Service
Name        : AdobeARMservice
Status      : Running

DisplayName : Adobe Genuine Monitor Service
Name        : AGMService
Status      : Running
<SNIP>
```

Isso ainda é uma tonelada de saída, mas é um pouco mais legível. Aqui é onde começamos a nos perguntar: precisamos de toda essa saída? Nós nos preocupamos com todos esses objetos ou apenas com um subconjunto específico deles? E se quiséssemos determinar se um serviço específico está em execução, mas precisávamos descobrir o Name específico? O Where-Object pode avaliar os objetos passados para ele e seus valores de propriedade específicos e procurar as informações necessárias. Considere este cenário:

Cenário: Acabamos de desembarcar um shell inicial em um host por meio de um protocolo não seguro, expondo o host ao mundo. Antes de entrarmos mais fundo, precisamos avaliar o host e determinar se algum serviço ou aplicativo defensivo está em execução. Primeiro, procuramos por qualquer instância dos serviços `Windows Defender` em execução.

Usar Where-Object (where como um alias) e o parâmetro correspondente a -like nos permitirá determinar se estamos seguros para continuar procurando por qualquer coisa com "Defender" na propriedade. Nesta instância, verificamos a propriedade DisplayName de todos os objetos recuperados por Get-Service.

```powershell-session
PS C:\htb>  Get-Service | where DisplayName -like '*Defender*'

Status   Name               DisplayName
------   ----               -----------
Running  mpssvc             Windows Defender Firewall
Stopped  Sense              Windows Defender Advanced Threat Pr...
Running  WdNisSvc           Microsoft Defender Antivirus Networ...
Running  WinDefend          Microsoft Defender Antivirus Service
```

Como podemos ver, nossos resultados retornaram vários serviços em execução, incluindo Defender Firewall, Advanced Threat Protection e muito mais. Esta é uma boa e uma má notícia para nós. Não podemos simplesmente mergulhar e começar a fazer as coisas porque provavelmente seremos vistos pelos serviços defensivos, mas é bom que os tenhamos avistado e agora possamos nos reagrupar e fazer um plano de ações defensivas de evasão(defensive evasion) a serem tomadas. Embora seja um cenário de exemplo rápido, isso é algo como pentesters que frequentemente encontramos e devemos ser capazes de detectar e identificar quando as medidas defensivas estão em vigor. Este exemplo traz uma maneira interessante de modificar nossas pesquisas, no entanto. Os valores de avaliação podem ser benéficos para a nossa causa. Vamos verificá-los mais.

# A Avaliação de Valores

Where e muitos outros cmdlets podem avaliar objetos e dados com base nos valores que esses objetos e suas propriedades contêm. A saída acima é um excelente exemplo disso utilizando o operador de comparação -like. Ele procurará qualquer coisa que corresponda aos valores expressos e possa incluir curingas como `*.` Abaixo está uma lista rápida (não abrangente) de outras expressões úteis que podemos utilizar:

- Like: Like utiliza expressões curinga para realizar a correspondência. Por exemplo, `*Defender*` corresponderia a qualquer coisa com a palavra Defender em algum lugar do valor.
- Contains: Contains obterá o objeto se qualquer item no valor da propriedade corresponder exatamente ao especificado.
- Equal: Especifica uma correspondência exata (com distinção entre maiúsculas e minúsculas) para o valor da propriedade fornecido.
- Match: É uma correspondência de expressão regular com o valor fornecido.
- Not: especifica uma correspondência se a propriedade estiver em branco ou não existir. Ele também corresponderá a $False. 

É claro que existem muitos outros operadores de comparação que podemos usar como, maior que, menor que e negativos como NotEqual, mas nesse tipo de pesquisa eles podem não ser tão amplamente usados. Agora com um entendimento -GTE de como esses operadores podem nos ajudar mais do que antes (veja o que eu fiz lá), vamos voltar a nos aprofundar nos serviços do Defender. Agora vamos procurar objetos de serviço com um DisplayName novamente, como `<algo>Defender<algo>`.

```powershell-session
PS C:\htb> Get-Service | where DisplayName -like '*Defender*' | Select-Object -Property *

Name                : mpssvc
RequiredServices    : {mpsdrv, bfe}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Windows Defender Firewall
DependentServices   :
MachineName         : .
ServiceName         : mpssvc
ServicesDependedOn  : {mpsdrv, bfe}
ServiceHandle       :
Status              : Running
ServiceType         : Win32ShareProcess
StartType           : Automatic
Site                :
Container           :

Name                : Sense
RequiredServices    : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Windows Defender Advanced Threat Protection Service
```

Nossos resultados acima agora filtram todos os serviços não associados ao Windows Defender e exibem a lista completa de propriedades de cada correspondência. Agora podemos examinar os serviços, determinar se estão em execução e até mesmo se podemos, em nosso nível de permissão atual, afetar o status desses serviços (desligá-los, desativá-los etc.). Durante muitos dos comandos que emitimos nas últimas seções, usamos o | símbolo para concatenar vários comandos que normalmente emitimos separadamente. Abaixo, discutiremos o que é isso e como funciona para nós.

Em sua forma mais simples, o Pipeline no PowerShell fornece ao usuário final uma maneira de encadear comandos. Essa cadeia é chamada de Pipeline e também é chamada de pipe ou comandos de tubulação juntos. Com o PowerShell manipulando objetos da maneira que faz, podemos emitir um comando e, em seguida, canalizar (|) a saída do objeto resultante para outro comando para ação. O Pipeline irá interpretar e executar os comandos um de cada vez da esquerda para a direita. Fizemos isso em alguns exemplos nas seções anteriores, então estamos nos aprofundando aqui. Como um exemplo usando o Pipeline para encadear comandos, pode ser assim:

```powershell-session
PS C:\htb> Command-1 | Command-2 | Command-3

Output from the result of 1+2+3  
```

```powershell-session
PS C:\htb> 
Command-1 |
  Command-2 |
    Command-3  

Output result from Pipeline
```

```powershell-session
PS C:\htb> Get-Process | Where-Object CPU | Where-Object Path
    | Get-Item |  

Output result from Pipeline  
```

Cada maneira é uma maneira perfeitamente aceitável de concatenar os comandos. O PowerShell pode interpretar o que você deseja com base na posição do (|) na string. Vejamos um exemplo de uso do pipeline para nos fornecer dados acionáveis. A seguir, emitiremos o cmdlet Get-Process, classificaremos os dados resultantes e mediremos quantos processos exclusivos temos em execução em nosso host.

```powershell-session
PS C:\htb> get-process | sort | unique | measure-object

Count             : 113  
```

Como resultado, o pipeline gera a contagem total (113) de processos exclusivos em execução naquele momento. Suponha que quebremos o pipeline em qualquer ponto específico. Nesse caso, podemos ver a saída do processo classificada, filtrada para instâncias exclusivas (sem nomes duplicados) ou apenas uma saída numérica do cmdlet Measure-Object. A tarefa que realizamos foi relativamente simples. No entanto, e se pudéssemos aproveitar isso para algo mais complexo, como classificar novas entradas de log, filtrar códigos de log de eventos específicos ou processando grandes quantidades de dados (um banco de dados e todas as suas entradas, por exemplo) procurando strings específicas? É aqui que o Pipeline pode aumentar nossa produtividade e simplificar a saída que recebemos, tornando-o uma ferramenta vital para qualquer administrador de sistema ou pentester.

# Chain Operators ( && e || )

Atualmente, o Windows PowerShell 5.1 e anteriores não oferecem suporte a operadores de cadeia de pipeline usados ​​dessa maneira. Se você vir erros, deverá instalar o PowerShell 7 juntamente com o Windows PowerShell. Eles não são a mesma coisa.

- &&: define uma condição na qual o PowerShell executará o próximo comando embutido se o comando atual for concluído corretamente.
- ||: Sets a condition in which PowerShell will execute the following command inline `if` the current command `fails`.

Esses operadores podem ser úteis para nos ajudar a definir condições para scripts que serão executados se uma meta ou condição for atendida. Por exemplo:

Cenário: digamos que escrevemos uma cadeia de comandos na qual queremos obter o conteúdo de um arquivo e, em seguida, executar ping em um host. Podemos definir isso para executar ping no host se o comando inicial for bem-sucedido com && ou para executar apenas se o comando falhar ||. Vamos ver os dois.

Nesta saída, podemos ver que ambos os comandos foram bem-sucedidos na execução porque obtivemos a saída do arquivo test.txt impressa no console junto com os resultados de nosso comando ping.

```powershell-session
PS C:\htb> Get-Content '.\test.txt' && ping 8.8.8.8
pass or fail

Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=23ms TTL=118
Reply from 8.8.8.8: bytes=32 time=28ms TTL=118
Reply from 8.8.8.8: bytes=32 time=28ms TTL=118
Reply from 8.8.8.8: bytes=32 time=21ms TTL=118

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 21ms, Maximum = 28ms, Average = 25ms
```

Com essa saída, podemos ver que nosso pipeline se fechou após o primeiro comando, pois foi executado adequadamente, imprimindo a saída do arquivo no console.

```powershell-session
PS C:\htb>  Get-Content '.\test.txt' || ping 8.8.8.8

pass or fail
```

Aqui podemos ver que nosso pipeline foi executado completamente. Nosso primeiro comando falhou porque o nome do arquivo foi digitado errado e o PowerShell vê isso como o arquivo que solicitamos não existe. Como o primeiro comando falhou, nosso segundo comando foi executado.

```powershell-session
PS C:\htb> Get-Content '.\testss.txt' || ping 8.8.8.8

Get-Content: Cannot find path 'C:\Users\MTanaka\Desktop\testss.txt' because it does not exist.

Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=20ms TTL=118
Reply from 8.8.8.8: bytes=32 time=37ms TTL=118
Reply from 8.8.8.8: bytes=32 time=19ms TTL=118

<SNIP>
```

O pipeline e os operadores que usamos são benéficos para nós de uma perspectiva de economia de tempo, além de poder alimentar rapidamente objetos e dados de uma tarefa para outra. A emissão de vários comandos em linha é muito mais eficaz do que a emissão manual de cada comando. E se quiséssemos pesquisar strings ou dados no conteúdo de arquivos e diretórios? Essa é uma tarefa comum que muitos pentesters executam enquanto enumeram um host ao qual obtiveram acesso. Pesquisar com o que está nativamente no host é uma ótima maneira de manter nossa discrição e garantir que não estamos introduzindo novos riscos ao trazer ferramentas para o ambiente do usuário.

# Achando dados com conteúdo

Existem algumas ferramentas, como Snaffler, Winpeas e similares, que podem procurar por arquivos e strings interessantes, mas e se não pudermos trazer uma nova ferramenta para o host? Como podemos buscar informações confidenciais, como credenciais, chaves, etc.? A combinação de cmdlets que praticamos nas seções anteriores emparelhados com novos cmdlets como Select-String e where é uma excelente maneira de fazer root em um sistema de arquivos. 

 Select-String (sls como um alias) para aqueles mais familiarizados com o uso da CLI do Linux, funciona da mesma maneira que Grep ou findstr.exe no prompt de comando do Windows. Ele executa avaliações de strings de entrada, conteúdo de arquivo e muito mais com base na correspondência de padrão de expressão regular (regex). Quando uma correspondência for encontrada, Select-String exibirá a linha correspondente, o nome do arquivo e o número da linha em que foi encontrado por padrão. No geral, é um cmdlet flexível e útil que deve estar na caixa de ferramentas de todos. A seguir, levaremos nosso novo cmdlet para um teste enquanto procuramos informações em alguns arquivos e diretórios interessantes aos quais devemos prestar atenção ao enumerar um host.
 
## Achando arquivos interessantes dentro de um diretório

Ao procurar arquivos interessantes, pense nos tipos de arquivos mais comuns que usaríamos diariamente e comece por aí. Em um determinado dia, podemos escrever arquivos de texto, um pouco de Markdown, um pouco de Python, PowerShell e muitos outros. Queremos procurar essas coisas ao pesquisar em um host, pois é onde os usuários e administradores interagem mais. Podemos começar com Get-ChildItem e realizar uma pesquisa recursiva em uma pasta. Vamos testá-lo.

```powershell-session
PS C:\htb> Get-ChildItem -Path C:\Users\MTanaka\ -File -Recurse 

 Directory: C:\Users\MTanaka\Desktop\notedump\NoteDump

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/26/2022  1:47 PM           1092 demo notes.md
-a---           4/22/2022  2:20 PM           1074 noteDump.py
-a---           4/22/2022  2:55 PM          61440 plum.sqlite
-a---           4/22/2022  2:20 PM            375 README.md
<SNIP>
```

Notaremos que ele retorna rapidamente informações demais. Cada arquivo em cada pasta no caminho especificado foi enviado para nosso console. Vamos usar a condição de olhar para o nome de extensões específicas de tipo de arquivo. Para fazer isso, canalizaremos a saída de Get-ChildItem por meio do cmdlet where para filtrar nossa saída. Vamos testar primeiro procurando pela extensão de tipo de arquivo *.txt.

```powershell-session
PS C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_.Name -like "*.txt")}

Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          10/11/2022  3:32 PM            183 demo-notes.txt
-a---            4/4/2022  9:37 AM            188 q2-to-do.txt
-a---          10/12/2022 11:26 AM             14 test.txt
-a---            1/4/2022 11:23 PM            310 Untitled-1.txt

    Directory: C:\Users\MTanaka\Desktop\win-stuff

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           5/19/2021 10:12 PM           7831 wmic.txt

    Directory: C:\Users\MTanaka\Desktop\Workshop\

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-----            1/7/2022  4:39 PM            945 info.txt
```

Isso funcionou com muito mais eficiência. Retornamos apenas os arquivos que correspondiam ao tipo de arquivo txt por causa do atributo `$_.Name` de nosso filtro.

```powershell-session
PS C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_.Name -like "*.txt" -or $_.Name -like "*.py" -or $_.Name -like "*.ps1" -or $_.Name -like "*.md" -or $_.Name -like "*.csv")}

 Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          10/11/2022  3:32 PM            183 demo-notes.txt
-a---          10/11/2022 10:22 AM           1286 github-creds.txt
-a---            4/4/2022  9:37 AM            188 q2-to-do.txt
-a---           9/18/2022 12:35 PM             30 notes.txt
-a---          10/12/2022 11:26 AM             14 test.txt
-a---           2/14/2022  3:40 PM           3824 remote-connect.ps1
-a---          10/11/2022  8:22 PM            874 treats.ps1
-a---            1/4/2022 11:23 PM            310 Untitled-1.txt

    Directory: C:\Users\MTanaka\Desktop\notedump\NoteDump

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/26/2022  1:47 PM           1092 demo.md
-a---           4/22/2022  2:20 PM           1074 noteDump.py
-a---           4/22/2022  2:20 PM            375 README.md
```

Nossa string funcionou e agora estamos recuperando vários tipos de arquivo de Get-ChildItem! Agora que temos nossa lista de arquivos interessantes, poderíamos virar e canalizar esses objetos para outro cmdlet (Select-String) que pesquisa em seu conteúdo strings e palavras-chave ou frases interessantes. Vamos ver isso em ação.

```powershell-session
PS C:\htb> Get-ChildItem -Path C:\Users\MTanaka\ -Filter "*.txt" -Recurse -File | sls "Password","credential","key"

CFP-Notes.txt:99:Lazzaro, N. (2004). Why we play games: Four keys to more emotion without story. Retrieved from:
notes.txt:3:- Password: F@ll2022!
wmic.txt:67:  wmic netlogin get name,badpasswordcount
wmic.txt:69:Are the screensavers password protected? What is the timeout? good use: see that all systems are
complying with policy evil use: find systems to walk up and use (assuming physical access is an option)
```

Lembre-se de que Select-string não diferencia maiúsculas de minúsculas por padrão. Se desejarmos que seja, podemos alimentá-lo com o modificador -CaseSensitive. Agora vamos combinar nossa pesquisa de arquivo original com nosso filtro de conteúdo.

```powershell-session
PS C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_. Name -like "*.txt" -or $_. Name -like "*.py" -or $_. Name -like "*.ps1" -or $_. Name -like "*.md" -or $_. Name -like "*.csv")} | sls "Password","credential","key","UserName"

New-PC-Setup.md:56:  - getting your vpn key
CFP-Notes.txt:99:Lazzaro, N. (2004). Why we play games: Four keys to more emotion without story. Retrieved from:
notes.txt:3:- Password: F@ll2022!
wmic.txt:54:  wmic computersystem get username
wmic.txt:67:  wmic netlogin get name,badpasswordcount
wmic.txt:69:Are the screensavers password protected? What is the timeout? good use: see that all systems are
complying with policy evil use: find systems to walk up and use (assuming physical access is an option)
wmic.txt:83:  wmic netuse get Name,username,connectiontype,localname
```

Nossos comandos no pipeline estão ficando mais longos, mas podemos facilmente limpar nossa visualização para torná-la legível. Olhando para os nossos resultados, no entanto, foi um processo muito mais tranquilo alimentar os resultados da nossa lista de arquivos em nossa pesquisa de palavras-chave. Observe que há algumas novas adições em nossa string de comando. Adicionamos uma linha para que o comando continue se ocorrer um erro (-ErrorAction SilentlyContinue). Isso nos ajuda a garantir que todo o nosso pipeline permaneça intacto quando ocorre em um arquivo ou diretório que não pode ser lido. Encontrar e filtrar conteúdo pode ser um quebra-cabeça interessante por si só. Determinar quais palavras e strings produzirão os melhores resultados é uma tarefa em constante evolução e geralmente varia de acordo com o cliente.

## Diretórios úteis para verificar

Enquanto procuramos por arquivos valiosos e outros conteúdos, podemos verificar muitos outros arquivos valiosos em muitos lugares diferentes. A lista abaixo contém apenas algumas dicas e truques que podem ser usados ​​em nossa busca por loot.

- Procurar em uma pasta Users \AppData\ é um ótimo lugar para começar. Muitos aplicativos armazenam arquivos de configuração, salvamentos temporários de documentos e muito mais.
- Uma pasta inicial de Usuários C:\User\User\ é um local de armazenamento comum; coisas como chaves VPN, chaves SSH e muito mais são armazenadas. Normalmente em pastas ocultas. (Get-ChildItem -Hidden)
- Os arquivos de histórico do console mantidos pelo host são um poço infinito de informações, especialmente se você acessar o host de um administrador. Você pode verificar dois pontos diferentes:
   - C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt
   - Get-Content (Get-PSReadlineOption).HistorySavePath
- Verificar a área de transferência de um usuário também pode fornecer informações úteis. Você pode fazer isso com Get-Clipboard
- Observar as tarefas agendadas também pode ser útil.

Estes são apenas alguns lugares interessantes para verificar. Use-o como ponto de partida para criar e manter sua própria lista de verificação à medida que suas habilidades e experiências aumentam.