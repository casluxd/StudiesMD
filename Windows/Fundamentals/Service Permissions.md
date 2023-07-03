
Lembre-se de que os serviços permitem o gerenciamento de processos de longa duração e são uma parte crítica dos sistemas operacionais Windows. Os administradores de sistema geralmente os ignoram como possíveis vetores de ameaças que podem ser usados ​​para carregar DLLs maliciosos, executar aplicativos sem acesso a uma conta de administrador, escalar privilégios e até mesmo manter a persistência. Esses vetores de ameaças nos serviços do Windows geralmente surgem por meio de configurações incorretas de permissões de serviço implementadas por software de terceiros e fáceis de cometer erros por administradores durante os processos de instalação.

O primeiro passo para perceber a importância das permissões de serviço é simplesmente entender que elas existem e estar atento a elas. Em sistemas operacionais de servidor, serviços de rede críticos, como DHCP e Active Directory Domain Services, geralmente são instalados usando a conta atribuída ao administrador que executa a instalação. Parte do processo de instalação inclui atribuir um serviço específico para ser executado usando as credenciais e privilégios de um usuário designado, que por padrão é definido no contexto do usuário conectado no momento.

Por exemplo, se estivermos conectados como Bob em um servidor durante a instalação do DHCP, esse serviço será configurado para ser executado como Bob, a menos que especificado de outra forma. Que coisas ruins podem resultar disso? Bem, e se Bob deixar a organização ou for demitido? A prática comercial típica seria desabilitar a conta de Bob como parte de seu processo de saída. Nesse caso, o que aconteceria com o DHCP e outros serviços executados usando a conta de Bob? Esses serviços falhariam ao iniciar. DHCP ou Dynamic Host Configuration Protocol é responsável pela concessão de endereços IP para computadores na rede. Se esse serviço parar em um servidor DHCP do Windows, os clientes que solicitarem um endereço IP não receberão um. Isso significa que uma configuração incorreta do serviço pode resultar em tempo de inatividade e perda de produtividade. É altamente recomendável criar uma conta de usuário individual para executar serviços de rede críticos. Elas são chamadas de contas de serviço.

Também devemos estar atentos às permissões de serviço e às permissões dos diretórios de onde eles são executados, pois é possível substituir o caminho para um executável por uma DLL ou arquivo executável malicioso. Vamos examinar as permissões dos serviços em execução no Windows 10 para entender melhor isso.

# Examinando serviços usando services.msc

Conforme discutido na seção de processos e serviços, podemos usar services.msc para visualizar e gerenciar quase todos os detalhes relacionados a todos os serviços. Vamos dar uma olhada no serviço associado ao Windows Update (wuauserv).

