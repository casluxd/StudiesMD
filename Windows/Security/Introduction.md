
A segurança é um tópico crítico em sistemas operacionais Windows. Sistemas windows tem várias partes móveis que apresentam uma vasta superfície de ataque. Devido a diversas aplicações bulti-in, features e camadas de configuração, sistemas Windows podem facilmente apresentar má configuração, abrindo uma porta de ataque mesmo que esteja tudo atualizado.

Há muitos recursos built-in que podem ser abusados e sofrem de uma ampla variedade de vulnerabilidades críticas, resultando em um amplo uso e bastante efetivo de exploits locais e remotos.

A Microsoft melhorou a segurança do Windows com o passar dos anos. Em um mundo de interligação que continua a expandir e os ataques se tornam mais sofisticados, Microsoft continua adicionando novas features que podem ser usadas por administradores de sistemas para endurecer o sistema e ativamente bloquear e detectar tentativas de intrusão e uso indevido.

Windows segue certos princípios de segurança. Eles são unidades no sistemas que podem ser autorizados ou autenticados para uma ação em particular. Essas unidades são usuários, computadores em uma rede, threads ou processos. Esses princípios são designados para fazerem mais difíceis para atacantes ou softwares maliciosos ganhar acesso não autorizado e explorar um sistema de maneira não intencional.

# Security Identifier (SID)

Cada security principals no sistema tem um único security identifier (SID). O sistema gera automaticamente SIDs. Isso significa que, por exemplo, temos dois usuários identicos no sistema. O Windows pode distinguir os dois e seus direitos com base no SIDs. Os SIDs são string values com diferentes tamanhos, que são armazenados no security database. Esses SIDs são adicionados no access token do usuário para identificar todas as ações que o usuário é autorizado a fazer.

Um SID consiste do Identifier Authority and Relative ID (RID). Em um domínio Active Directory, o SID inclui o SID do domínio.

Exemplo de SID:

`S-1-5-21-674899381-4069889467-2080702030-1002`

O SID é quebrado no padrão:

(SID)-(revision-level)-(identifier-authority)-(subauthority1)-(subauthority2)-(etc)

- **S:** Significa SID, ou seja, identifica a string como SID

- **1:** Revision Level. Nunca é alterado e sempre será 1.

- **5:** Identifier-authority. Uma string de 48-bit que identificar a authority(um computador ou uma rede) que criou o SID.

- **21:** Subauthority1. Este é um número variável que identifica a relação do usuário ou grupo descrito pelo SID para a autoridade que o criou. Ele nos diz em que ordem essa autoridade criou a conta do usuário.

- **674899381-4069889467-2080702030:** Subauthority2. Diz qual computador (ou domínio) criou o número.

- **1002:** Subauthority3. O RID distingue uma conta de outra conta. Nos diz se esse usuário é um normal user, um administrator ou parte de algum outro grupo.

  

# Security Accounts Manager (SAM) e Access Control Entries(ACE)

SAM garante os direitos de uma rede executar um processo específico.

Os próprios direitos de acesso são gerenciados pelo Access Control Entries(ACE) em uma Access Control List (ACL). As ACLs contém ACEs que definem usuários, grupos ou processos que possuem acesso a um arquivo ou para executar um processo, por exemplo.

# User Account Control (UAC)

User Account Control (UAC) é uma feature de segurança no Windows para previnir um malware de rodar ou manipular processos que podem causar dano em um computador ou no seu conteúdo. Há um Admin Approval Mode no UAC, que é designada para previnir softwares não esperados de serem instalados sem o conhecimento do administrador ou para previnir uma ampla alteração de serem feitas. Ceramten você já visualizou o prompt de consentimento para instalar um software e o sistema pergunta se você confirma se quer mesmo instalar o software. Desde que os direitos administrativos é requerido, uma janela é aberta, perguntando se confirma a instalação. Como um usuário padrão não tem direitos para instalação, a execução será bloqueada ou pedirá pela senha de um administrador. Esse prompt de consetimento interrompe a execução de scripts ou binários que um malware ou atacantes podem tentar executar sem que o usuário coloque a senha ou confirme a execução.

