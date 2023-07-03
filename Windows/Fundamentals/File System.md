Existem 5 tipos de sistemas de arquivos do Windows: FAT12, FAT16, FAT32, NTFS e exFAT. FAT12 e FAT16 não são mais usados em sistemas operacionais Windows modernos. 

FAT32 (File Allocation Table) é amplamente usado em muitos tipos de dispositivos de armazenamento, como cartões de memória USB e cartões SD, mas também pode ser usado para formatar discos rígidos. O "32" no nome refere-se ao fato de que o FAT32 usa 32 bits de dados para identificar clusters de dados em um dispositivo de armazenamento.

Pros do FAT32:
- Compatibilidade de dispositivo: pode ser usado em computadores, câmeras digitais, consoles de jogos, smartphones, tablets e mais.
- Compatibilidade entre sistemas operacionais: Funciona em todos sistemas operacionais Windows começando do Windows 95 e também é suportado pelo MacOS e Linux.

Contra do FAT32:
- Pode ser usado apenas com arquivos menores que 4GB.
- Sem recursos integrados de proteção de dados ou compactação de arquivos.
- Deve usar ferramentas de terceiros para criptografia de arquivos.

NTFS (New Technology File System) é o sistema de arquivos padrão do Windows desde o Windows NT 3.1. Além de compensar as deficiências do FAT32, NTFS também tem melhor suporte para metadados e melhor desempenho devido à estrutura de dados aprimorada.

Pros do NTFS:
- O NTFS é confiável e pode restaurar a consistência do sistema de arquivos em caso de falha do sistema ou queda de energia.
- Fornece segurança, permitindo-nos definir permissões granulares em arquivos e pastas.
- Suporta partições de tamanho muito grande.
- Possui registro no journaling(diário integrado), o que significa que as modificações do arquivo (adição, modificação, exclusão) são registradas.

Contras do NTFS:
- A maioria dos dispositivos móveis não oferece suporte nativo ao NTFS.
- Dispositivos de mídia mais antigos, como TVs e câmeras digitais, não oferecem suporte para dispositivos de armazenamento NTFS.

## Permissões NTFS

O sistema de arquivos NTFS tem muitas permissões básicas e avançadas. Alguns dos principais tipos de permissão são:

- Full Control: Permite ler, escrever, alterar, excluir, renomear, atribuir permissões e controlar outras configurações de segurança de arquivos/pastas. O usuário com "Full Control" pode realizar qualquer ação em relação ao objeto, incluindo conceder ou negar acesso a outros usuários.
- Modify: Permite a leitura, gravação e exclusão de arquivos/pastas, mas não permite alterar as permissões ou outras configurações do objeto.
- List Folder Contents: Permite visualizar e listar pastas e subpastas, bem como executar arquivos. As pastas herdam apenas essa permissão.
- Read and Execute: Permite visualizar e listar arquivos e subpastas, bem como executar arquivos. Arquivos e pastas herdam essa permissão.
- Write: Permite adicionar arquivos a pastas e subpastas e gravar em um arquivo.
- Read: Permite visualizar e listar pastas e subpastas e visualizar o conteúdo de um arquivo.
- Traverse Folder: Isso permite ou nega a capacidade de mover-se pelas pastas para acessar outros arquivos ou pastas. Por exemplo, um usuário pode não ter permissão para listar o conteúdo do diretório ou visualizar arquivos no diretório de documentos ou aplicativos da web neste exemplo `c:\users\bsmith\documents\webapps\backups\backup_02042020.zip`, mas com as permissões Traverse Folder aplicadas, eles podem acessar o arquivo de backup.

Arquivos e pastas herdam as permissões NTFS de sua pasta pai para facilitar a administração, portanto, os administradores não precisam definir explicitamente as permissões para cada arquivo e pasta, pois isso consumiria muito tempo. Se as permissões precisarem ser definidas explicitamente, um administrador pode desativar a herança de permissões para os arquivos e pastas necessários e, em seguida, definir as permissões diretamente em cada um.

