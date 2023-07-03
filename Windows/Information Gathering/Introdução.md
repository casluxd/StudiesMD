Obter informações do sistema (também conhecido como host enumeration) pode parecer assustador no início. Porém, é uma etapa crucial para prover uma boa base do ambiente. Aprender e ter uma ideia geral sobre o ambiente é benéfico para tanto o red team, quanto o blue team.

Para o red team(penetration testers, red team operators, hackers, etc), encontrarão valor em poder escanear seus hosts e ambientes para saber quais serviços e máquinas vulneráveis podem ser explorados. Enquanto o blue team (administradores de sistema, analistas SOC, etc) pode usar as informações para diagnosticar problemas, proteger hosts e serviços e garantir a integridade para toda a rede. Independentemente da equipe em que possamos estar mais interessados ou atualmente envolvidos, esta seção visa fornecer as seguintes informações:

- Quais informações nós podemos obter do sistema (host)?
- Porque nós precisamos dessa informação e qual a importância da enumeração?
- Como obtemos essas informações por meio do prompt de comando e qual metodologia geral devemos seguir?


## Quais tipos de informações nós podemos obter do sistema?

Uma vez que obtemos o **acesso inicial** ao sistema por meio de algum command shell, saber por onde começar a pesquisar informações no sistema pode ser difícil. Enumeração manual do sistema sem nenhum caminho em mente em como nós podemos proceder, pode nos levar a perda de horas procurando por tesouros que parecem ser importantes e no fim obter pouco ou nenhum resultado de todo o tempo perdido. O objetivo do **host enumeration** é prover uma visão geral do sistema alvo, seu ambiente e como ele interage com outros sistemas através da rede.
Mantendo isso em mente, a primeira pergunta que podemos fazer é:

- Nós sabemos o que nós estamos procurando?
Para responder essa questão, nós temos que ter um entendimento básico de todos os tipos de informações disponíveis para nós em um sistema. Abaixo está um gráfico ao qual podemos fazer referência para nos fornecer um esboço generalizado dos principais tipos de informações das quais precisamos estar cientes ao realizar o **host enumeration**.

![[Pasted image 20230517232313.png]]

Como podemos ver no diagrama acima, os tipos de informações que procuraríamos podem ser divididos nas seguintes categorias:

- Generation System Information: Contém informações sobre o sistema de destino geral. As informações do sistema de destino incluem, entre outras, o nome do host da máquina, detalhes específicos do sistema operacional (nome, versão, configuração, etc.) e hotfixes/patches instalados no sistema.
- Networking Information: Contém informações de rede e conexão para o sistema de destino e sistema(s) aos quais o destino está conectado pela rede. Exemplos de informações de rede mas não limitados são: endereços de ip de hosts, interfaces de rede disponíveis, sub-redes acessíveis, servidores DNS, hosts conhecidos e recursos de rede.
- Basic Domain Information: Contém informações do Active Directory sobre o domínio ao qual o sistema de destino está conectado.
- User Information: Contém informações sobre usuários e grupos locais no sistema de destino. Isso normalmente pode ser expandido para conter qualquer coisa acessível a essas contas, como variáveis de ambiente, tarefas atualmente em execução, tarefas agendadas e serviços conhecidos. 

Embora esta não seja uma lista exaustiva de todas as informações de um sistema, isso nos fornecerá os meios para começar uma metodologia sólida para enumeração. Observando o diagrama com nosso novo conhecimento, podemos ver um padrão emergindo sobre o que devemos procurar ao realizar a enumeração em nosso host de destino. Para nos mantermos no alvo durante a enumeração, queremos tentar nos fazer algumas das seguintes perguntas:

- Quais informações do sistema podemos obter do nosso sistema alvo?
- Quais outro(s) sistema(s) nosso host de destino está interagindo na rede?
- A que conta(s) de usuário temos acesso e quais informações estão acessíveis a partir da(s) conta(s)?

Pense nessas perguntas como uma forma de fornecer estrutura para nos ajudar a desenvolver um senso de consciência situacional e uma metodologia para testar. Isso nos dá uma ideia mais clara do que estamos procurando e quais informações precisam ser filtradas ou priorizadas durante um envolvimento na vida real.

## Porque nós precisamos dessa informação?