Para entender como o UAC funciona, nós precisamos saber como ele é estruturado e como funciona e o que trigga a janela de consentimento.
  
![image](https://academy.hackthebox.com/storage/modules/49/uacarchitecture1.png)

  

# Registry

A Registry é uma database hierarquica no Windows crítico para o sistema operacional. Ele armazena configurações de baixo nível para o sistema operacional e aplicações que escolham o usar. Ele é dividido em dados computer-specific e user-specific. Nós podemos abrir o Registry Editor escrevendo regedit no linha de comando ou na caixa de pesquisa do Windows.

Essa estrutura em árvore conssite de main folders (root keys) no qual subfolders(subkeys) com suas entries/files(values) são alocadas. Há 11 tipos de valores que podem ser armazenados em uma subkey.

- **REGBINARY:** Dados binários em qualquer forma.

- **REG_DWORD:** Um número de 32-bit.

- **REG_DWORD_LITTLE_ENDIAN:** Um número de 32-bit em formato little-endian. Windows é designado a rodar arquitetura de computador little-endian. Portanto, esse valor é definido como REG_DWORD no cabeçalho dos arquivos Windows.

- **REG_DWORD_BIG_ENDIAN:** Um número de 32-bit no formato big-endian. Alguns sistemas UNIX suportam arquiteturas big-endian.

- **REG_EXPAND_SZ:** Uma string terminada em nulo que contém referências não expandidas para variáveis de ambiente (por exemplo: %PATH%). Será uma string Unicode ou ANSI, dependendo se você usa as funções Unicode ou ANSI. Para expandir as referências de variáveis ​​de ambiente, use a função ExpandEnvironmentStrings.

- **REG_LINK:** Uma string Unicode terminada em nulo contendo o caminho de destino de um link simbólico criado chamando a função RegCreateKeyEx com REG_OPTION_CREATE_LINK.

- **REG_MULTI_SZ:** Uma sequência de strings terminadas em nulo, terminadas por uma string vazia (\0). O seguinte é um exemplo: String1\0String2\0String3\0LastString\0\0 O primeiro \0 encerra a primeira string, o penúltimo \0 encerra a última string e o \0 final encerra a sequência. Observe que o terminador final deve ser fatorado no comprimento da string.

- **REG_NONE:** Um tipo de valor não definido.

- **REG_QWORD:** Um número 64-bit.

- **REG_QWORD_LITTLE_ENDIAN:** Um número de 64 bits no formato little-endian. O Windows foi projetado para ser executado em arquiteturas de computador de pequeno porte. Portanto, esse valor é definido como REG_QWORD nos arquivos de cabeçalho do Windows.

- **REG_SZ:** Uma string terminada em nulo. Isso será uma string Unicode ou ANSI, dependendo se você usa as funções Unicode ou ANSI.

Cada pasta no computador é uma key. Todas as root keys iniciam com HKEY. Uma key como HKEY-LOCAL-MACHINE é abreviada para HKLM. A HKLM contém todas as configurações relevantes para o sistema local. Essa root key contém 6 subkeys como SAM, SECURITY, SYSTEM, SOFTWARE, HARDWARE e BCD, carregada na memória na inicialização(exceto HARDWARE, o qual é dinamicamente carregada).

O sistema inteiro de registry é armazenada em vários arquivos no sistema operacional. Você pode encontrá-los em: `C:\Windows\System32\Config\`

Essa user-specify registry hive (HKCU) é armazenada no user folder (C:\Windows\Users\<USERNAME>\Ntuser.dat)

  

# Chaves de registro Run e RunOne

  

Há também as chamadas seções de registro, que contêm um grupo lógico de chaves, subchaves e valores para dar suporte a software e arquivos carregados na memória quando o sistema operacional é iniciado ou um usuário faz login. Essas seções são úteis para manter o acesso ao sistema. Elas são chamadas de chaves de registro Run e RunOnce.

O registro do Windows inclui as quatro chaves a seguir:
`
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce


# Application Whitelisting

Uma lista de permissões de aplicativos é uma lista de aplicativos de software ou executáveis ​​aprovados que podem estar presentes e serem executados em um sistema. O objetivo é proteger o ambiente contra malware prejudicial e software não aprovado que não se alinha com as necessidades comerciais específicas de uma organização. A implementação de uma lista de permissões forçada pode ser um desafio, especialmente em uma rede grande. Uma organização deve implementar uma lista de permissões no modo de auditoria inicialmente para garantir que todos os aplicativos necessários sejam incluídos na lista de permissões e não sejam bloqueados por um erro de omissão, o que pode causar mais problemas do que soluções.

A lista negra, por outro lado, especifica uma lista de software/aplicativos nocivos ou não permitidos para bloquear, e todos os outros podem ser executados/instalados. A lista de permissões é baseada em um princípio de "confiança zero", no qual todos os softwares/aplicativos são considerados "ruins", exceto aqueles especificamente permitidos. A manutenção de uma lista branca geralmente é menos ouvida, pois um administrador de sistema precisará apenas especificar o que é permitido e não atualizar constantemente uma "lista negra" com novos aplicativos maliciosos.

A lista de permissões é recomendada por organizações como o NIST, especialmente em ambientes de alta segurança.

  
# AppLocker

O AppLocker é a solução de lista branca de aplicativos da Microsoft e foi introduzido pela primeira vez no Windows 7. O AppLocker oferece aos administradores de sistema controle sobre quais aplicativos e arquivos os usuários podem executar. Ele oferece controle granular sobre executáveis, scripts, arquivos do instalador do Windows, DLLs, aplicativos empacotados e instaladores de aplicativos empacotados.

Ele permite a criação de regras com base em atributos de arquivo, como o nome do editor (que pode ser derivado da assinatura digital), nome do produto, nome do arquivo e versão. As regras também podem ser configuradas com base em caminhos de arquivo e hashes. As regras podem ser aplicadas a grupos de segurança ou usuários individuais, com base na necessidade do negócio. O AppLocker pode ser implantado no modo de auditoria primeiro para testar o impacto antes de impor todas as regras.

# Local Group Policy

A Diretiva de Grupo permite que os administradores definam, configurem e ajustem uma variedade de configurações. Em um ambiente de domínio, as políticas de grupo são enviadas de um controlador de domínio para todas as máquinas ingressadas no domínio às quais os objetos de política de grupo (GPOs) estão vinculados. Essas configurações também podem ser definidas em máquinas individuais usando a Diretiva de Grupo Local.

A Diretiva de Grupo pode ser configurada localmente, tanto em ambientes de domínio quanto em ambientes fora do domínio. A Diretiva de Grupo Local pode ser usada para ajustar certas configurações gráficas e de rede que, de outra forma, não seriam acessíveis por meio do Painel de Controle. Ele também pode ser usado para bloquear uma política de computador individual com configurações de segurança rigorosas, como permitir apenas a instalação/execução de determinados programas ou impor requisitos estritos de senha de conta de usuário.

Podemos abrir o Editor de Diretiva de Grupo Local abrindo o menu Iniciar e digitando gpedit.msc. O editor é dividido em duas categorias em Diretiva de Computador Local - Configuração do Computador e Configuração do Usuário.

Por exemplo, podemos abrir a Diretiva de computador local para ativar o Credential Guard ativando a configuração Ativar segurança baseada em virtualização. O Credential Guard é um recurso do Windows 10 que protege contra ataques de roubo de credenciais, isolando o processo LSA do sistema operacional.

Também podemos ativar a auditoria de conta ajustada e configurar o AppLocker no Editor de Diretiva de Grupo Local. Vale a pena explorar a Diretiva de Grupo Local e aprender sobre a ampla variedade de maneiras pelas quais ela pode ser usada para bloquear um sistema Windows.