## Integrity Control Access Control List (icacls)

As permissões NTFS em arquivos e pastas no Windows podem ser gerenciadas usando a GUI do File Explorer na guia de segurança. Além da GUI, também podemos obter um bom nível de granularidade sobre as permissões de arquivo NTFS no Windows a partir da linha de comando usando o utilitário icacls.

Podemos listar as permissões NTFS em um diretório específico executando icacls de dentro do diretório de trabalho ou icacls `C:\Windows` em um diretório que não esteja atualmente.

```cmd-session
C:\htb> icacls c:\windows
c:\windows NT SERVICE\TrustedInstaller:(F)
           NT SERVICE\TrustedInstaller:(CI)(IO)(F)
           NT AUTHORITY\SYSTEM:(M)
           NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
           BUILTIN\Administrators:(M)
           BUILTIN\Administrators:(OI)(CI)(IO)(F)
           BUILTIN\Users:(RX)
           BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
           CREATOR OWNER:(OI)(CI)(IO)(F)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

O nível de acesso do recurso é listado após cada usuário na saída. As possíveis configurações de herança são:
- (CI): container inherit: Quando uma permissão é configurada com a opção "Container Inherit", ela é aplicada aos objetos contidos dentro de um contêiner, como pastas e subpastas. Essa permissão é herdada pelos objetos filhos dentro do contêiner, mas não é aplicada ao próprio contêiner em si.
- (OI): object inherit: Ao utilizar a opção "Object Inherit", a permissão é aplicada aos arquivos dentro de um contêiner, como documentos ou outros tipos de arquivos. Essa permissão também é herdada por objetos filhos, mas não é aplicada ao contêiner em si.
- (IO): inherit only: Quando uma permissão é definida como "Inherit Only", ela não é aplicada ao objeto em que está definida, mas apenas aos objetos filhos. Isso permite que as permissões sejam herdadas pelos objetos dentro de um contêiner sem afetar o próprio objeto em que a permissão foi definida.
- (NP): do not propagate inherit: Ao utilizar a opção "Do Not Propagate Inherit", a permissão não é herdada por objetos filhos. Isso significa que os objetos dentro de um contêiner não receberão essa permissão específica, mesmo que as outras permissões sejam herdadas normalmente.
- (I): permission inherited from parent container: Essa opção indica que as permissões de um objeto são herdadas do contêiner pai. Isso significa que os objetos filhos receberão as mesmas permissões que foram definidas para o contêiner pai. Essa configuração é útil para garantir que os objetos dentro de um contêiner tenham as mesmas permissões básicas.

No exemplo acima, a conta `NT AUTHORITY\SYSTEM` tem object inherit, container inherit, inherit only e full access permissions. Isso significa que esta conta tem controle total sobre todos os objetos do sistema de arquivos neste diretório e subdiretórios.

As permissões básicas de acesso são as seguintes:
- F: Acesso Completo
- D: Deleção
- N: Sem acesso
- M: Modificação
- RX: Read and Execute
- R: Read-only
- W: Write-Only

Podemos adicionar e remover permissões por meio da linha de comando usando icacls. Aqui estamos executando icacls no contexto de uma conta de administrador local mostrando o diretório `C:\users` onde o usuário joe não tem nenhuma permissão de gravação.

```cmd-session
C:\htb> icacls c:\Users
c:\Users NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

Usando o comando `icacls c:\users /grant joe:f` podemos conceder ao usuário joe controle total sobre o diretório, mas dado que (oi) e (ci) não foram incluídos no comando, o usuário joe só terá direitos sobre a pasta `c:\users`, mas não sobre os subdiretórios do usuário e os arquivos contidos neles.

```cmd-session
C:\htb> icacls c:\users /grant joe:f
processed file: c:\users
Successfully processed 1 files; Failed processing 0 files
```