Na seção anterior, discutimos quais informações podem ser coletadas de um sistema durante a enumeração e o que devemos estar ciente durante nossa pesquisa. Esta seção fornecerá mais do porquê por trás da coleta de informações em primeiro lugar e a importância da enumeração completa de um alvo.

Conforme declarado anteriomrente, nosso objetivo com a enumeração de host aqui é usar as informações obtidas do alvo para nos fornecer um ponto de partida e um guia de como desejamos atacar o sistema. Para obter uma melhor compreensão do conceito por trás da importância da enumeração adequada do host, vamos acompanhar o seguinte o exemplo:

`Exemplo: Imagine que você tenha a tarefa de trabalhar em um envolvimento de violação presumido e tenha acesso inicial por meio do que se supõe ser uma conta de usuário sem privilégio. Sua tarefa é obter uma visão geral do terreno e ver se você pode aumentar seus privilégios além do acesso inicial da conta de usuário comprometida.`

Seguindo o cenário de exemplo, podemos ver que recebemos acesso direto ao nosso host inicial por meio de uma conta de usuário não privilegiada. No entanto, nosso objetivo é eventualmente escalar nossos privilégios para uma conta com acesso a privilégios mais altos ou permissões administrativas, se tivermos sorte. Para fazer isso, vamos precisar de uma compreensão completa do nosso ambiente, incluindo o seguinte:

- A qual conta de usuário temos acesso?
- A quais grupos nosso usuário pertence?
- A qual conjunto de privilégios de trabalho atual nosso usuário tem acesso?
- Quais recursos nosso usuário pode acessar pela rede?
- Quais tarefas e serviços estão sendo executados em nossa conta de usuário?

Lembre-se de que isso abrange apenas parcialmente todas as perguntas que podemos fazer a nós mesmos para alcançar nosso objetivo pretendido, mas apenas um pequeno subconjunto de possibilidades. Sem pensar bem e sem seguir nenhuma estrutura guiada durante a enumeração, lutaremos para saber se temos todas as informações necessárias para atingir nosso objetivo. Pode ser fácil descartar um sistema como sendo completamente corrigido e não vulnerável a nenhum CVE atual ou às vulnerabilidades mais recentes. No entanto, se você se concentrar apenas nesse aspecto, é facil perder os muitos erros de configuração humana que podem existir no ambiente. Esta é a razão pela qual tomar nosso tempo e coletar todas as informações que pudermos em um sistema ou ambiente deve ser priorizado em termos de importância em relação à simples exploração de um sistema ao acaso.

## Como obtemos essa informação?

CMD provê um local para informações por meio do comando **systeminfo**. É excelente para encontrar informações relevantes sobre o host, como nome do host, endereço(s) IP, se pertence a um domínio, quais hotfixes foram instalados e muito mais.

Para um hacker, essa é uma ótima maneira de obter rapidamente a configuração do terreno quando você acessa um host pela primeira vez, deixando o mínimo de footprint. **Executar um comando é sempre melhor do que executar dois ou três apenas para obter as mesmas informações.** É menos provável que sejamos detectados dessa maneira. Ter acesso rápido a coisas como a versão do sistema operacional, hotfixes instalados e o OS build version pode nos ajudar a determinar rapidamente, a partir de uma pesquisa rápida no Google ou ExploitDB, se existe uma exploração que pode ser aproveitada rapidamente para explorar ainda mais esse host, elevar privilégios e muito mais.

- ### Examinando o sistema

Conforme mostrado anteriormente, **systeminfo** contém muitas informações para filtrar; no entanto, se precisarmos recuperar algumas informações básicas do sistema, como o nome do host ou versão do sistema operacional, nós podemos usar os utilitários **hostname** e **ver** integrados no prompt de comando.

O utilitário **hostname** segue seu homônimo e nos fornece o nome do host da máquina, enquanto o comando **ver** imprime o número da versão atual do sistema operacional. Ambos os comandos, em conjunto, nos fornecerá uma maneira alternativa de recuperar algumas informações básicas do sistema que podemos usar enquanto enumeramos ainda mais o host de destino.

- ### Escopo da rede

Além das informações de host fornecidas acima, vamos examinar rapidamente algumas informações básicas de rede para nosso alvo.
Uma compreensão completa de como nosso alvo está conectado e quais dispositivos ele pode acessar na rede é uma ferramenta inestimável em nosso arsenal como invasor. 