![services.msc](https://academy.hackthebox.com/storage/modules/49/service_properties.png)

Anote as diferentes propriedades disponíveis para visualização e configuração. Saber o nome do serviço é especialmente útil ao usar ferramentas de linha de comando para examinar e gerenciar serviços. O caminho para o executável é o caminho completo para o programa e o comando a serem executados quando o serviço for iniciado. Se as permissões NTFS do diretório de destino estiverem configuradas com permissões fracas, um invasor pode substituir o executável original por um criado para fins maliciosos.


![Log On tab](https://academy.hackthebox.com/storage/modules/49/logon.png)

A maioria dos serviços é executada com privilégios LocalSystem por padrão, que é o nível mais alto de acesso permitido em um sistema operacional Windows individual. Nem todos os aplicativos precisam de permissões de nível de conta do Local System, portanto, é útil realizar pesquisas caso a caso ao considerar a instalação de novos aplicativos em um ambiente Windows. É uma boa prática identificar aplicativos que podem ser executados com o mínimo de privilégios possível para alinhar com o princípio do menor privilégio(https://www.cloudflare.com/pt-br/learning/access-management/principle-of-least-privilege/).

Contas de serviço integradas notáveis ​​no Windows:
- LocalService
- NetworkService
- LocalSystem

Observação: também podemos criar novas contas e usá-las com o único objetivo de executar um serviço.

![Recovery tab](https://academy.hackthebox.com/storage/modules/49/recovery_screen.png)

A guia de recuperação permite que as etapas sejam configuradas caso um serviço falhe. Observe como esse serviço pode ser configurado para executar um programa após a primeira falha. Este é mais um vetor que um invasor pode usar para executar programas maliciosos utilizando um serviço legítimo.

# Examinando serviços usando sc

Sc também pode ser usado para configurar e gerenciar serviços. Vamos experimentar alguns comandos.

```cmd-session
C:\Users\htb-student>sc qc wuauserv
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\system32\svchost.exe -k netsvcs -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

O comando sc qc é usado para consultar o serviço. É aqui que saber os nomes dos serviços pode ser útil. Se quiséssemos consultar um serviço em um dispositivo na rede, poderíamos especificar o nome do host ou endereço IP imediatamente após sc.

```cmd-session
C:\Users\htb-student>sc //hostname or ip of box query ServiceName
```

Também podemos usar sc para iniciar e parar serviços.

```cmd-session
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```

Observe como nos é negado o acesso para executar esta ação sem executá-la em um contexto administrativo. Se executarmos um prompt de comando com privilégios elevados, teremos permissão para concluir esta ação.

```cmd-session
C:\WINDOWS\system32> sc config wuauserv binPath=C:\Winbows\Perfectlylegitprogram.exe

[SC] ChangeServiceConfig SUCCESS

C:\WINDOWS\system32> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Winbows\Perfectlylegitprogram.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

Se estivéssemos investigando uma situação em que suspeitávamos que o sistema tinha malware, o sc nos daria a capacidade de pesquisar e analisar rapidamente serviços direcionados comumente e serviços recém-criados. Também é muito mais amigável ao script do que utilizar ferramentas GUI como services.msc.

Outra maneira útil de examinar as permissões de serviço usando sc é por meio do comando sdshow.

```cmd-session
C:\WINDOWS\system32> sc sdshow wuauserv

D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)S:(AU;FA;CCDCLCSWRPWPDTLOSDRCWDWO;;;WD)
```

À primeira vista, a saída parece louca. Quase parece que fizemos algo errado em nosso comando, mas há um significado para essa loucura. Cada objeto nomeado no Windows é um securable object e até mesmo alguns objects não nomeados são securables. Se puder ser protegido em um sistema operacional Windows, ele terá um descritor de segurança. Os descritores de segurança identificam o proprietário do objeto e um grupo primário contendo uma lista de controle de acesso discricionário (DACL) e uma lista de controle de acesso do sistema (SACL).

Geralmente, uma DACL é usada para controlar o acesso a um objeto e uma SACL é usada para contabilizar e registrar tentativas de acesso. Esta seção examinará a DACL, mas os mesmos conceitos se aplicariam a uma SACL.

````
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
````

Esse amálgama de caracteres compactados e delimitados por parênteses abertos e fechados está em um formato conhecido como SDDL (Security Descriptor Definition Language).

Podemos ficar tentados a ler da esquerda para a direita porque é assim que o idioma português é tipicamente escrito, mas pode ser muito diferente ao interagir com computadores. Leia todo o descritor de segurança para o serviço Windows Update (wuauserv) nesta ordem, começando com a primeira letra e um par de parênteses:

`D: (A;;CCLCSWRPLORC;;;AU)`

- D: - os caracteres anteriores são permissões DACL
- AU: - define o principal de segurança Usuários autenticados
- A;; - o acesso é permitido
- CC - SERVICE_QUERY_CONFIG é o nome completo e é uma consulta ao gerenciador de controle de serviço (SCM) para a configuração do serviço
- LC - SERVICE_QUERY_STATUS é o nome completo e é uma consulta ao gerenciador de controle de serviço (SCM) para o status atual do serviço
- SW - SERVICE_ENUMERATE_DEPENDENTS é o nome completo e enumerará uma lista de serviços dependentes
- RP - SERVICE_START é o nome completo, e vai iniciar o serviço
- LO - SERVICE_INTERROGATE é o nome completo e consultará o serviço para seu status atual
- RC - READ_CONTROL é o nome completo, e irá consultar o descritor de segurança do serviço

À medida que lemos o descritor de segurança, pode ser fácil se perder na ordem aparentemente aleatória dos caracteres, mas lembre-se de que estamos essencialmente visualizando entradas de controle de acesso em uma lista de controle de acesso. Cada conjunto de 2 caracteres entre os pontos e vírgulas representa ações que podem ser executadas por um usuário ou grupo específico.

`;;CCLCSWRPLORC;;;`

Após o último conjunto de ponto e vírgula, os caracteres especificam a entidade de segurança (usuário e/ou grupo) que tem permissão para executar essas ações.

``;;;AU``

O caractere imediatamente após os parênteses de abertura e antes do primeiro conjunto de ponto e vírgula define se as ações são permitidas ou negadas.

``A;;``

Todo esse descritor de segurança associado ao serviço Windows Update (wuauserv) tem três conjuntos de entradas de controle de acesso porque existem três entidades de segurança diferentes. Cada entidade de segurança tem permissões específicas aplicadas.

## Examine as permissões de serviço usando o PowerShell

Usando o cmdlet Get-Acl do PowerShell, podemos examinar as permissões de serviço direcionando o caminho de um serviço específico no registro.

```powershell-session
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List

Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\wuauserv
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Users Allow  -2147483648
         BUILTIN\Administrators Allow  FullControl
         BUILTIN\Administrators Allow  268435456
         NT AUTHORITY\SYSTEM Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  268435456
         CREATOR OWNER Allow  268435456
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  ReadKey
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  -2147483648
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         ReadKey
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         -2147483648
Audit  :
Sddl   : O:SYG:SYD:AI(A;ID;KR;;;BU)(A;CIIOID;GR;;;BU)(A;ID;KA;;;BA)(A;CIIOID;GA;;;BA)(A;ID;KA;;;SY)(A;CIIOID;GA;;;SY)(A
         ;CIIOID;GA;;;CO)(A;ID;KR;;;AC)(A;CIIOID;GR;;;AC)(A;ID;KR;;;S-1-15-3-1024-1065365936-1281604716-3511738428-1654
         721687-432734479-3232135806-4053264122-3456934681)(A;CIIOID;GR;;;S-1-15-3-1024-1065365936-1281604716-351173842
         8-1654721687-432734479-3232135806-4053264122-3456934681)
```

Observe como esse comando retorna permissões de conta específicas em um formato fácil de ler e em SDDL. Além disso, o SID que representa cada entidade de segurança (Usuário e/ou Grupo) está presente no SDDL. Isso é algo que não obtemos ao executar sc no prompt de comando.

Saber como interagir com os serviços e suas permissões associadas na linha de comando facilita o script dessas tarefas. Embora seja bom saber como executar essas tarefas a partir da GUI, ela não escala bem quando começamos a entrar em ambientes de rede e domínios maiores.
