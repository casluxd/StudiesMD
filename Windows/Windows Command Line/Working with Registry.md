
Devemos estar confortáveis ​​com a CLI neste ponto. É hora de nivelar nossas habilidades novamente e enfrentar um dos aspectos mais complicados do sistema operacional Windows, o Registro. Esta seção nos orientará sobre o que é o Registro, como navegar nele e como ler pares de chave/valor e fazer alterações conforme necessário.

## O que é o Registro do Windows?

Em sua essência, o Registry pode ser considerado uma árvore hierárquica que contém dois elementos essenciais: chaves e valores. Essa árvore armazena todas as informações necessárias para que o sistema operacional e o software instalado sejam executados em subárvores (pense nelas como ramos de uma árvore). Essas informações podem ser qualquer coisa, desde configurações a diretórios de instalação até opções e valores específicos que determinam como tudo funciona. Como Pentesters, o Registry é um ótimo local para encontrar informações úteis, plantar persistência e muito mais. O MITRE fornece muitos exemplos excelentes do que um agente de ameaça pode fazer com acesso (local ou remotamente) à seção de registro de um host.

## O que são Chaves

As chaves, em essência, são contêineres que representam um componente específico do PC. As chaves podem conter outras chaves e valores como dados. Essas entradas podem assumir várias formas e os contextos de nomenclatura exigem apenas que uma Chave seja nomeada usando caracteres alfanuméricos (imprimíveis) e não faz distinção entre maiúsculas e minúsculas. Como exemplo visual de Keys, se observarmos a imagem abaixo, cada pasta dentro do retângulo verde é uma Key e contém subchaves.

#### Chaves (em verde)

![[Pasted image 20230525221110.png]]

## Arquivos de Chave do Registro

As chaves raiz do Registro de um sistema host são armazenadas em vários arquivos diferentes e podem ser acessadas em `C:\Windows\System32\Config\`. Junto com esses arquivos de chave, seções de registro são mantidas em todo o host em vários outros lugares.

### Chaves do Registro Raiz

```powershell-session
PS C:\htb> Get-ChildItem C:\Windows\System32\config\

    Directory: C:\Windows\System32\config

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           12/7/2019  4:14 AM                Journal
d----           12/7/2019  4:14 AM                RegBack
d----           4/28/2021 11:43 AM                systemprofile
d----           9/18/2021 12:22 AM                TxR
-a---          10/12/2022 10:06 AM         786432 BBI
-a---           1/20/2021  5:13 PM          28672 BCD-Template
-a---          10/18/2022 11:14 AM       38273024 COMPONENTS
-a---          10/12/2022 10:06 AM        1048576 DEFAULT
-a---          10/15/2022  9:33 PM       13463552 DRIVERS
-a---           1/27/2021  2:54 PM          32768 ELAM
-a---          10/12/2022 10:06 AM         131072 SAM
-a---          10/12/2022 10:06 AM          65536 SECURITY
-a---          10/12/2022 10:06 AM      168034304 SOFTWARE
-a---          10/12/2022 10:06 AM       29884416 SYSTEM
-a---          10/12/2022 10:06 AM           1623 VSMIDK
```

Para obter uma lista detalhada de todos os Registry Hives e seus arquivos de suporte no sistema operacional, podemos ver AQUI(https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-hives). Agora vamos discutir Valores dentro do Registro.

## O que são valores?

Os valores representam dados na forma de objetos que pertencem a essa chave específica. Esses valores consistem em um nome, uma especificação de tipo e os dados necessários para identificar para que serve. A imagem abaixo representa visualmente os valores como os dados entre as linhas laranja. Esses valores estão aninhados na chave Installer, que, por sua vez, está dentro de outra chave.

![[Pasted image 20230525221236.png]]

Podemos consultar a lista completa de valores de chave de registro AQUI(https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types). Ao todo, são 11 tipos de valores diferentes que podem ser configurados.

## Seções de registro

Cada host do Windows possui um conjunto de chaves de registro predefinidas que mantêm o host e as configurações necessárias para uso. Abaixo está um detalhamento de cada seção e o que pode ser encontrado referenciado dentro.

- HKEY_LOCAL_MACHINE(HKLM): Essa subárvore contém informações sobre o estado físico do computador, como hardware e dados do sistema operacional, tipos de barramento, memória, drivers de dispositivo e muito mais.
- HKEY_CURRENT_CONFIG(HKCC): Esta seção contém registros para o perfil de hardware atual do host. (mostra a variação entre as configurações atuais e padrão) Pense nisso como um redirecionamento da chave de perfil HKLM CurrentControlSet.
- HKEY_CLASSES_ROOT(HKCR): Informações de tipo de arquivo, extensões de interface do usuário e configurações de compatibilidade com versões anteriores são definidas aqui.
- HKEY_CURRENT_USER(HKCU): As entradas de valor aqui definem as configurações específicas de sistema operacional e software para cada usuário específico. As configurações do perfil de roaming, incluindo as preferências do usuário, são armazenadas em HKCU.
- HKEY_USERS(HKU): O perfil de usuário padrão e as definições de configuração do usuário atual para o computador local são definidos em HKU.

Existem outras chaves predefinidas para o Registro, mas são específicas para determinadas versões e configurações regionais do Windows. Para obter mais informações sobre essas entradas e chaves do Registro em geral, consulte a documentação fornecida pela Microsoft(https://learn.microsoft.com/en-us/windows/win32/sysinfo/predefined-keys).

# Por que as informações armazenadas no registro são importantes?

Como pentester, o Registry pode ser um tesouro de informações que pode nos ajudar a promover nossos compromissos. Tudo, desde qual software está instalado, revisão atual do sistema operacional, configurações de segurança pertinentes, controle do Defender e muito mais, pode ser encontrado no Registro. Podemos encontrar todas essas informações em outros lugares? Sim. Mas não há ponto único melhor para encontrar tudo isso e ter a capacidade de fazer alterações amplas no host simultaneamente.

De uma perspectiva ofensiva, o Registro é difícil para os Defensores protegerem. As seções são enormes e cheias de centenas de entradas. Encontrar uma mudança ou acréscimo singular entre as seções é como procurar uma agulha no palheiro (a menos que eles mantenham backups sólidos de suas configurações e estados de host). Ter uma compreensão geral do Registro e onde os valores-chave estão dentro pode nos ajudar a agir mais rapidamente e para os defensores identificarem quaisquer problemas mais cedo.

## Como acessamos as informações?

A partir da CLI, temos várias opções para acessar o Registro e gerenciar nossas chaves. A primeira é usar o reg.exe. Reg é um executável DOS feito explicitamente para uso no gerenciamento de configurações do Registro. A segunda é usar os cmdlets Get-Item e Get-ItemProperty para ler chaves e valores. Se quisermos fazer uma alteração, o uso de New-ItemProperty resolverá o problema.

## Consultando entradas de registro

Veremos primeiro como usar Get-Item e Get-ChildItem. Abaixo, podemos ver a saída usando Get-Item e canalizando o resultado para Select-Object.

```powershell
PS C:\htb> Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property  

