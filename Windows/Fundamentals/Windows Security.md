A segurança é um tópico crítico nos sistemas operacionais Windows. Os sistemas Windows têm muitas partes móveis que apresentam uma vasta superfície de ataque. Devido aos muitos aplicativos, recursos e camadas de configurações integrados, os sistemas Windows podem ser facilmente configurados incorretamente, abrindo-os para ataques, mesmo que estejam totalmente corrigidos.

Ele tem muitos recursos integrados que podem ser abusados ​​e sofreu com uma ampla variedade de vulnerabilidades críticas, resultando em explorações locais e remotas amplamente usadas e muito eficazes.

A Microsoft melhorou a segurança do Windows ao longo dos anos. À medida que a interconectividade do nosso mundo continua a se expandir e os invasores se tornam mais sofisticados, a Microsoft continuou a adicionar novos recursos que podem ser usados ​​por administradores de sistemas para fortalecer os sistemas e bloquear e detectar ativamente tentativas de invasão e uso indevido.

# Security Identifier (SID)

Cada um dos principais de segurança no sistema possui um identificador de segurança exclusivo (SID). O sistema gera SIDs automaticamente. Isso significa que, mesmo que, por exemplo, tenhamos dois usuários idênticos no sistema, o Windows pode distinguir os dois e seus direitos com base em seus SIDs. SIDs são valores de string com comprimentos diferentes, que são armazenados no banco de dados de segurança. Esses SIDs são adicionados ao token de acesso do usuário para identificar todas as ações que o usuário está autorizado a realizar.

Um SID consiste na autoridade do identificador e no ID relativo (RID). Em um ambiente de domínio Active Directory (AD), o SID também inclui o domínio SID.

```powershell-session
PS C:\htb> whoami /user

USER INFORMATION
----------------

User Name           SID
=================== =============================================
ws01\bob S-1-5-21-674899381-4069889467-2080702030-1002
```

O SID é dividido neste padrão.

```powershell-session
(SID)-(revision level)-(identifier-authority)-(subauthority1)-(subauthority2)-(etc)
```

Vamos dividir o SID peça por peça.

S -> SID > Identifica a string como um SID.
1 -> REVISION LEVEL -> Até hoje, isso nunca mudou e sempre foi 1.
5 -> IDENTIFIER-AUTHORITY -> Uma string de 48 bits que identifica a autoridade (o computador ou a rede) que criou o SID.
21 -> SUBAUTHORITY1 -> Este é um número variável que identifica a relação do usuário ou grupo descrito pelo SID para a autoridade que o criou. Ele nos diz em que ordem essa autoridade criou a conta do usuário.
674899381-4069889467-2080702030 -> SUBAUTHORITY2 -> Nos diz qual computador (ou domínio) criou o número
1002 -> SUBAUTHORITY3 -> O RID que distingue uma conta da outra. Informa se este usuário é um usuário normal, um convidado, um administrador ou parte de algum outro grupo

## Security Accounts Manager (SAM) and Access Control Entries (ACE)

O SAM concede direitos a uma rede para executar processos específicos.

Os próprios direitos de acesso são gerenciados por entradas de controle de acesso (ACE) em listas de controle de acesso (ACL). As ACLs contêm ACEs que definem quais usuários, grupos ou processos têm acesso a um arquivo ou para executar um processo, por exemplo.

As permissões para acessar um objeto protegível são fornecidas pelo descritor de segurança, classificado em dois tipos de ACLs: a lista de controle de acesso discricionário (DACL) ou a lista de controle de acesso do sistema (SACL). Cada thread e processo iniciado ou iniciado por um usuário passa por um processo de autorização. Uma parte integrante desse processo são os tokens de acesso, validados pela Autoridade de Segurança Local (LSA). Além do SID, esses tokens de acesso contêm outras informações relevantes para a segurança. Entender essas funcionalidades é uma parte essencial para aprender como usar e contornar esses mecanismos de segurança durante a fase de escalonamento de privilégios.

## User Account Control (UAC)

