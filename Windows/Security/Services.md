
Serviços são o componente principal do sistema operacional Windows. Eles permitem a criação e o gerenciamento de processos de longa duração. Serviços windows podem ser iniciados automaticamente no boot do sistema sem intervenção do usuário. Esses serviços continuam rodando no background mesmo após o usuário deslogar da sua conta no sistema.

Aplicações podem ser criadas para serem instaladas como serviço, como aplicação de monitoramento de rede instalada em um servidor. Serviços no windows são responsáveis por muitas funções, como funções de rede, diagnóstico de perfomance do sistema, gerenciamento de credenciais dos usuários, controle de atualizações do Windows e muito mais.

Serviços windows são gerenciados pelo sistema Service Control Manager(SCM), acessível através de `services.msc`.

Ele provê uma interface GUI para interação e gerenciamento dos serviços e mostra informações sobre cada serviço instalado. Essas informações incluem: Nome, Descrição, Status, Tipo de Inicialização e o usuário que iniciou o serviço.

Também é possível buscar e gerenciar serviços pela linha de comando usando o sc.exe através do Powershell usando como Get-Service.

Ex: `Get-Service | ? {$_.Status -eq "Running"} | Select -First 2 |fl`

Status de serviços podem aparecer como Running, Stopped ou Paused e eles podem ser setados para iniciarem manualmente, automaticamente ou em um delay após a inicialização do sistema. Serviços também podem estar nos estados Starting ou Stopping se alguma ação tiver sido triggada para iniciar ou parar.

Windows tem três categorias de serviços:

- **Local Services**

- **Network Services**

- **System Services**

Serviços podem ser usualmente apenas criado, modificado ou deletado por usuários que tem privilégios administrativos.

Misconfigurations sobre permissões de serviços é um vetor de ataque comum para privilege escalation em sistemas Windows.

No Windows, há alguns serviços de sistema críticos que não podem ser parados ou reiniciados sem a reinicialização do sistema. Se algum arquivo ou recurso que for usado por algum desses serviços for atualizado, é necessário reiniciar o sistema.

- **smss.exe:** Session Manager SubSystem. Responsável pelo manuseio de sessões no sistema.

- **crss.exe:** Client Server Runtime Process. A porção de modo usuário do subsistema Windows.

- **wininit.exe:** Inicia o arquivo Winit .init que lista todas as alterações feitas no Windows quando um computador é reiniciado após a instalação de um programa.

- **logonui.exe:** Usado para facilitar o login do usuário no PC.

- **lsass.exe:** Local Security Authentication Server verifica a validade dos logons dos usuários em um PC ou servidor. Gera o processo responsável por autenticar os usuários no serviço Winlogon.

- **services.exe:** Gerencia a operação de iniciar e parar serviços.

- **System:** Um processo do sistema em background que roda o kernel do Windows.

- **svchost.exe with RPCSS:** Gerencia os serviços do sistema que rodam com dynamic-link libraries(arquivos com extensão .dll) como "Automatic Updates", "Windows Firewall" e "Plug and Play". Usa o Remote Procedure Call(RPC) Service(RPCSS)

- **svchost.exe com Dcom/PnP:** Gerencia os serviços do sistema que rodam com dynamic-link libraries(arquivos com extensão .dll) como "Automatic Updates", "Windows Firewall" e "Plug and Play". Usa os serviços Distributed Component Object Model (DCOM) e Plug and Play (PnP).

# Service Permissions

Lembre-se de que os serviços permitem o gerenciamento de processos de execução longa e são uma parte crítica dos sistemas operacionais Windows. Sysadmins muitas vezes ignoram os potenciais vetores de ameaça que podem ser usados para carregar DLLs maliciosos, executar aplicações sem acesso a uma conta administrativa, escalar privilégios e até manter persistência. Esses vetores de ameaças em serviços Windows muitas vezes existem por causa de erros de configuração nas permissões do serviços implementado por software de terceiros e podem fácil de errar pelos admins durante o processo de instalação.

A primeira etapa da realização da importância de permissões de serviço é simplesmente entender que eles existem e estar atento a eles. Em sistemas operacionais de servidores, serviços críticos de rede como DHCP e Active Directory Domain Services geralmente é instalado usando a conta atríbuida a um administrador. Parte do processo de instalação inclui atribuir a serviços específicos rodarem usando credenciais e privilégios do usuário designado, que por padrão é definido no contexto do usuário conectado no momento.

Por exemplo, se um usuário está logado como Bob em um servidor durante a instalação do DHCP, então o serviço será configurado a rodar como Bob ao menos que seja especificado de outra forma. Que coisas ruins poderiam vir disso? Bom, se Bob deixar a empresa ou ser demitido? A prática comercial típica seria desativar a conta de Bob como parte de seu processo de saída. Nesse caso, o que vai acontecer ao DHCP e outros serviços rodando na conta do Bob? Os serviços começarão a falhar. DHCP ou Dynamic Host Configuration Protocol é usado para atribuir endereços IPs a computadores em uma rede. Se esse serviço para em um servidor DHCP Windows, então os clientes que pedem por um endereço IP não receberão um. Isso significa que um problema de configuração do serviço irá causar um tempo de inatividade e uma perda de produtividade. É altamente recomendável criar uma conta indivdual para rodar serviços críticos de rede. Eles são referidos como service accounts.

Devemos estar atentos às permissões de serviço e às permissões dos diretórios a partir dos quais eles executam, pois é possível substituir o caminho para um executável com uma DLL ou arquivo executável malicioso.

  

# Examinando serviços usando services.msc

Como falado anteriormente, o serviço `services.msc` pode ser usado para visualizar e gerenciar cada detalhe sobre todos os serviços.