```cmd-session
C:\htb> >icacls c:\users
c:\users WS01\joe:(F)
         NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

Essas permissões podem ser revogadas usando o comando `icacls c:\users /remove joe.`

icacls é muito poderoso e pode ser usado em uma configuração de domínio para dar a certos usuários ou grupos permissões específicas sobre um arquivo ou pasta, negar acesso explicitamente, habilitar ou desabilitar permissões de herança e alterar a propriedade do diretório/arquivo.

# NTFS vs Share Permissions

A Microsoft detém mais de 70% da participação no mercado global de sistemas operacionais de desktop com Windows. Isso explica por que a maioria dos autores de malware escolhe escrever malware para o Windows e por que muitos consideram o Windows menos seguro do que outros sistemas operacionais. Do ponto de vista comercial, faz sentido que os autores de malware gastem recursos na criação de malware para Windows. A ideia de que qualquer sistema operacional é imune a malware é uma falácia técnica. Se o software pode ser escrito para um sistema operacional, um vírus pode ser escrito para um sistema operacional. Tenha em mente que um vírus, por definição, é um software escrito com intenção maliciosa e pode ser escrito para qualquer sistema operacional. Muitas variantes de malware criadas para Windows podem se espalhar pela rede por meio de compartilhamentos de rede com permissões brandas aplicadas. Também é importante notar que, até hoje, a infame vulnerabilidade EternalBlue ainda assombra sistemas Windows não corrigidos executando SMBv1 e muitas vezes abre caminho para o ransomware encerrar organizações.

O protocolo Server Message Block (SMB) é usado no Windows para conectar recursos compartilhados como arquivos e impressoras. É usado em ambientes de grandes, médias e pequenas empresas. Veja a imagem abaixo para visualizar este conceito:

![smb diagram](https://academy.hackthebox.com/storage/modules/49/smb_diagram.png)

Observação: Sempre que você vir uma visualização/diagrama de um conceito, reserve um tempo para entendê-lo completamente. Uma imagem pode valer mais que mil palavras, mas é muito tentador pular durante a leitura.

As permissões NTFS e as permissões de compartilhamento geralmente são consideradas iguais. Saiba que eles não são os mesmos, mas geralmente se aplicam ao mesmo recurso compartilhado. Vamos dar uma olhada nas permissões individuais que podem ser definidas para proteger/conceder acesso de objetos a um compartilhamento de rede hospedado em um sistema operacional Windows executando o sistema de arquivos NTFS.

# Share Permissions

- Full Control: Os usuários têm permissão para executar todas as ações fornecidas pelas permissões Change e Read, bem como alterar permissões para arquivos e subpastas NTFS
- Change: Os usuários têm permissão para ler, editar, excluir e adicionar arquivos e subpastas
- Read: Os usuários têm permissão para visualizar o conteúdo do arquivo e da subpasta

# NTFS Basic Permissions

- Full Control: Os usuários têm permissão para adicionar, editar, mover, excluir arquivos e pastas, bem como alterar as permissões NTFS que se aplicam a todas as pastas permitidas
- Modify: Os usuários têm permissões permitidas ou negadas para visualizar e modificar arquivos e pastas. Isso inclui adicionar ou excluir arquivos
- Read & Execute: Os usuários têm permissões permitidas ou negadas para ler o conteúdo dos arquivos e executar programas
- List folder contents: Os usuários têm permissões permitidas ou negadas para visualizar uma lista de arquivos e subpastas
- Read: Os usuários têm permissões permitidas ou negadas para ler o conteúdo dos arquivos
- Write: Os usuários têm permissões permitidas ou negadas para gravar alterações em um arquivo e adicionar novos arquivos a uma pasta
- Special Permissions: Uma variedade de opções de permissões avançadas

#### NTFS special permissions

- Travese folder / execute file: Os usuários têm permissões permitidas ou negadas para acessar uma subpasta dentro de uma estrutura de diretório, mesmo que o usuário tenha acesso negado ao conteúdo no nível da pasta pai. Os usuários também podem ter permissões permitidas ou negadas para executar programas
- List folder/read data: Os usuários têm permissões permitidas ou negadas para exibir arquivos e pastas contidos na pasta pai. Os usuários também podem ter permissão para abrir e visualizar arquivos
- Read attributes: Os usuários têm permissões permitidas ou negadas para exibir atributos básicos de um arquivo ou pasta. Exemplos de atributos básicos: sistema, arquivo, somente leitura e oculto
- Read extend attributes: Os usuários têm permissões permitidas ou negadas para exibir atributos estendidos de um arquivo ou pasta. Os atributos diferem dependendo do programa
- Create files/write data: Os usuários têm permissões permitidas ou negadas para criar arquivos em uma pasta e fazer alterações em um arquivo
- Create folders/append data: Os usuários têm permissões permitidas ou negadas para criar subpastas dentro de uma pasta. Os dados podem ser adicionados aos arquivos, mas o conteúdo pré-existente não pode ser substituído
- Write attributes: Os usuários têm permissão ou não para alterar os atributos do arquivo. Esta permissão não concede acesso à criação de arquivos ou pastas
- Write extended attributes: Os usuários têm permissões permitidas ou negadas para alterar atributos estendidos em um arquivo ou pasta. Os atributos diferem dependendo do programa
- Delete subfolders and files: Os usuários têm permissões permitidas ou negadas para excluir subpastas e arquivos. As pastas principais não serão excluídas
- Delete: Os usuários têm permissões permitidas ou negadas para excluir pastas, subpastas e arquivos principais.
- Read permissions: Os usuários têm permissões permitidas ou negadas para ler as permissões de uma pasta
- Change permissions: Os usuários têm permissões permitidas ou negadas para alterar as permissões de um arquivo ou pasta
- Take ownership: Os usuários têm ou não permissão para se apropriar de um arquivo ou pasta. O proprietário de um arquivo tem permissão total para alterar quaisquer permissões.

Lembre-se de que as permissões NTFS se aplicam ao sistema em que a pasta e os arquivos estão hospedados. Pastas criadas em NTFS herdam permissões de pastas pai por padrão. É possível desativar a herança para definir permissões personalizadas no pai e nas subpastas, como faremos posteriormente neste módulo. As permissões de compartilhamento se aplicam quando a pasta está sendo acessada por meio de SMB, geralmente de um sistema diferente na rede. Isso significa que alguém conectado localmente à máquina ou via RDP pode acessar a pasta e os arquivos compartilhados simplesmente navegando até o local no sistema de arquivos e só precisa considerar as permissões NTFS. As permissões no nível NTFS fornecem aos administradores um controle muito mais granular sobre o que os usuários podem fazer em uma pasta ou arquivo.

# Criando um Network Share

Nesse caso, criaremos uma pasta compartilhada criando primeiro uma nova pasta na área de trabalho do Windows 10. Lembre-se de que, na maioria dos grandes ambientes corporativos, os compartilhamentos são criados em uma rede de área de armazenamento (SAN), dispositivo de armazenamento conectado à rede (NAS) ou uma partição separada em unidades acessadas por meio de um sistema operacional de servidor como o Windows Server. Se alguma vez encontrarmos compartilhamentos em um sistema operacional de desktop, será uma pequena empresa ou um sistema de cabeça de ponte usado por um testador de penetração ou invasor mal-intencionado para coletar e exfiltrar dados.

![creating directory](https://academy.hackthebox.com/storage/modules/49/creating_directory.png)

Vamos usar a opção Compartilhamento avançado para configurar nosso compartilhamento.

![configuring share](https://academy.hackthebox.com/storage/modules/49/configuring_share.png)

Observe como o nome do compartilhamento é padronizado automaticamente para o nome da pasta. Além disso, podemos ver que é possível limitar o número de usuários que podem estar conectados a este compartilhamento simultaneamente. Em um ambiente do mundo real, é uma boa prática que os administradores definam esse número de acordo com o número de usuários que precisam acessar regularmente o recurso que está sendo compartilhado.

Semelhante às permissões NTFS, há uma lista de controle de acesso (ACL) para recursos compartilhados. Podemos considerar isso a lista de permissões SMB. Lembre-se de que, com recursos compartilhados, as listas de permissões SMB e NTFS se aplicam a todos os recursos compartilhados no Windows. A ACL contém entradas de controle de acesso (ACEs). Normalmente, essas ACEs são compostas por usuários e grupos (também chamados de entidades de segurança), pois são um mecanismo adequado para gerenciar e rastrear o acesso a recursos compartilhados.

Observe a entrada de controle de acesso padrão e as configurações de permissões.

![share permissions](https://academy.hackthebox.com/storage/modules/49/share_permissions.png)

Por enquanto, vamos aplicar essas configurações para testar o efeito dessa ACL e as permissões aplicadas como estão.

```shell-session
casluxd@htb[/htb]$ smbclient -L IPaddressOfTarget -U htb-student
Enter WORKGROUP\htb-student's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Company Data    Disk      
	IPC$            IPC       Remote IPC
