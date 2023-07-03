
À medida que avançamos neste módulo, examinamos os muitos recursos e funcionalidades incorporados ao Active Directory. Todos construídos com base na premissa de gerenciamento central e na capacidade de compartilhar informações rapidamente, à vontade, com uma grande base de usuários. O Active Directory pode ser considerado inseguro por design por causa disso. Uma instalação padrão do Active Directory não terá muitas medidas de proteção, configurações e ferramentas que podem ser usadas para proteger uma implementação do AD. Quando pensamos em segurança cibernética, uma das primeiras coisas que surgem é o equilíbrio entre Confidencialidade, Integridade e Disponibilidade, também conhecido como Tríade CIA(https://www.f5.com/labs/learning-center/what-is-the-cia-triad). Encontrar esse equilíbrio é difícil, e o AD se inclina fortemente para a Disponibilidade e a Confidencialidade em sua essência.

![[Pasted image 20230604230740.png]]

Podemos ajudar a equilibrar as escalas utilizando os recursos integrados da Microsoft que podem ser ativados/ajustados para proteger o AD contra ataques comuns. A lista abaixo não é exaustiva. Muitos outros princípios gerais de fortalecimento da segurança devem estar em vigor dentro de uma organização para garantir uma abordagem de defesa em profundidade adequada (ter um inventário de ativos preciso, patches de vulnerabilidade, proteção de endpoint, treinamento de conscientização de segurança, segmentação de rede, etc.). Esta seção pode ser considerada o mínimo das práticas recomendadas gerais de segurança do AD das quais qualquer organização se beneficiará. Iremos nos aprofundar no Active Directory Defense em um módulo posterior. Vamos nos aprofundar e começar com algumas medidas gerais de proteção para AD.

## General Active Directory Hardening Measures

O Microsoft Local Administrator Password Solution (LAPS) (https://www.microsoft.com/en-us/download/details.aspx?id=46899) é usado para randomizar e alternar as senhas do administrador local em hosts do Windows e impedir o movimento lateral.

#### LAPS

As contas podem ser configuradas para que suas senhas sejam alternadas em um intervalo fixo (ou seja, 12 horas, 24 horas, etc.). Essa ferramenta gratuita pode ser benéfica para reduzir o impacto de um host individual comprometido em um ambiente de AD. As organizações não devem confiar apenas em ferramentas como esta. Ainda assim, quando combinado com outras medidas de proteção e práticas recomendadas de segurança, pode ser uma ferramenta muito eficaz para o gerenciamento de senhas de contas de administrador local.

#### Audit Policy Settings (Logging and Monitoring)

Toda organização precisa ter configuração de registro e monitoramento para detectar e reagir a mudanças ou atividades inesperadas que possam indicar um ataque. O registro e o monitoramento eficazes podem ser usados ​​para detectar um invasor ou funcionário não autorizado adicionando um usuário ou computador, modificando um objeto no AD, alterando a senha de uma conta, acessando um sistema de maneira não autorizada ou fora do padrão, realizando um ataque como a pulverização de senha ou ataques mais avançados, como os ataques Kerberos modernos.

#### Group Policy Security Settings

Conforme mencionado anteriormente no módulo, os Objetos de Diretiva de Grupo (GPOs) são coleções virtuais de configurações de diretiva que podem ser aplicadas a usuários, grupos e computadores específicos no nível da UO. Eles podem ser usados ​​para aplicar uma ampla variedade de políticas de segurança para ajudar a proteger o Active Directory. A seguir está uma lista não exaustiva dos tipos de políticas de segurança que podem ser aplicadas:
- Account Policies: Gerencia como as contas de usuário interagem com o domínio. Isso inclui a política de senha, a política de bloqueio de conta e as configurações relacionadas ao Kerberos, como o tempo de vida dos tíquetes do Kerberos.
- Local Policies: Eles se aplicam a um computador específico e incluem a política de auditoria de eventos de segurança, atribuições de direitos de usuário (privilégios de usuário em um host) e configurações de segurança específicas, como a capacidade de instalar drivers, se as contas de administrador e convidado estão habilitadas, renomeando as contas de convidado e administrador, evitando que os usuários instalem impressoras ou usem mídia removível e uma variedade de acesso à rede e controles de segurança de rede.
- Software Restriction Policies: Configurações para controlar qual software pode ser executado em um host.
- Application Control Policies: Configurações para controlar quais aplicativos podem ser executados por determinados usuários/grupos. Isso pode incluir o bloqueio de certos usuários de executar todos os executáveis, arquivos do Windows Installer, scripts, etc. Os administradores usam o AppLocker para restringir o acesso a determinados tipos de aplicativos e arquivos. Não é incomum ver organizações bloqueando o acesso ao CMD e PowerShell (entre outros executáveis) para usuários que não precisam deles para o trabalho do dia-a-dia. Essas políticas são imperfeitas e muitas vezes podem ser contornadas, mas necessárias para uma estratégia de defesa em profundidade.
- Advanced Audit Policy Configuration: Uma variedade de configurações que podem ser ajustadas para atividades de auditoria, como acesso ou modificação de arquivos, logon/logoff de conta, alterações de política, uso de privilégios e muito mais.

![[Pasted image 20230604235411.png]]

#### Update Management (SCCM/WSUS)

O gerenciamento adequado de patches é crítico para qualquer organização, especialmente aquelas que executam sistemas Windows/Active Directory. O Windows Server Update Service (WSUS) pode ser instalado como uma função em um Windows Server e pode ser usado para minimizar a tarefa manual de corrigir sistemas Windows. O System Center Configuration Manager (SCCM) é uma solução paga que depende da instalação da função WSUS Windows Server e oferece mais recursos do que o WSUS por conta própria. Uma solução de gerenciamento de patches pode ajudar a garantir a implantação oportuna de patches e maximizar a cobertura, garantindo que nenhum host perca patches de segurança críticos. Se uma organização depende de um método manual para aplicar patches, isso pode levar muito tempo, dependendo do tamanho do ambiente, e também pode resultar na perda de sistemas e deixá-los vulneráveis.

#### Group Managed Service Accounts (gMSA)

Um gMSA é uma conta gerenciada pelo domínio que oferece um nível de segurança mais alto do que outros tipos de contas de serviço para uso com aplicativos não interativos, serviços, processos e tarefas que são executados automaticamente, mas requerem credenciais para serem executados. Eles fornecem gerenciamento automático de senha com uma senha de 120 caracteres gerada pelo controlador de domínio. A senha é alterada em intervalos regulares e não precisa ser conhecida por nenhum usuário. Ele permite que as credenciais sejam usadas em vários hosts.

#### Security Groups

Os grupos de segurança oferecem uma maneira fácil de atribuir acesso aos recursos de rede. Eles podem ser usados ​​para atribuir direitos específicos ao grupo (em vez de diretamente ao usuário) para determinar o que os membros do grupo podem fazer no ambiente do AD. O Active Directory cria automaticamente alguns grupos de segurança padrão durante a instalação. Alguns exemplos são operadores de conta, administradores, operadores de backup, administradores de domínio e usuários de domínio. Esses grupos também podem ser usados ​​para atribuir permissão para acessar recursos (ou seja, um compartilhamento de arquivo, pasta, impressora ou um documento). Os grupos de segurança ajudam a garantir que você possa atribuir permissões granulares a usuários em massa, em vez de gerenciar individualmente cada usuário.

![[Pasted image 20230605000959.png]]

#### Account Separation

Os administradores devem ter duas contas separadas. Um para o trabalho do dia-a-dia e outro para as tarefas administrativas que devem realizar. Por exemplo, um usuário pode fazer login em sua máquina usando sua conta sjones para enviar/receber um e-mail, criar documentos, etc. Eles devem ter uma conta separada, como sjones_adm, para acessar um host administrativo seguro usado para executar tarefas administrativas. Isso pode ajudar a garantir que, se o host de um usuário for comprometido (através de um ataque de phishing, por exemplo), o invasor ficaria limitado a esse host e não obteria credenciais para um usuário altamente privilegiado com acesso considerável dentro do domínio. Também é essencial que o indivíduo use senhas diferentes para cada conta para reduzir o risco de ataques de reutilização de senha se sua conta não administrativa for comprometida.

#### Password Complexity Policies + Passphares + 2FA

Idealmente, uma organização deve usar frases secretas ou grandes senhas geradas aleatoriamente usando um gerenciador de senhas corporativo. As senhas padrão de 7 a 8 caracteres podem ser quebradas offline usando uma ferramenta como Hashcat muito rapidamente com um equipamento de quebra de senha GPU. Senhas mais curtas e menos complexas também podem ser adivinhadas por meio de um ataque de pulverização de senha, dando ao invasor uma posição segura no domínio. As regras de complexidade de senha sozinhas no AD não são suficientes para garantir senhas fortes. Por exemplo, a senha Welcome1 atenderia às regras de complexidade padrão (3 de 4 letras maiúsculas, minúsculas, números e caracteres especiais), mas seria uma das primeiras senhas que eu tentaria em um ataque de pulverização de senha. Uma organização também deve considerar a implementação de um filtro de senha para proibir senhas que contenham os meses ou estações do ano, o nome da empresa e palavras comuns, como `password` e `welcome`.  O comprimento mínimo da senha para usuários padrão deve ser de pelo menos 12 caracteres e, idealmente, maior para administradores/contas de serviço. Outra importante medida de segurança é a implementação da autenticação multifator (MFA) para Acesso à Área de Trabalho Remota a qualquer host. Isso pode ajudar a limitar as tentativas de movimento lateral que podem depender do acesso da GUI a um host.

#### Limiting Domain Admin Account Usage

As poderosas contas de administrador de domínio devem ser usadas apenas para fazer login em controladores de domínio, não em estações de trabalho pessoais, hosts de salto, servidores da Web, etc. Isso pode reduzir significativamente o impacto de um ataque e reduzir possíveis caminhos de ataque caso um host seja comprometido. Isso garantiria que as senhas da conta Admin do domínio não fossem deixadas na memória dos hosts em todo o ambiente.

#### Periodically Auditing and Removing Stale Users and Objects

É importante que uma organização audite periodicamente o Active Directory e remova ou desabilite todas as contas não utilizadas. Por exemplo, pode haver uma conta de serviço privilegiada que foi criada há oito anos com uma senha muito fraca que nunca foi alterada e a conta não está mais em uso. Mesmo que a política de senhas tenha sido alterada para ser mais resistente a ataques como a pulverização de senhas, uma conta como essa pode ser um ponto de apoio rápido e fácil ou um método para movimentação lateral ou escalonamento de privilégios dentro do domínio.

#### Auditing Permissions and Access

As organizações também devem realizar periodicamente auditorias de controle de acesso para garantir que os usuários tenham apenas o nível de acesso necessário para seu trabalho diário. É importante auditar os direitos de administrador local, o número de administradores de domínio (realmente precisamos de 30 deles?), e os administradores corporativos para limitar a superfície de ataque, acesso ao compartilhamento de arquivos, direitos do usuário (ou seja, participação em determinados grupos de segurança privilegiados) e muito mais.

#### Audit Policies & Logging

A visibilidade no domínio é obrigatória. Uma organização pode conseguir isso por meio de registro robusto e, em seguida, usando regras para detectar atividades anômalas (como muitas tentativas de login com falha que podem ser indicativas de um ataque de pulverização de senha) ou indicadores de que um ataque Kerberoasting está sendo tentado. Eles também podem ser usados ​​para detectar a enumeração do Active Directory. Vale a pena nos familiarizarmos com as recomendações de política de auditoria da Microsoft(https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations) para ajudar a detectar o comprometimento.

#### Using Restricted Groups

Os Grupos Restritos permitem que os administradores configurem a participação no grupo por meio da Diretiva de Grupo. Eles podem ser usados ​​por vários motivos, como controlar a associação no grupo do administrador local em todos os hosts no domínio, restringindo-o apenas à conta do administrador local e aos administradores de domínio e controlando a associação nos grupos altamente privilegiados de Administradores Corporativos e Administradores de Esquemas e outros grupos administrativos importantes.

#### Limiting Server Roles

É importante não instalar funções adicionais em hosts confidenciais, como instalar a função do Internet Information Server (IIS) em um controlador de domínio. Isso aumentaria a superfície de ataque do controlador de domínio, e esse tipo de função deve ser instalado em um servidor da Web autônomo separado. Alguns outros exemplos seriam não hospedar aplicativos da Web em um servidor de correio do Exchange e separar servidores da Web e servidores de banco de dados em hosts diferentes. Esse tipo de separação de função pode ajudar a reduzir o impacto de um ataque bem-sucedido.

#### Limiting Local Admin and RDP Rights

As organizações devem controlar rigorosamente quais usuários têm direitos de administrador local em quais computadores. Conforme declarado acima, isso pode ser feito usando Grupos Restritos. Já vi muitas organizações com todo o grupo de usuários do domínio com direitos de administrador local em um ou mais hosts. Isso permitiria que um invasor comprometesse QUALQUER conta (mesmo com privilégios muito baixos) para acessar esse host como um administrador local e possivelmente obter dados confidenciais ou roubar credenciais de conta de domínio com privilégios elevados da memória se outro usuário estiver conectado. O mesmo vale para os direitos de Área de Trabalho Remota (RDP). Se muitos usuários puderem fazer o RDP para uma ou várias máquinas, isso aumentará o risco de exposição de dados confidenciais ou possíveis ataques de escalonamento de privilégios, levando a um maior comprometimento.

Este link(https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory) fornece leitura adicional sobre as práticas recomendadas da Microsoft para proteger o Active Directory.

# Examining Group Policy

A Diretiva de Grupo é um recurso do Windows que fornece aos administradores uma ampla variedade de configurações avançadas que podem ser aplicadas a contas de usuário e de computador em um ambiente Windows. Cada host do Windows possui um editor de Diretiva de Grupo Local para gerenciar as configurações locais. Para nossos propósitos, vamos nos concentrar na Diretiva de Grupo em um contexto de domínio para gerenciar usuários e computadores no Active Directory. A Diretiva de Grupo é uma ferramenta poderosa para gerenciar e definir configurações de usuário, sistemas operacionais e aplicativos. A Diretiva de Grupo também é uma ferramenta potente para gerenciar a segurança em um ambiente de domínio. De um contexto de segurança, alavancar a Diretiva de Grupo é uma das melhores maneiras de afetar amplamente a postura de segurança da sua empresa. O Active Directory não é de forma alguma seguro "fora da caixa" e a Diretiva de Grupo, quando usada corretamente, é uma parte crucial de uma estratégia de defesa em profundidade.

Embora a Diretiva de Grupo seja uma excelente ferramenta para gerenciar a segurança de um domínio, ela também pode ser abusada por invasores. A obtenção de direitos sobre um objeto de diretiva de grupo pode levar a movimentação lateral, escalonamento de privilégios e até comprometimento total do domínio se o invasor puder aproveitá-los de forma a assumir o controle de um usuário ou computador de alto valor. Eles também podem ser usados ​​como uma forma de um invasor manter a persistência dentro de uma rede. Entender como a Diretiva de Grupo funciona nos dará uma vantagem contra os invasores e pode nos ajudar muito em testes de penetração, às vezes encontra configurações incorretas sutis que outros testadores de penetração podem perder.

## Group Policy Objects (GPOs)

Um objeto de política de grupo (GPO) é uma coleção virtual de configurações de política que podem ser aplicadas a usuários ou computadores. Os GPOs incluem políticas como tempo limite de bloqueio de tela, desativação de portas USB, imposição de uma política de senha de domínio personalizada, instalação de software, gerenciamento de aplicativos, personalização de configurações de acesso remoto e muito mais. Cada GPO tem um nome exclusivo e recebe um identificador exclusivo (um GUID). Eles podem ser vinculados a uma UO, domínio ou site específico. Um único GPO pode ser vinculado a vários contêineres e qualquer contêiner pode ter vários GPOs aplicados a ele. Eles podem ser aplicados a usuários individuais, hosts ou grupos sendo aplicados diretamente a uma UO. Cada GPO contém uma ou mais configurações de Diretiva de Grupo que podem ser aplicadas no nível da máquina local ou no contexto do Active Directory.

### Example GPOs

- Estabelecer políticas de senha diferentes para contas de serviço, contas de administrador e contas de usuário padrão usando GPOs separados
- Impedindo o uso de dispositivos de mídia removíveis (como dispositivos USB)
- Aplicando um screensaver com uma senha
- Restringindo o acesso a aplicativos que um usuário padrão pode não precisar, como cmd.exe e PowerShell
- Aplicação de políticas de auditoria e registro
- Bloquear usuários de executar certos tipos de programas e scripts
- Implantando software em um domínio
- Exibição de um banner de logon sempre que um usuário faz login em um sistema
- Proibindo o uso de hash LM no domínio
- Execução de scripts quando os computadores iniciam/desligam ou quando um usuário faz login/logout de sua máquina

Vamos usar como exemplo uma implementação padrão do Active Directory do Windows Server 2008, a complexidade da senha é imposta por padrão. Os requisitos de complexidade de senha são os seguintes:
- As senhas devem ter pelo menos 7 caracteres.
- As senhas devem conter caracteres de pelo menos três das quatro categorias a seguir: Caracteres maiúsculos (A-Z), Caracteres minúsculos (a-z), Números (0-9), Caracteres especiais (por exemplo, `!@#$%^&*()_+|~-={}[]:";'<>?,./)`

Esses são apenas alguns exemplos do que pode ser feito com a Diretiva de Grupo. Existem centenas de configurações que podem ser aplicadas em um GPO, que podem ser extremamente granulares. Por exemplo, abaixo estão algumas opções que podemos definir para sessões de Área de Trabalho Remota.

![[Pasted image 20230605113130.png]]

As configurações de GPO são processadas usando a estrutura hierárquica do AD e são aplicadas usando a regra de Ordem de Precedência conforme a tabela abaixo:
- Local Group Policy: As políticas são definidas diretamente para o host localmente fora do domínio. Qualquer configuração aqui será substituída se uma configuração semelhante for definida em um nível superior.
- Site Policy: Quaisquer políticas específicas para o Enterprise Site no qual o host reside. Lembre-se de que os ambientes corporativos podem abranger grandes campus e até mesmo entre países. Portanto, é lógico que um site pode ter suas próprias políticas a seguir que podem diferenciá-lo do resto da organização. As políticas de controle de acesso são um ótimo exemplo disso. Digamos que um edifício ou local específico realize pesquisas secretas ou restritas e exija um nível mais alto de autenticação para acesso aos recursos. Você pode especificar essas configurações no nível do site e verifique se eles estão vinculados para não serem substituídos pela política de domínio. Essa também é uma ótima maneira de executar ações como impressora e compartilhar mapeamento para usuários em sites específicos.
- Domain-wide Policy: Quaisquer configurações que você deseja aplicar em todo o domínio. Por exemplo, definir o nível de complexidade da política de senha, configurar um plano de fundo da área de trabalho para todos os usuários e definir um banner de Aviso de Uso e Consentimento para Monitorar na tela de login.
- Organizational Unit (OU): Essas configurações afetariam usuários e computadores que pertencem a OUs específicas. Você gostaria de colocar quaisquer configurações exclusivas aqui que sejam específicas da função. Por exemplo, o mapeamento de uma unidade de compartilhamento específica que só pode ser acessada pelo RH, acesso a recursos específicos como impressoras ou a capacidade dos administradores de TI de utilizar o PowerShell e o prompt de comando.
- Qualquer Policies da OU aninhados dentro de outras OUs: As configurações nesse nível refletiriam permissões especiais para objetos dentro de OUs aninhadas. Por exemplo, fornecer aos analistas de segurança um conjunto específico de configurações de política do Applocker que diferem das configurações padrão do Applocker de TI.

Podemos gerenciar a Política de Grupo no Console de Gerenciamento de Política de Grupo (encontrado em Ferramentas Administrativas no Menu Iniciar em um controlador de domínio), aplicativos personalizados ou usando o módulo PowerShell GroupPolicy via linha de comando. A Diretiva de Domínio Padrão é o GPO padrão que é criado e vinculado automaticamente ao domínio. Ele tem a precedência mais alta de todos os GPOs e é aplicado por padrão a todos os usuários e computadores. Geralmente, é uma prática recomendada usar esse GPO padrão para gerenciar as configurações padrão que serão aplicadas em todo o domínio. A política de controladores de domínio padrão também é criada automaticamente com um domínio e define as configurações básicas de segurança e auditoria para todos os controladores de domínio em um determinado domínio. Ele pode ser personalizado conforme necessário, como qualquer GPO.

### GPO Order of Precedence

Os GPOs são processados ​​de cima para baixo ao visualizá-los do ponto de vista organizacional do domínio. Um GPO vinculado a uma OU no nível mais alto em uma rede do Active Directory (no nível do domínio, por exemplo) seriam processados ​​primeiro, seguidos pelos vinculados a uma OU filha, etc. Isso significa que um GPO vinculado diretamente a uma UO contendo objetos de usuário ou computador é processado por último. Em outras palavras, um GPO anexado a uma UO específica teria precedência sobre um GPO anexado no nível do domínio porque ele será processado por último e pode correr o risco de substituir as configurações em um GPO superior na hierarquia do domínio. Mais uma coisa para acompanhar com precedência é que uma configuração definida na política de computador sempre terá uma prioridade mais alta da mesma configuração aplicada a um usuário. O gráfico a seguir ilustra a precedência e como ela é aplicada.

![[Pasted image 20230605133757.png]]

Vejamos outro exemplo usando o Console de Gerenciamento de Diretiva de Grupo em um Controlador de Domínio. Nesta imagem, vemos vários GPOs.
O GPO Disabled Forced Restarts terá precedência sobre o GPO Logon Banner, pois será processado por último. Quaisquer configurações definidas no GPO Disabled Forced Restarts podem substituir as configurações em qualquer GPO superior na hierarquia (incluindo aqueles vinculados à UO Corp).

![[Pasted image 20230605133824.png]]

Esta imagem também mostra um exemplo de vários GPOs vinculados à UO Corp. Quando mais de um GPO está vinculado a uma UO, eles são processados ​​com base na Ordem de Vinculação. O GPO com a ordem de link mais baixa é processado por último, ou o GPO com ordem de link 1 tem a precedência mais alta, depois 2 e 3 e assim por diante. Portanto, em nosso exemplo acima, o GPO Disallow LM Hash terá precedência sobre os GPOs Block Removable Media e Disable Guest Account, o que significa que será processado primeiro.

É possível especificar a opção Enforced para impor configurações em um GPO específico. Se essa opção for definida, as configurações de política em GPOs vinculados a OUs inferiores NÃO PODEM substituir as configurações. Se um GPO for definido no nível do domínio com a opção Imposta selecionada, as configurações contidas nesse GPO serão aplicadas a todas as OUs no domínio e não poderão ser substituídas por políticas de OU de nível inferior. No passado, essa configuração era chamada de No Override e era definida no contêiner em questão em Active Directory Users and Computers. Abaixo, podemos ver um exemplo de um GPO imposto, em que o GPO de banner de logon tem precedência sobre os GPOs vinculados a OUs inferiores e, portanto, não será substituído.

![[Pasted image 20230605133946.png]]

Independentemente de qual GPO esteja definido como imposto, se o GPO de Diretiva de Domínio Padrão for imposto, ele terá precedência sobre todos os GPOs em todos os níveis.

![[Pasted image 20230605133958.png]]

Também é possível definir a opção Bloquear herança em uma UO. Se isso for especificado para uma UO específica, as políticas superiores (como no nível do domínio) NÃO serão aplicadas a essa UO. Se ambas as opções forem definidas, a opção No Override terá precedência sobre a opção Bloquear herança. Aqui está um exemplo rápido. A OU Computers está herdando GPOs definidos na OU Corp na imagem abaixo.

![[Pasted image 20230605134028.png]]

Se a opção Block Inheritance for escolhida, podemos ver que os 3 GPOs aplicados acima da OU Corp não são mais aplicados na OU Computers.

![[Pasted image 20230605134040.png]]

## Group Policy Refresh Frequency

Quando um novo GPO é criado, as configurações não são aplicadas automaticamente imediatamente. O Windows executa atualizações periódicas da Política de Grupo, que por padrão é feito a cada 90 minutos com um deslocamento aleatório de +/- 30 minutos para usuários e computadores. O período é de apenas 5 minutos para os controladores de domínio atualizarem por padrão. Quando um novo GPO é criado e vinculado, pode levar até 2 horas (120 minutos) até que as configurações entrem em vigor. Esse deslocamento aleatório de +/- 30 minutos é definido para evitar sobrecarregar os controladores de domínio fazendo com que todos os clientes solicitem a Diretiva de Grupo do controlador de domínio simultaneamente.

É possível alterar o intervalo de atualização padrão na própria Diretiva de Grupo. Além disso, podemos emitir o comando gpupdate /force para iniciar o processo de atualização. Este comando irá comparar os GPOs atualmente aplicados na máquina com o controlador de domínio e modificá-los ou ignorá-los, dependendo se eles foram alterados desde a última atualização automática.

Podemos modificar o intervalo de atualização por meio da Política de Grupo clicando em Configuração do Computador --> Políticas --> Modelos Administrativos --> Sistema --> Política de Grupo e selecionando Definir intervalo de atualização da Diretiva de Grupo para computadores. Embora possa ser alterado, não deve ser configurado para ocorrer com muita frequência, ou pode causar congestionamento de rede levando a problemas de replicação.

![[Pasted image 20230605135604.png]]

## Security Considerations of GPOs

Conforme mencionado anteriormente, os GPOs podem ser usados ​​para realizar ataques. Esses ataques podem incluir adicionar direitos adicionais a uma conta de usuário que controlamos, adicionar um administrador local a um host, ou criar uma tarefa agendada imediata para executar um comando malicioso, como modificar a associação ao grupo, adicionar uma nova conta de administrador, estabelecer uma conexão shell reversa, ou até mesmo instalar malware direcionado em um domínio. Esses ataques geralmente acontecem quando um usuário tem os direitos necessários para modificar um GPO que se aplica a uma UO que contém uma conta de usuário controlada por nós ou um computador.

Abaixo está um exemplo de um caminho de ataque GPO identificado usando a ferramenta BloodHound. Este exemplo mostra que o grupo Usuários do Domínio pode modificar o GPO Desconectar Idle RDP devido à associação de grupo aninhado. Este exemplo mostra que o grupo Usuários do Domínio pode modificar o GPO Desconectar Idle RDP devido à associação de grupo aninhado. Nesse caso, veremos a seguir a quais OUs esse GPO se aplica e se podemos aproveitar esses direitos para obter controle sobre um usuário de alto valor (administrador ou administrador do domínio) ou computador (servidor, controlador de domínio ou host crítico) e mova-se lateralmente para aumentar os privilégios no domínio.

![[Pasted image 20230605135748.png]]

Cobrimos muitas informações até este ponto. O Active Directory é um tópico vasto e nós apenas arranhamos a superfície. Cobrimos a teoria fundamental agora; vamos colocar a mão na massa e brincar com objetos do Active Directory, Diretiva de Grupo e muito mais na próxima seção.