Para reunir essas informações rapidamente e em um comando simples de usar, o promp de comando oferece o utilitário **ipconfig**. 
O utilitário **ipconfig** exibe todas as configurações de rede TCP/IP atuais da máquina. 

#### ipconfig sem parâmetros

````cmd-session
C:\htb> ipconfig

Windows IP Configuration

<SNIP>

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : htb.local
   Link-local IPv6 Address . . . . . : fe80::2958:39a:df51:b60%23
   IPv4 Address. . . . . . . . . . . : 10.0.25.17
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.0.25.1

Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : internal.htb.local
   Link-local IPv6 Address . . . . . : fe80::bc3b:6f9f:68d4:3ec5%26
   IPv4 Address. . . . . . . . . . . : 172.16.50.15
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 172.16.50.1

<SNIP>
````

Como podemos ver no exemplo acima, mesmo sem especificar parâmetros, somos recebidos com algumas informações básicas de rede da máquina host, como nome de domínio, endereço IPv4, máscara de sub-rede e gateway padrão. Tudo isso pode fornecer informações sobre a(s) rede(s) das quais o alvo faz parte e está conectado e o ambiente mais amplo. Se precisarmos de informações adicionais ou quisermos nos aprofundar nas configurações aplicadas a cada adaptador, podemos usar o comando **ipconfig /all**.
Conforme implícito no sinalizador fornecido, este comando fornece uma listagem totalmente abrangente (configuração TCP/IP completa) de cada adaptador de rede conectado ao sistema e informações adicionais, incluindo o endereço físico de cada adaptador (endereço MAC), configurações DHCP e servidores DNS.

ipconfig é um comando altamente versátil para reunir informações sobre a conectividade de rede do host alvo. No entanto, se precisarmos ver rapidamente com quais hosts nosso alvo entrou em contato, basta usar o comando arp.

O utilitário **arp** exibe efetivamente o conteúdo e as entradas contidas no cache do Address Resolution Protocol (ARP). Também podemos usar este comando para modificar as entradas da tabela de forma eficaz.

- **Utilizando ARP para encontrar hosts adicionais**
````cmd-session
C:\htb> arp /a

<SNIP>

Interface: 10.0.25.17 --- 0x17
  Internet Address      Physical Address      Type
  10.0.25.1             00-e0-67-15-cf-43     dynamic
  10.0.25.5             54-9f-35-1c-3a-e2     dynamic
  10.0.25.10            00-0c-29-62-09-81     dynamic
  10.0.25.255           ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
  255.255.255.255       ff-ff-ff-ff-ff-ff     static

Interface: 172.16.50.15 --- 0x1a
  Internet Address      Physical Address      Type
  172.16.50.1           15-c0-6b-58-70-ed     dynamic
  172.16.50.20          80-e5-53-3c-72-30     dynamic
  172.16.50.32          fb-90-01-5c-1f-88     dynamic
  172.16.50.65          7a-49-56-10-3b-76     dynamic
  172.16.50.255         ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static\

<SNIP>
````

A partir deste exemplo, podemos ver todos os hosts que entraram em contato ou podem ter tido alguma comunicação anterior com nosso alvo. Podemos usar essas informações para começar a mapear a rede ao longo de cada uma das interfaces de rede pertencentes ao nosso alvo.

## Entendendo nosso usuário atual

Agora que temos algumas informações básicas do host para começar, devemos entender melhor nossa conta de usuário comprometida atual. Um dos melhores utilitários de prompt de comando para fazer isso é o **whoami**.

````cmd-session
C:\htb> whoami

ACADEMY-WIN11\htb-student
````

Como podemos ver na saída inicial acima, executar whoami sem parâmetros nos fornece o domínio atual e o nome de usuário da conta conectado.

Observação: se o usuário atual não for uma conta ingressada no domínio, o nome NetBIOS será fornecido. O nome do host atual será usado na maioria dos casos.

### Checando nosso privilégio

Conforme mencionado anteriormente, também podemos usar whoami para visualizar os privilégios de segurança do nosso usuário atual no sistema. Ao entender quais privilégios estão habilitados para nosso usuário atual, podemos determinar nossos recursos em nosso host alvo.