```

O que poderia nos impedir de acessar este compartilhamento se todas as nossas entradas estiverem corretas e nossa lista de permissões tiver o grupo Everyone presente com pelo menos permissões de Read?

##### Windows Defender

É o Firewall do Windows Defender que pode estar bloqueando o acesso ao compartilhamento SMB. Como estamos nos conectando a partir de um sistema baseado em Linux, o firewall bloqueou o acesso de qualquer dispositivo que não esteja associado ao mesmo grupo de trabalho. Também é importante observar que, quando um sistema Windows faz parte de um grupo de trabalho, todas as solicitações de logon de rede são autenticadas no banco de dados SAM desse sistema Windows específico. Quando um sistema Windows é associado a um ambiente de Domínio Windows, todas as solicitações de logon de rede são autenticadas no Active Directory. A principal diferença entre um workgroup e um Windows Domain em termos de autenticação é que, com um workgroup, o banco de dados SAM local é usado e, em um Windows Domain, é usado um banco de dados centralizado baseado em rede (Active Directory). Devemos saber essas informações ao tentar fazer logon e autenticação com um sistema Windows. Considere onde a conta htb-student está hospedada para se conectar corretamente ao destino. 

Em termos de conexões de bloqueio de firewall, isso pode ser testado desativando completamente cada perfil de firewall no Windows ou habilitando regras de firewall de entrada predefinidas específicas nas configurações de segurança avançadas do Firewall do Windows Defender. Como a maioria dos firewalls, o Windows Defender Firewall permite ou nega o tráfego (neste caso, solicitações de acesso e conexão) fluindo de entrada e/ou saída.

As diferentes regras de entrada e saída estão associadas aos diferentes perfis de firewall no defender.

Perfis de firewall do Windows Defender:
- Public
- Private
- Domain

É uma prática recomendada habilitar regras predefinidas ou adicionar exceções personalizadas em vez de desativar o firewall completamente. Infelizmente, é muito comum que os firewalls sejam deixados completamente desativados por conveniência ou falta de compreensão. As regras de firewall em sistemas de desktop podem ser gerenciadas centralmente quando ingressadas em um ambiente de Domínio do Windows por meio do uso da Diretiva de Grupo. Os conceitos e configurações da Diretiva de Grupo estão fora do escopo deste módulo.

Depois que as regras de firewall de entrada apropriadas estiverem habilitadas, nos conectaremos com sucesso ao compartilhamento. Lembre-se de que só podemos nos conectar ao compartilhamento porque a conta de usuário que estamos usando (htb-student) está no grupo Everyone. Lembre-se de que deixamos as permissões de compartilhamento específicas para o grupo Everyone definidas como Read, o que literalmente significa que só poderemos ler arquivos neste compartilhamento. Depois que uma conexão é estabelecida com um compartilhamento, podemos criar um ponto de montagem de nosso Pwnbox para o sistema de arquivos da caixa de destino do Windows 10. É aqui que também devemos considerar que as permissões NTFS se aplicam juntamente com as permissões de compartilhamento. Lembre-se de que o NTFS é o sistema de arquivos padrão no Windows. Vamos voltar para nossa sessão xfreerdp com nossa caixa de destino do Windows 10 e dar uma olhada nas permissões NTFS na pasta Company Data.

![ntfs](https://academy.hackthebox.com/storage/modules/49/ntfs.png)

Há um controle mais granular com permissões NTFS que podem ser aplicadas a usuários e grupos. Sempre que vemos uma marca de seleção cinza ao lado de uma permissão, ela foi herdada de um diretório pai. Por padrão, todas as permissões NTFS são herdadas do diretório pai. No mundo do Windows, a unidade `C:\` é o diretório pai para governar todos os diretórios, a menos que um administrador do sistema desabilite a herança nas configurações de segurança avançadas de uma pasta recém-criada.

Em muitos casos, o(s) administrador(es) do sistema de uma organização seria(ão) responsável(is) por decidir quais permissões um usuário ou grupo de usuários obtém sobre os recursos de rede. É por isso que muitos ataques de spear phishing são direcionados a administradores de sistema e outros líderes de TI. Eles têm muita influência sobre o que é permitido nos ambientes que supervisionam, ainda mais do que os líderes não técnicos de c-level de uma organização em muitos casos. Por exemplo, os médicos ou executivos que trabalham em um hospital não terão direitos administrativos sobre a rede, mas sim os administradores do sistema.

Agora vamos dar ao grupo Everyone controle total no nível do compartilhamento e testar o impacto da mudança tentando criar um ponto de montagem para o compartilhamento na área de trabalho do nosso Pwnbox.

```shell-session
casluxd@htb[/htb]$ sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