SecurityHealth
RtkAudUService
WavesSvc
DisplayLinkTrayApp
LogiOptions
Acrobat Assistant 8.0
(default)
Focusrite Notifier
AdobeGCInvoker-1.0
```

É uma saída simples e mostra apenas o nome dos serviços/aplicativos em execução no momento. Se quiséssemos ver cada chave e objeto dentro de uma seção, também poderíamos usar Get-ChildItem com o parâmetro -Recurse da seguinte forma:

```powershell
PS C:\htb> Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse

Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths
<SNIP>
Name                           Property
----                           --------
7zFM.exe                       (default) : C:\Program Files\7-Zip\7zFM.exe
                               Path      : C:\Program Files\7-Zip\
Acrobat.exe                    (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrobat.exe
                               Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
AcrobatInfo.exe                (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\AcrobatInfo.exe
                               Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
AcroDist.exe                   Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
                               (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrodist.exe
Ahk2Exe.exe                    (default) : C:\Program Files\AutoHotkey\Compiler\Ahk2Exe.exe
AutoHotkey.exe                 (default) : C:\Program Files\AutoHotkey\AutoHotkey.exe
chrome.exe                     (default) : C:\Program Files\Google\Chrome\Application\chrome.exe
                               Path      : C:\Program Files\Google\Chrome\Application
cmmgr32.exe                    CmNative          : 2
                               CmstpExtensionDll : C:\Windows\System32\cmcfg32.dll
CNMNSST.exe                    (default) : C:\Program Files (x86)\Canon\IJ Network Scanner Selector EX\CNMNSST.exe
                               Path      : C:\Program Files (x86)\Canon\IJ Network Scanner Selector EX
devenv.exe                     (default) : "C:\Program Files\Microsoft Visual
                               Studio\2022\Community\common7\ide\devenv.exe"
dfshim.dll                     UseURL : 1
excel.exe                      (default) : C:\Program Files\Microsoft Office\Root\Office16\EXCEL.EXE
                               Path      : C:\Program Files\Microsoft Office\Root\Office16\
                               UseURL    : 1
                               SaveURL   : 1
fsquirt.exe                    DropTarget : {047ea9a0-93bb-415f-a1c3-d7aeb3dd5087}
IEDIAG.EXE                     (default) : C:\Program Files\Internet Explorer\IEDIAGCMD.EXE
                               Path      : C:\Program Files\Internet Explorer;
IEDIAGCMD.EXE                  (default) : C:\Program Files\Internet Explorer\IEDIAGCMD.EXE
                               Path      : C:\Program Files\Internet Explorer;
IEXPLORE.EXE                   (default) : C:\Program Files\Internet Explorer\IEXPLORE.EXE
                               Path      : C:\Program Files\Internet Explorer;
install.exe                    BlockOnTSNonInstallMode : 1
javaws.exe                     (default) : C:\Program Files\Java\jre1.8.0_341\bin\javaws.exe
                               Path      : C:\Program Files\Java\jre1.8.0_341\bin
licensemanagershellext.exe     (default) : C:\Windows\System32\licensemanagershellext.exe
mip.exe                        (default) : C:\Program Files\Common Files\Microsoft Shared\Ink\mip.exe
mpc-hc64.exe                   (default) : C:\Program Files (x86)\K-Lite Codec Pack\MPC-HC64\mpc-hc64.exe
                               Path      : C:\Program Files (x86)\K-Lite Codec Pack\MPC-HC64
mplayer2.exe                   (default) : "C:\Program Files\Windows Media Player\wmplayer.exe"
                               Path      : C:\Program Files\Windows Media Player
MSACCESS.EXE                   (default) : C:\Program Files\Microsoft Office\Root\Office16\MSACCESS.EXE
                               Path      : C:\Program Files\Microsoft Office\Root\Office16\
                               UseURL    : 1
msedge.exe                     (default) : C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe
                               Path      : C:\Program Files (x86)\Microsoft\Edge\Application

    Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths\msedge.exe

Name                           Property
----                           --------
SupportedProtocols             http  :
                               https :
<SNIP>  
```


```powershell
PS C:\htb> Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

SecurityHealth        : C:\Windows\system32\SecurityHealthSystray.exe
RtkAudUService        : "C:\Windows\System32\DriverStore\FileRepository\realtekservice.inf_amd64_85cff5320735903
                        d\RtkAudUService64.exe" -background
WavesSvc              : "C:\Windows\System32\DriverStore\FileRepository\wavesapo9de.inf_amd64_d350b8504310bbf5\W
                        avesSvc64.exe" -Jack
DisplayLinkTrayApp    : "C:\Program Files\DisplayLink Core Software\DisplayLinkTrayApp.exe" -basicMode
LogiOptions           : C:\Program Files\Logitech\LogiOptions\LogiOptions.exe /noui
Acrobat Assistant 8.0 : "C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrotray.exe"
(default)             :
Focusrite Notifier    : "C:\Program Files\Focusriteusb\Focusrite Notifier.exe"
AdobeGCInvoker-1.0    : "C:\Program Files (x86)\Common Files\Adobe\AdobeGCClient\AGCInvokerUtility.exe"
PSPath                : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Curren
                        tVersion\Run
PSParentPath          : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Curren
                        tVersion
PSChildName           : Run
PSProvider            : Microsoft.PowerShell.Core\Registry
```

Agora vamos quebrar isso. Emitimos o comando Get-ItemProperty, especificamos o caminho como uma busca no Registro e especificamos a chave `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`. A saída nos fornece o nome dos serviços iniciados e o valor que foi usado para executá-los (o caminho de onde foram executados). Essa chave do Registro é usada para iniciar serviços/aplicativos quando um usuário faz login no host. É uma ótima chave para ter visibilidade e ter em mente como testador de penetração. Existem várias versões dessa chave que discutiremos um pouco mais adiante nesta seção. Usar Get-ItemProperty é muito mais legível do que Get-Item. Quando se trata de consultar informações, também podemos usar o Reg.exe. Vamos dar uma olhada na saída disso. Vamos olhar para uma chave mais simples para este exemplo.

```powershell
PS C:\htb> reg query HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip

HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
    Path64    REG_SZ    C:\Program Files\7-Zip\
    Path    REG_SZ    C:\Program Files\7-Zip\
```

Consultamos a chave HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip com Reg.exe, que nos forneceu os valores associados. Podemos ver que dois valores são definidos, Path e Path64, o ValueType é um valor Reg_SZ que especifica que contém uma string Unicode ou ASCII, e esse valor é o caminho para 7-Zip C:\Program Files\7-Zip\ .

# Encontrando informações no registro

Para nós, como pentesters e administradores, encontrar dados no Registro é uma habilidade obrigatória. É aqui que Reg.exe realmente brilha para nós. Podemos usá-lo para pesquisar palavras-chave e strings como senha e nome de usuário por meio de nomes de chave e valor ou dos dados contidos. Antes de colocá-lo em uso, vamos detalhar o uso de Reg Query. Veremos a string de comando `REG QUERY HKCU /F "password" /t REG_SZ /S /K.`

- Reg query: Estamos chamando Reg.exe e especificando que queremos consultar dados.
- HKCU: Esta parte está definindo o caminho a ser pesquisado. Neste caso, estamos procurando em todos os HKey_Current_User.
- /f "password":  /f define o padrão que estamos procurando. Neste caso, estamos procurando por "password".
- /t REG_SZ: /t está definindo o tipo de valor a ser pesquisado. Se não especificarmos, a consulta reg pesquisará todos os tipos.
- /s: /s diz para pesquisar todas as subchaves e valores recursivamente.
- /k: /k limita a pesquisa apenas pelos nomes das chaves.

```powershell
PS C:\htb>  REG QUERY HKCU /F "Password" /t REG_SZ /S /K

HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\Winlogon\PasswordExpiryNotification
    NotShownErrorTime    REG_SZ    08::23::24, 2022/10/19
    NotShownErrorReason    REG_SZ    GetPwdResetInfoFailed

End of search: 2 match(es) found.
```

Nossos resultados dessa consulta podem ser mais empolgantes, mas ainda vale a pena dar uma olhada e usar uma pesquisa semelhante para outras palavras-chave e frases como nome de usuário, credenciais e chaves. Podemos nos surpreender com o que encontramos. Como podemos ver, consultar chaves e valores de registro é relativamente fácil. E se quisermos definir um novo valor ou criar uma nova chave?

# Criando e modificando chaves e valores do registro

Ao lidar com a modificação ou criação de novas chaves e valores, podemos usar cmdlets padrão do PowerShell como New-Item, Set-Item, New-ItemProperty e Set-ItemProperty ou utilizar Reg.exe novamente para fazer as mudanças que precisamos. Vamos tentar criar uma nova chave de registro abaixo. Para nosso exemplo, criaremos uma nova chave de teste na seção RunOnce `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce` chamada TestKey.

`Cenário: chegamos a um host e podemos adicionar uma nova chave de registro para persistência. Precisamos definir uma chave chamada TestKey e um valor de C:\Users\htb-student\Downloads\payload.exe que informa RunOnce para executar nossa carga útil, deixamos no host na próxima vez que o usuário fizer login. Isso garantirá que, se o host reiniciar ou perdermos o acesso, na próxima vez que o usuário fizer login, obteremos um novo shell.`

```powershell
PS C:\htb> New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey

    Hive: HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce

Name                           Property
----                           --------
TestKey   
```

Agora temos uma nova chave dentro da chave RunOnce. Ao especificar o parâmetro -Path, evitamos alterar nossa localização no shell para onde queremos adicionar uma chave no Registro, deixando-nos trabalhar de qualquer lugar, desde que especifiquemos o caminho absoluto. Vamos definir uma propriedade e um valor agora.

```powershell
PS C:\htb>  New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"

access       : C:\Users\htb-student\Downloads\payload.exe
PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\
               TestKey
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
PSChildName  : TestKey
PSDrive      : HKCU
PSProvider   : Microsoft.PowerShell.Core\Registry
```

Depois de usar New-ItemProperty para definir nosso valor chamado access e especificar o valor como `C:\Users\htb-student\Downloads\payload.exe` podemos ver nos resultados que nosso valor foi criado com sucesso e as informações correspondentes, como localização do caminho e nome da chave. Só para mostrar que nossa chave foi criada, podemos ver a nova chave e seus valores na imagem abaixo do editor GUI Registry.

![[Pasted image 20230525222819.png]]

Se quiséssemos adicionar o mesmo par chave/valor usando Reg.exe, faríamos assim:

```powershell
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce\TestKey" /v access /t REG_SZ /d "C:\Users\htb-student\Downloads\payload.exe"  
```

Agora, em um pentest real, teríamos deixado uma carga executável no host e, caso o host reiniciasse ou o usuário fizesse login, adquiriríamos um novo shell para o nosso C2. Esse valor não nos ajuda muito agora, então vamos praticar a exclusão dele.

```powershell
PS C:\htb> Remove-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access"

PS C:\htb> Get-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey
```

Se nenhuma janela de erro aparecer, nosso par chave/valor foi excluído com sucesso. No entanto, esta é uma daquelas coisas com as quais você deve ter muito cuidado. A remoção de entradas do Registro do Windows pode afetar negativamente o host e seu funcionamento. Certifique-se de saber o que está removendo antes. Nas sábias palavras do Tio Ben, "Com grandes poderes vêm grandes responsabilidades."