```cmd-session
C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

Pela saída acima, parece só temos acesso a um conjunto básico de permissões e a maioria de nossas opções estão desativadas. Isso está dentro das limitações de uma conta de usuário padrão provisionada no domínio. No entanto, se houver alguma configuração incorreta nessas configurações ou se o usuário tiver privilégios adicionais, poderíamos usar isso a nosso favor ao tentar aumentar os privilégios de nosso usuário atual.

### Investigando grupos

Além de ter uma compreensão completa dos privilégios de nosso usuário atual, também devemos dedicar algum tempo para ver de quais grupos nossa conta é membro. Isso pode fornecer informações sobre outros grupos dos quais nosso usuário atual faz parte, incluindo quais grupos padrão (integrados) e, mais importante, quaisquer grupos personalizados aos quais nosso usuário tenha acesso explicitamente concedido. Para visualizar os grupos dos quais nosso usuário atual faz parte, podemos emitir o seguinte comando: **whoami /groups**

```cmd-session
C:\htb> whoami /groups

GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Performance Log Users          Alias            S-1-5-32-559 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                          Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192
```

Nosso usuário não é membro de nenhum outro grupo além dos integrados adicionados à nossa conta na criação. No entanto, é essencial observar que, em alguns casos, os usuários podem receber acesso adicional, privilégios e permissões baseadas nos grupos dos quais fazem parte.

### Investigando outros usuários/grupos

Depois de investigar nossa atual conta de usuário comprometida, precisamos expandir um pouco e ver se podemos obter acesso a outras contas. Na maioria dos ambientes, as máquinas em uma rede são ingressadas no domínio. Devido à natureza das redes ingressadas no domínio, qualquer pessoa pode efetuar login em qualquer host físico na rede sem exigir uma conta local na máquina. Podemos usar isso a nosso favor, analisando quais usuários acessaram nosso host atual para ver se podemos acessar outras contas. Isso é muito benéfico como método de manutenção da persistência na rede. Para fazer isso, podemos utilizar a funcionalidade específica do comando **net**.

##### Net User

Net User nos permite exibir uma lista de todos usuários em um host, informações sobre um usuário específico e criar ou excluir usuários.

```cmd-session
C:\htb> net user

User accounts for \\ACADEMY-WIN11

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
htb-student              WDAGUtilityAccount
The command completed successfully.
```

A partir da saída fornecida, apenas algumas contas de usuários foram criadas para esta máquina. No entanto, se estivéssemos em uma rede mais populosa, poderíamos encontrar mais contas para comprometer.

#### Net Group/ Localgroup

Além das contas de usuário, também devemos dar uma olhada rápida em quais grupos existem na rede. Na seção anterior, discutimos muito sobre os grupos dos quais nosso usuário é membro. No entanto, também temos a capacidade de nosso host atual visualizar todos os grupos que existem em nosso host e do domínio. Podemos conseguir isso utilizando os comandos **net group** e **net localgroup**.

O **net group** exibirá todos os grupos existentes no host do qual emitimos o comando, criará e excluirá grupos e adicionará ou removerá usuários dos grupos. Ele também exibirá informações do grupo de domínio se o host ingressar no domínio. Lembre-se de que o **net group** deve ser executado em um servidor de domínio, como o DC, enquanto o **net localgroup** pode ser executado em qualquer host para nos mostrar os grupos que ele contém.

```cmd-session
C:\htb> net group
net group
This command can be used only on a Windows Domain Controller.

More help is available by typing NET HELPMSG 3515.


C:\htb>net localgroup

Aliases for \\ACADEMY-WIN11