Tenha noção das diferentes propriedades que estão disponíveis para visualização e configuração. Sabendo o nome do serviço é especialmente útil quando usando ferramentas de linhas de comando para examinar e gerenciar serviços. Caminho para o executável é o caminho completo para o programa e o comando a executar quando um serviço inicia. Se as permissões NTFS do diretório de destino estão configuradas com permissões fracas, um atacante pode trocar o executável original com um criado com propósito malicioso.

A maioria dos serviços rodam com privilégios de LocalSystem, o qual é o maior level de acesso permitido para um sistema operacional individual. Nem todas as aplicações precisam de permissões de nível LocalSystem, então é benéfico performar pesquisas em caso a caso quando considerar a instalação de uma nova aplicação em um ambiente Windows. É uma boa prática identificar aplicações que rodam com o mínimo privilégio possível para alinhar com o princípio do least privilege.

## Notáveis built-in service accounts no Windows:

- **LocalService**

- **NetworkService**

- **LocalSystem**
  
Nós podemos criar novas contas e usa-las com o propósito único de rodar um serviço.

A aba de recuperação permite etapas a serem configuradas quando um serviço falha. Uma das opções é rodar um programa após a falha, o que pode ser um vetor que um atacante pode usar para rodar programas maliciosos usando um serviço legítimo.

  

# Examinando serviços usando sc

`sc` pode ser usado para configurar e gerenciar serviços.

`sc qc` é usado para pesquisar um serviço. Aqui é onde saber o nome dos serviços se torna útil. Se nós queremos pesquisar um serviço em um dispositivo na rede, nós podemos especificar o hostname ou endereço IP imediatamente após sc: `sc //hostname ou ip// ServiceName`

`sc` também pode ser usado para parar e iniciar serviços: `sc stop ServiceName`. Caso esse comando seja executando sem estar em um contexto administrativo, haverá o acesso negado. Se nós rodarmos um comando com privilégios elevados, essa ação será completada: `sc config ServiceName binPath=C:\Windows\Perfectlegitprogram.exe`

Se nós estamos investigando uma situação onde suspeitamos que o sistema tem um malware, sc nos dá a possibilidade de rapidamente pesquisar e analisar serviços comumentes alvos e serviços criados recentemente. É muito mais script-friendly do que ferramentas GUI como `services.msc`.

Outra maneira útil de examinar permissões de um serviço é usar o `sc` juntamente com o `sdshow`: `sc sdshow ServiceName`

A saída pode parecer meio louca. Parecerá que a gente fez algo errado no comando, mas há um significado para essa saída. Cada named object(objetos do kernel do Windows) no Windows é um `securable object(objetos que possuem permissões de segurança)` e até mesmo alguns `unnamed objects` são protegidos. Se é protegido em um dispositivo Windows, haverá um `security descriptors`. Security descriptors identifica o `object's owner` e o primary group contendo um `Discretionary Access Control List(DACL)` e um `System Access Control List(SACL)`.

Geralmente, um DACL é usado para controlar o acesso a um objeto e um SACL é usado para contabilizar e registrar tentativas de acesso.

`D:(A;;CCLCSWRPLORC;;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)`

Essa loucura de caracteres juntos e delimitado por parenteses abertos e fechados é um formato conhecido como `Security Descriptor Definition Language(SDDL)`

Nós talvez nos sentimos tentados a ler da esquerda para a direita, porque é a ordem normal de leitura, mas é um pouco diferente quando interagimos com o computador. Ler o `security descriptor` inteiro para o serviço começa na primeira letra e o conjunto de parentêses.

**D:(A;;CCLCSWRPLORC;;;;AU)**

1. D - Os caracteres anteriores são permissões DACL

2. AU - Define o `security principal` como Authenticated Users

3. A;; - Acesso é permitido

4. CC - SERVICE_QUERY_CONFIG é o nome completo e é uma consulta ao service control manager(SCM) para a configuração do serviço

5. LC - SERVICE_QUERY_STATUS é o nome completo e é uma consulta ao service control manager(SCM) para o atual status do serviço.

6. SW - SERVICE_ENUMERATE_DEPENDENTS é o nome completo e enumera uma lista de serviços dependentes.

7. RP - SERVICE_START é o nome completo e inicia um serviço

8. LO - SERVICE_INTERROGATE é o nome completo, e realiza a query do serviço para seu status atual.

9. RC - READ_CONTROL é o nome completo e realiza a query pelo security descriptor do serviço.

Conform lemos o security descriptor, é fácil se perder, mas lembre-se que nós estamos essencialmente visualizando o access control entries de uma access control list.

`;;CCLCSWRPLORC;;;`

Cada conjunto de 2 caracteres entre os semi-colons representa uma ação permitida a ser performada por um usuário específico ou grupo.

`;;;AU`

Após os último conjunto do semi-colons, os caracteres especificam o security principal(User and/or Group) que é permitido a performar essas ações.

``A;;`

O caracter imediamente após a abertura dos parenteses e antes do primeiro conjunto de semi-colons define qque as ações são Allowed ou Denied.

# Examinando permissões de serviços usando o PowerShell

Usando o `Get-Acl`, nós podemos examinar as permissões do serviços mirando o caminho do serviço específico no registro.

Esse comando retorna as permissões das contas em formato easy-to-read e também em SDDL. Também, o SID representa cada security principal (User and/or Group) presente no SDDL. Isso é algo que não obtemos usando o comando `sc`.

Saber como interagir com os serviços e suas permissões associadas via linha de comando faz mais simples realizar scripts dessas taks. Enquanto é bom saber como performar essas tasks usando a GUI, não escala quando começamos aentrar em ambientes de domínio de rede maiores.