Se este comando não estiver funcionando, verifique a sintaxe. Se a sintaxe estiver correta, mas o comando ainda não estiver funcionando, pode ser necessário instalar o cifs-utils. Isso pode ser feito com o seguinte comando:

```shell-session
casluxd@htb[/htb]$ sudo apt-get install cifs-utils
```

Depois de criarmos com sucesso o ponto de montagem na área de trabalho de nosso Pwnbox, devemos examinar algumas ferramentas integradas ao Windows que nos permitirão rastrear e monitorar o que fizemos.

O comando net share nos permite visualizar todas as pastas compartilhadas no sistema. Observe o compartilhamento que criamos e também a unidade `C:\`.

Você se lembra de nós compartilhando a unidade `C:\`?

Não compartilhamos C: manualmente. A unidade mais importante com os arquivos mais críticos em um sistema Windows é compartilhada via SMB na instalação. Isso significa que qualquer pessoa com acesso adequado pode acessar remotamente todo o `C:\` de cada sistema Windows em uma rede.

Também podemos ver o compartilhamento que criamos.

```cmd-session
C:\Users\htb-student> net share

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\WINDOWS                      Remote Admin
Company Data C:\Users\htb-student\Desktop\Company Data

The command completed successfully.
```

O Gerenciamento do Computador é outra ferramenta que podemos usar para identificar e monitorar recursos compartilhados em um sistema Windows.

![computer management](https://academy.hackthebox.com/storage/modules/49/computer_management.png)

Podemos dar uma olhada em compartilhamentos, sessões e arquivos abertos para ter uma ideia de quais informações isso nos fornece. Se houver uma situação em que ajudamos um indivíduo ou organização a responder a uma violação relacionada a SMB, esses são alguns ótimos lugares para verificar e começar a entender como a violação pode ter acontecido e o que pode ter sido deixado para trás.

# Visualizando logs de acesso de compartilhamento no Visualizador de Eventos

O Event Viewer é outro bom lugar para investigar as ações concluídas no Windows. Quase todo sistema operacional possui um mecanismo de log e um utilitário para visualizar os logs que foram capturados. Saiba que um log é como uma entrada de diário para um computador, onde o computador anota todas as ações que foram executadas e vários detalhes associados a essa ação. Podemos visualizar os logs criados para cada ação que executamos ao acessar a caixa de destino do Windows 10, bem como ao criar, editar e acessar a pasta compartilhada.

![[Pasted image 20230526142807.png]]