-------------------------------------------------------------------------------
*__vmware__
*Access Control Assistance Operators
*Administrators
*Backup Operators
*Cryptographic Operators
*Device Owners
*Distributed COM Users
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Remote Desktop Users
*Remote Management Users
*Replicator
*System Managed Accounts Group
*Users
The command completed successfully.
```

### Explorando recursos na rede

Anteriormente, nos concentramos no que nosso usuário atual tem acesso em termos de host localmente. No entanto, em um ambiente de domínio, os usuários geralmente precisam armazenar qualquer material relacionado ao trabalho em um compartilhamento localizado na rede, em vez de armazenar arquivos localmente em suas máquinas. Esses compartilhamentos geralmente são encontrados em um servidor longe do acesso físico de um funcionário comum. Normalmente, os usuários padrão terão as permissões necessárias para ler, gravar e executar arquivos de um compartilhamento, desde que tenham credencias válidas. Podemos, é claro, abusar disso como um método de persistência adicional, mas como localizamos esse compartilhamentos em primeiro lugar?

#### Net share

Uma maneira de fazer isso envolve o uso do comando **net share**. Net share nos permite exibir informações sobre recursos compartilhados no host e também criar novos recursos compartilhados.

```cmd-session
C:\htb> net share  

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\Windows                      Remote Admin
Records      D:\Important-Files              Mounted share for records storage  
The command completed successfully.
```

Como podemos ver no exemplo acima, uma lista de compartilhamentos aos quais nosso usuário comprometido atual tem acesso. Ao ler, podemos adivinhar que **Records** é um compartilhamento montado manualmente que pode conter algumas informações potencialmente interessante para enumerarmos. Idealmente, se encontrássemos um compartilhamento aberto como este durante um engagement, precisariamos saber o seguinte:
- Temos as permissões adequadas para acessar este compartilhamento?
- Podemos ler, gravar e executar arquivos no compartilhamento?
- Existe algum dado valioso sobre o compartilhamento?

Além de fornecer informações, os compartilhamentos são ótimos para hospedar tudo o que precisamos e mover-nos lateralmente entre os hosts como um pentester. Se não estivermos muito preocupados em ser sorrateiros, podemos colocar um payload ou outros dados em um compartilhamento para permitir o movimento em torno de outros na rede. Embora fora do escopo deste módulo, abusar de compartilhamentos dessa maneira é um excelente método de persistência e pode ser usado para escalar privilégios.

#### Net view

Se não estivermos procurando compartilhamento explicitamente e quisermos fazer uma busca ampla no ambiente, temos um comando alternativo que pode ser extremamente útil, também conhecido como **net view**.

O **net view** exibirá para nós todos os recursos compartilhados que o host para o qual você está emitindo o comando conheça. Isso inclui recursos de domínio, compartilhamentos, impressoras e muito mais.

```cmd-session
C:\htb> net view
```

## Juntando as coisas

Usando todas as informações e exemplos fornecidos acima, extraímos uma quantidade significativa de informações de nosso host e seus arredores. A partir daqui, dependendo do nosso acesso, podemos elevar nossos privilégios ou continuar avançando em direção ao nosso objetivo. O acesso no nível do sistema em todos os hosts é desnecessário para um pentester (a menos que a avaliação o exija), portanto, evitamos ficar presos tentando obtê-lo em todas as ocasiões.

Esta é apenas uma visão rápida de como o CMD pode ser usado para obter acesso e continuar uma avaliação com recursos limitados. Tenha em mente que esta rota é bastante barulhenta e seremos notados eventualmente por até mesmo uma blue team semi-competente. Do jeito que está, estamos escrevendo toneladas de logs, deixando rastros em vários hosts e temos pouco ou nenhum insight sobre o que seu EDR ou NIDS foram capazes de ver.

Observação: em um ambiente padrão, o uso do prompt do cmd não é algo comum para um usuário comum. Às vezes, os administradores têm um motivo para usá-lo, mas suspeitam ativamente de qualquer usuário médio executando o cmd.exe. Com isso em mente, o uso de comandos net * em um ambiente também não é normal e pode ser uma maneira de alertar facilmente sobre uma possível infiltração de um host em rede. Com o monitoramento e registro adequados ativados, devemos detectar essas ações rapidamente e usá-las para triagem de um incidente antes que fique muito fora de controle.

## Consideraçõs finais

Embora esta seja uma seção incrivelmente longa, devemos ter uma noção geral do escopo geral das informações que podem ser encontradas em um sistema, por que precisamos delas e como podemos coletar essas informações de maneira rápida e eficiente. Como pentester, ter essa mentalidade nos permite aprofundar nossa metodologia e obter uma compreensão completa do que exatamente estamos procurando ao enumerar um sistema ou seu ambiente mais amplo. Ter uma metodologia sólida para enumeração é uma habilidade altamente inestimável para nós aprendermos desde o início.

À medida que avançamos para as próximas seções deste módulo, nossa mentalidade e metodologia permanecerão as mesmas, pois simplesmente estaremos construindo sobre a fundação que está sendo lançada.