O Controle de Conta de Usuário (UAC) é um recurso de segurança do Windows para evitar que malwares executem ou manipulem processos que possam danificar o computador ou seu conteúdo. Existe o Modo de Aprovação do Administrador no UAC, que é projetado para impedir que softwares indesejados sejam instalados sem o conhecimento do administrador ou para impedir que sejam feitas alterações em todo o sistema. Certamente você já viu o prompt de consentimento se instalou um software específico e seu sistema pediu confirmação se deseja instalá-lo. Como a instalação requer direitos de administrador, uma janela aparece, perguntando se você deseja confirmar a instalação. Com um usuário padrão que não tenha direitos para a instalação, a execução será negada ou será solicitada a senha do administrador. Esse prompt de consentimento interrompe a execução de scripts ou binários que malwares ou invasores tentam executar até que o usuário insira a senha ou confirme a execução.
Para entender como o UAC funciona, precisamos saber como ele é estruturado e como funciona, e o que aciona o prompt de consentimento. O diagrama a seguir, adaptado da fonte aqui(https://learn.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works), ilustra como o UAC funciona.

![image](https://academy.hackthebox.com/storage/modules/49/uacarchitecture1.png)

## Registry

O Registro é um banco de dados hierárquico no Windows crítico para o sistema operacional. Ele armazena configurações de baixo nível para o sistema operacional Windows e aplicativos que optam por usá-lo. Ele é dividido em dados específicos do computador e específicos do usuário. Podemos abrir o Editor do Registro digitando regedit na linha de comando ou na barra de pesquisa do Windows.

![image](https://academy.hackthebox.com/storage/modules/49/regedit.png)

A estrutura em árvore consiste em pastas principais (chaves raiz) nas quais estão localizadas subpastas (subchaves) com suas entradas/arquivos (valores). Existem 11 tipos diferentes de valores que podem ser inseridos em uma subchave.

- REG_BINARY: Dados binários em qualquer forma.
- REG_DWORD: Um número de 32 bits.
- REG_DWORD_LITTLE_ENDIAN: Um número de 32 bits no formato little-endian. O Windows foi projetado para ser executado em arquiteturas de computador de pequeno porte. Portanto, esse valor é definido como REG_DWORD nos arquivos de cabeçalho do Windows.
- REG_DWORD_BIG_ENDIAN: Um número de 32 bits no formato big-endian. Alguns sistemas UNIX suportam arquiteturas big-endian.
- REG_EXPAND_SZ: Uma cadeia de caracteres terminada em nulo que contém referências não expandidas a variáveis ​​de ambiente (por exemplo, "%PATH%"). Será uma string Unicode ou ANSI dependendo se você usar as funções Unicode ou ANSI. Para expandir as referências de variáveis ​​de ambiente, use a função ExpandEnvironmentStrings.
- REG_LINK: Uma string Unicode terminada em nulo contendo o caminho de destino de um link simbólico criado chamando a função RegCreateKeyEx com REG_OPTION_CREATE_LINK.
- REG_MULTI_SZ: Uma sequência de strings terminadas em nulo, terminadas por uma string vazia (`\0`). O seguinte é um exemplo: `String1\0String2\0String3\0LastString\0\0`  O primeiro `\0` termina a primeira string, o penúltimo `\0` termina a última string, e o `\0` final encerra a sequência. Observe que o terminador final deve ser fatorado no comprimento da string.
- REG_NONE: Nenhum tipo de valor definido.
- REG_QWORD: Um número de 64 bits.
- REG_QWORD_LITTLE_ENDIAN: Um número de 64 bits no formato little-endian. O Windows foi projetado para ser executado em arquiteturas de computador de pequeno porte. Portanto, esse valor é definido como REG_QWORD nos arquivos de cabeçalho do Windows.
- REG_SZ: Uma string terminada em nulo. Isso será uma string Unicode ou ANSI, dependendo se você usa as funções Unicode ou ANSI.

Cada pasta em Computador é uma chave. Todas as chaves raiz começam com HKEY. Uma chave como HKEY-LOCAL-MACHINE é abreviada para HKLM. HKLM contém todas as configurações relevantes para o sistema local. Essa chave raiz contém seis subchaves como SAM, SECURITY, SYSTEM, SOFTWARE, HARDWARE e BCD, carregadas na memória no momento da inicialização (exceto HARDWARE que é carregado dinamicamente).

![image](https://academy.hackthebox.com/storage/modules/49/regedit2.png)

Todo o registro do sistema é armazenado em vários arquivos no sistema operacional. Você pode encontrá-los em `C:\Windows\System32\Config\`.

```powershell-session
PS C:\htb> ls

    Directory: C:\Windows\system32\config

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         12/7/2019   4:14 AM                Journal
d-----         12/7/2019   4:14 AM                RegBack
d-----         12/7/2019   4:14 AM                systemprofile
d-----         8/12/2020   1:43 AM                TxR
-a----         8/13/2020   6:02 PM        1048576 BBI
-a----         6/25/2020   4:36 PM          28672 BCD-Template
-a----         8/30/2020  12:17 PM       33816576 COMPONENTS
-a----         8/13/2020   6:02 PM         524288 DEFAULT
-a----         8/26/2020   7:51 PM        4603904 DRIVERS
-a----         6/25/2020   3:37 PM          32768 ELAM
-a----         8/13/2020   6:02 PM          65536 SAM
-a----         8/13/2020   6:02 PM          65536 SECURITY
-a----         8/13/2020   6:02 PM       87818240 SOFTWARE
-a----         8/13/2020   6:02 PM       17039360 SYSTEM
```

```powershell-session
PS C:\htb> gci -Hidden

    Directory: C:\Users\bob

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--h--         6/25/2020   5:12 PM                AppData
d--hsl         6/25/2020   5:12 PM                Application Data
d--hsl         6/25/2020   5:12 PM                Cookies
d--hsl         6/25/2020   5:12 PM                Local Settings
d--h--         6/25/2020   5:12 PM                MicrosoftEdgeBackups
d--hsl         6/25/2020   5:12 PM                My Documents
d--hsl         6/25/2020   5:12 PM                NetHood
d--hsl         6/25/2020   5:12 PM                PrintHood
d--hsl         6/25/2020   5:12 PM                Recent
d--hsl         6/25/2020   5:12 PM                SendTo
d--hsl         6/25/2020   5:12 PM                Start Menu
d--hsl         6/25/2020   5:12 PM                Templates
-a-h--         8/13/2020   6:01 PM        2883584 NTUSER.DAT
-a-hs-         6/25/2020   5:12 PM         524288 ntuser.dat.LOG1
-a-hs-         6/25/2020   5:12 PM        1011712 ntuser.dat.LOG2
-a-hs-         8/17/2020   5:46 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.0.regtrans-ms
-a-hs-         8/17/2020  12:13 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.1.regtrans-ms
-a-hs-         8/17/2020  12:13 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.2.regtrans-ms
-a-hs-         8/17/2020   5:46 PM          65536 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.blf
-a-hs-         6/25/2020   5:15 PM          65536 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TM.blf
-a-hs-         6/25/2020   5:12 PM         524288 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TMContainer000000000
                                                  00000000001.regtrans-ms
-a-hs-         6/25/2020   5:12 PM         524288 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TMContainer000000000
                                                  00000000002.regtrans-ms
---hs-         6/25/2020   5:12 PM             20 ntuser.ini
```

## Chaves de registro Run e RunOnce

Há também as chamadas seções de registro, que contêm um grupo lógico de chaves, subchaves e valores para dar suporte a software e arquivos carregados na memória quando o sistema operacional é iniciado ou um usuário faz login. Essas colmeias são úteis para manter o acesso ao sistema. Elas são chamadas de chaves de registro Run e RunOnce.

O registro do Windows inclui as quatro chaves a seguir:

```powershell-session
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

Aqui está um exemplo da chave `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run` enquanto conectado a um sistema.

```powershell-session
PS C:\htb> reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
    SecurityHealth    REG_EXPAND_SZ    %windir%\system32\SecurityHealthSystray.exe
    RTHDVCPL    REG_SZ    "C:\Program Files\Realtek\Audio\HDA\RtkNGUI64.exe" -s
    Greenshot    REG_SZ    C:\Program Files\Greenshot\Greenshot.exe
```

Aqui está um exemplo de `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run` mostrando aplicativos em execução no usuário atual enquanto está conectado a um sistema.

```powershell-session
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
    OneDrive    REG_SZ    "C:\Users\bob\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
    OPENVPN-GUI    REG_SZ    C:\Program Files\OpenVPN\bin\openvpn-gui.exe
    Docker Desktop    REG_SZ    C:\Program Files\Docker\Docker\Docker Desktop.exe
```

## Application Whitelisting

Uma lista de permissões de aplicativos é uma lista de aplicativos de software ou executáveis ​​aprovados que podem estar presentes e serem executados em um sistema. O objetivo é proteger o ambiente contra malware prejudicial e software não aprovado que não se alinha com as necessidades comerciais específicas de uma organização. A implementação de uma lista de permissões forçada pode ser um desafio, especialmente em uma rede grande. Uma organização deve implementar uma lista de permissões no modo de auditoria inicialmente para garantir que todos os aplicativos necessários sejam incluídos na lista de permissões e não sejam bloqueados por um erro de omissão, o que pode causar mais problemas do que soluções.

A lista negra, por outro lado, especifica uma lista de software/aplicativos nocivos ou não permitidos para bloquear, e todos os outros podem ser executados/instalados. A lista de permissões é baseada em um princípio de "confiança zero", no qual todos os softwares/aplicativos são considerados "ruins", exceto aqueles especificamente permitidos. A manutenção de uma lista branca geralmente tem menos sobrecarga, pois o administrador do sistema precisará apenas especificar o que é permitido e não atualizar constantemente uma "lista negra" com novos aplicativos maliciosos. 

A lista de permissões é recomendada por organizações como o NIST, especialmente em ambientes de alta segurança.

## Applocker

O AppLocker é a solução de lista branca de aplicativos da Microsoft e foi introduzido pela primeira vez no Windows 7. O AppLocker oferece aos administradores de sistema controle sobre quais aplicativos e arquivos os usuários podem executar. Ele oferece controle granular sobre executáveis, scripts, arquivos do instalador do Windows, DLLs, aplicativos empacotados e instaladores de aplicativos compactados.

Ele permite a criação de regras com base em atributos de arquivo, como o nome do editor (que pode ser derivado da assinatura digital), nome do produto, nome do arquivo e versão. As regras também podem ser configuradas com base em caminhos de arquivo e hashes. As regras podem ser aplicadas a grupos de segurança ou usuários individuais, com base na necessidade do negócio. O AppLocker pode ser implantado no modo de auditoria primeiro para testar o impacto antes de impor todas as regras.

## Local Group Policy

A Diretiva de Grupo permite que os administradores definam, configurem e ajustem uma variedade de configurações. Em um ambiente de domínio, as políticas de grupo são enviadas de um controlador de domínio para todas as máquinas ingressadas no domínio às quais os objetos de política de grupo (GPOs) estão vinculados. Essas configurações também podem ser definidas em máquinas individuais usando a Diretiva de Grupo Local.

A Diretiva de Grupo pode ser configurada localmente, tanto em ambientes de domínio quanto em ambientes fora do domínio. A Diretiva de Grupo Local pode ser usada para ajustar certas configurações gráficas e de rede que, de outra forma, não seriam acessíveis por meio do Painel de Controle. Ele também pode ser usado para bloquear uma política de computador individual com configurações de segurança rigorosas, como permitir apenas a instalação/execução de determinados programas ou impor requisitos estritos de senha de conta de usuário.

Podemos abrir o Editor de Diretiva de Grupo Local abrindo o menu Iniciar e digitando gpedit.msc. O editor é dividido em duas categorias em Diretiva de Computador Local - Configuração do Computador e Configuração do Usuário.

![image](https://academy.hackthebox.com/storage/modules/49/Local_GP.png)

Por exemplo, podemos abrir a Diretiva de computador local para ativar o Credential Guard ativando a configuração Ativar segurança baseada em virtualização. O Credential Guard é um recurso do Windows 10 que protege contra ataques de roubo de credenciais, isolando o processo LSA do sistema operacional.

![[Pasted image 20230525212648.png]]

Também podemos ativar a auditoria de conta ajustada e configurar o AppLocker no Editor de Diretiva de Grupo Local. Vale a pena explorar a Diretiva de Grupo Local e aprender sobre a ampla variedade de maneiras pelas quais ela pode ser usada para bloquear um sistema Windows.

## Windows Defender Antivirus

O Windows Defender Antivirus (Defender), anteriormente conhecido como Windows Defender, é um antivírus integrado fornecido gratuitamente com os sistemas operacionais Windows. Foi lançado pela primeira vez como uma ferramenta anti-spyware para download para Windows XP e Server 2003. O Defender começou a vir pré-embalado como parte do sistema operacional com Windows Vista/Server 2008. O programa foi renomeado para Windows Defender Antivirus com o Windows 10 Creators Update.

O Defender vem com vários recursos, como proteção em tempo real, que protege o dispositivo contra ameaças conhecidas em tempo real e proteção fornecida pela nuvem, que funciona em conjunto com o envio automático de amostras para carregar arquivos suspeitos para análise. Quando os arquivos são enviados para o serviço de proteção em nuvem, eles são "bloqueados" para evitar qualquer comportamento potencialmente malicioso até que a análise seja concluída. Outro recurso é a proteção contra adulterações, que impede que as configurações de segurança sejam alteradas por meio do registro, cmdlets do PowerShell ou política de grupo. 

O Windows Defender é gerenciado a partir da Central de Segurança, a partir da qual vários recursos e configurações de segurança adicionais podem ser habilitados e gerenciados.

![[Pasted image 20230525212812.png]]

As configurações de proteção em tempo real podem ser ajustadas para adicionar arquivos, pastas e áreas de memória para acesso controlado a pastas para evitar alterações não autorizadas. Também podemos adicionar arquivos ou pastas a uma lista de exclusão, para que não sejam verificados. Um exemplo seria excluir uma pasta de ferramentas usadas para teste de penetração da verificação, pois elas serão sinalizadas como maliciosas e colocadas em quarentena ou removidas do sistema. O acesso controlado à pasta é a proteção integrada do Ransomware do Defender.

Podemos usar o cmdlet Get-MpComputerStatus do PowerShell para verificar quais configurações de proteção estão habilitadas.

```powershell-session
PS C:\htb> Get-MpComputerStatus | findstr "True"
AMServiceEnabled                : True
AntispywareEnabled              : True
AntivirusEnabled                : True
BehaviorMonitorEnabled          : True
IoavProtectionEnabled           : True
IsTamperProtected               : True
NISEnabled                      : True
OnAccessProtectionEnabled       : True
RealTimeProtectionEnabled       : True
```

Embora nenhuma solução antivírus seja perfeita, o Windows Defender se sai muito bem em testes mensais de taxa de detecção em comparação com outras soluções, mesmo pagas. Além disso, como vem pré-instalado como parte do sistema operacional, não causa "inchaço" no sistema, como outros programas que adicionam extensões de navegador e rastreadores. Outros produtos são conhecidos por desacelerar o sistema devido à maneira como se conectam ao sistema operacional.

O Windows Defender tem suas falhas e deve fazer parte de uma estratégia de defesa profunda construída em torno dos princípios básicos de configuração e gerenciamento de patches, não tratado como uma bala de prata para proteger nossos sistemas. As definições são atualizadas constantemente e as novas versões do Windows Defender são incorporadas às principais versões operacionais, como o Windows 10, versão 1909, que é a versão mais recente no momento da redação.

O Windows Defender coleta cargas úteis de estruturas comuns de código aberto, como Metasploit, ou versões inalteradas de ferramentas, como Mimikatz.

![[Pasted image 20230525213026.png]]

Embora esteja se tornando cada vez mais difícil, ainda é possível contornar totalmente as proteções do Windows Defender impostas pela versão mais recente com as definições mais atualizadas instaladas.