
## Interactive

Uma sessão de logon local ou interativa é iniciada por um usuário que se autentica em um sistema local ou de domínio inserindo suas credenciais. Um logon interativo pode ser iniciado fazendo logon diretamente no sistema, solicitando uma sessão de logon secundária usando o comando runas por meio da linha de comando ou por meio de uma conexão de Área de Trabalho Remota.

## Non-interactive

As contas não interativas no Windows diferem das contas de usuário padrão, pois não exigem credenciais de login. Existem 3 tipos de contas não interativas: a conta do sistema local, a conta de serviço local e a conta de serviço de rede. As contas não interativas geralmente são usadas pelo sistema operacional Windows para iniciar automaticamente serviços e aplicativos sem exigir interação do usuário. Essas contas não têm senha associada a elas e geralmente são usadas para iniciar serviços quando o sistema é inicializado ou para executar tarefas agendadas.

Existem diferenças entre os três tipos de contas:
- Local System Account: Também conhecida como a conta `NT AUTHORITY\SYSTEM`, esta é a conta mais poderosa nos sistemas Windows. Ele é usado para uma variedade de tarefas relacionadas ao sistema operacional, como iniciar serviços do Windows. Esta conta é mais poderosa do que as contas do grupo de administradores locais.
- Local Service Account: Conhecida como a conta `NT AUTHORITY\LocalService`, esta é uma versão menos privilegiada da conta SYSTEM e tem privilégios semelhantes a uma conta de usuário local. Ele recebe funcionalidade limitada e pode iniciar alguns serviços.
- Network Service Account: Isso é conhecido como a conta `NT AUTHORITY\NetworkService` e é semelhante a uma conta de usuário de domínio padrão. Tem privilégios semelhantes à conta de serviço local na máquina local. Ele pode estabelecer sessões autenticadas para determinados serviços de rede.

