
Como um penetration tester, é importante possuir um conhecimento de uma variedade de tecnologias. Uma compreensão completa dos sistemas operacionais Windows e Linux é benéfica em uma ampla gama de tipos de avaliação. A maioria dos sistemas que encontraremos durante as avaliações, tanto on-premise quanto na cloud, serão baseadas nesses dois sistemas operacionais. É importante entender como atacar e como defender esses tipos de sistemas operacionais e como cada um deles pode ser usado como uma plataforma para realizar outras atividades de penetration testing.

# O sistema operacional Windows

A Microsoft introduziu pela primeira vez o sistema operacional Windows em 20 de novembro de 1985. A primeira versão do Windows era um shell de sistema operacional gráfico para MS-DOS. Versões posteriores do Windows Desktop introduziram os programas Windows File Manager,  Program Manager e Print Manager.

Windows 95 foi a primeira integração completa do Windows e do DOS e ofereceu suporte integrado à Internet pela primeira vez. Esta versão também estreou o navegador Internet Explorer. Desde a versão inicial, foram lançadas mais de uma dezena de versões do Windows, como Windows XP, Vista e 8, até a versão atual: Windows 10. Com o tempo, a Microsoft ofereceu várias edições de cada versão do Windows Desktop, atendendo a todos, desde consumidores casuais a clientes corporativos.

O Windows Server foi lançado pela primeira vez em 1993 com o lançamento do Windows NT 3.1 Advanced Server. O Windows NT viu várias atualizações ao longo dos anos, adicionando tecnologias como Internet Information Services (IIS), vários protocolos de rede, assistentes administrativos e muito mais. Com o lançamento do Windows 2000, a Microsoft lançou o Active Directory, originalmente destinado a ajudar os administradores de sistema a configurar o compartilhamento de arquivos, criptografia de dados, VPNs, etc. Windows Server 2000 também incluía o Microsoft Management Console (MMC) e suportava volumes de disco dinâmico.

O Windows Server 2003 veio a seguir com funções de servidor, um firewall integrado, o Volume Shadow Copy Service e muito mais. O Windows Server 2008 incluía clustering de failover, software de virtualização Hyper-V, Server Core, Event Viewer e grandes melhorias no Active Directory. Ao passar dos anos, a Microsoft lançou outras versões do Server, incluindo Server 2012, Server 2016 e, mais recentemente, Server 2019. Esta versão mais recente adicionou suporte para Kubernetes, contêineres Linux e recursos de segurança mais avançados.

À medida que novas versões do Windows são introduzidas, as versões mais antigas são substituídas e não recebem mais atualizações da Microsoft(a menos que um contrato de suporte de longo prazo seja adquirido em alguns casos). O Windows Server 2008 e 2012 atingiram o fim da vida útil para atualizações de segurança em 14 de janeiro de 2020. Atualmente, apenas o Server 2012 R2 e posterior têm suporte. Entretanto, a Microsoft lançou patches out-of-band para versões anteriores do Windows nos últimos anos devido à descoberta da vulnerabilidade crítica SMBv1(EternalBlue).

Muitas versões do Windows agora são consideradas "legadas" e não são mais suportadas. Muitas vezes, as organizações se veem executando vários sistemas operacionais mais antigos para dar suporte a aplicativos críticos ou devido a questões operacionais ou orçamentárias. Um avaliador precisa entender as diferenças enter as versões e as várias configurações incorretas e vulnerabilidades inerentes a cada uma.

A seguir está uma lista dos principais sistemas operacionais Windows e números de versão associados:

- Windows NT 4.0: 4.0
- Windows 2000: 5.0
- Windows XP: 5.1
- Windows Server 2003, 2003 R2: 5.2
- Windows Vista, Server 2008: 6.0
- Windows 7, Server 2008 R2: 6.1
- Windows 8, Server 2012: 6.2
- Windows 8.1, Server 2012 R2: 6.3
- Windows 10, Server 2016, Server 2019: 10.0

Podemos usar o cmdlet Get-WmiObject para encontrar informações sobre o sistema operacional. Esse cmdlet pode ser usado para obter instâncias de classes WMI ou informações sobre classes WMI disponíveis. Há uma variedade de maneiras de encontrar a versão e o número de compilação do nosso sistema. Podemos obter facilmente essas informações usando a classe win32_OperatingSystem, que mostra que estamos em um host Windows 10, build number 19041.

```powershell-session
PS C:\htb> Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber

Version    BuildNumber
-------    -----------
10.0.19041 19041
```

Algumas outras classes úteis que podem ser usadas com Get-WmiObject são Win32_Process para obter uma listagem de processos, Win32_Service para obter uma listagem de serviços e Win32_Bios para obter informações do BIOS (Basic Input/Output System). O BIOS é um firmware instalado na placa-mãe de um computador que controla as funções essenciais do computador, como gerenciamento de energia, interfaces de input/output e configuração do sistema. Podemos usar o parâmetro ComputerName para obter informações sobre computadores remotos. Get-WmiObject pode ser usado para iniciar e interromper serviços em computadores locais e remotos e muito mais.

# Conceitos de Acesso Remoto

Acesso Remoto é acessar um computador através de uma rede. O acesso local a um computador é necessário antes que alguém possa acessar outro computador remotamente. Existem inúmeros métodos para acesso remoto. Os avanços nas tecnologias de rede e Internet deram origem a setores inteiros que dependem completamente do acesso remoto e da administração remota de sistemas de computador.

Considere MSPs(Managed Service Provider) e MSSPs(Managed Security Service Provider), ambos os setores dependem principalmente do gerenciamento remoto dos sistemas de computadores de seus clientes. Esta funcionalidade permite-lhes centralizar a gestão, padronizar quais tecnologias são usadas, automatizar várias tarefas, permitir arranjos de trabalho remoto e permitir um tempo de resposta rápido quando surgirem problemas ou possíveis ameaças à segurança. O acesso remoto não se limita apenas a MSPs e MSSPs. Organizações com equipes de TI, desenvolvimento de software e/ou segurança usam métodos de acesso remoto diariamente para criar aplicativos, gerenciar servidores e administrar as estações de trabalho dos funcionários. Algumas das tecnologias de acesso remoto mais comuns incluem, mas não estão limitadas a:
- Virtual Private Network (VPN)
- Secure Shell (SSH)
- File Transfer Protocol (FTP)
- Virtual Network Computing (VNC)
- Windows Remote Management (ou PowerShell Remoting) (WinRM)
- Remote Desktop Protocol (RDP)

# Remote Desktop Protocol (RDP)

O RDP usa uma arquitetura cliente/servidor em que um aplicativo do lado do cliente é usado para especificar o endereço IP de destino de um computador ou o nome do host em uma rede em que o acesso RDP está habilitado. O computador de destino onde o acesso remoto RDP está ativado é considerado o servidor. É importante observar que o RDP escuta por padrão na porta lógica 3389. Lembre-se de que um endereço IP é usado como um identificador lógico para um computador em uma rede e uma porta lógica é um identificador atribuído a um aplicativo. Em termos mais simples, poderíamos considerar uma sub-rede de rede, uma rua em uma cidade (a rede corporativa), um endereço IP nessa sub-rede atribuído a um host como uma casa naquela rua e portas lógicas como janelas/portas que podem ser usadas para acessar a casa.

Assim que uma solicitação (encapsulada em um pacote) chegar a um computador de destino por meio de seu endereço IP, a solicitação será direcionada a um aplicativo hospedado no computador com base na porta especificada na requisição(incluído como um cabeçalho dentro de um pacote). 

Podemos usar o RDP para conectar-se a um alvo Windows a partir de um host de ataque executando Linux ou Windows. Se estivermos nos conectando a um destino do Windows a partir de um host do Windows, podemos usar o aplicativo cliente RDP integrado chamado Remote Desktop Connection (mstsc.exe). Confira o clipe abaixo para ver o uso básico:

![Using Remote Desktop](https://academy.hackthebox.com/storage/modules/49/UsingRemoteDesktopConnection.gif)

Para que isso funcione, o acesso remoto já deve ser permitido no sistema Windows de destino. Por padrão, o acesso remoto não é permitido nos sistemas operacionais Windows.

A Remote Desktop Connection também nos permite salvar perfis de conexão. Esse é um hábito comum entre os administradores de TI porque torna a conexão com sistemas remotos mais conveniente.

![Saving RDP Connections](https://academy.hackthebox.com/storage/modules/49/SavingRDPConnections.gif)

Como pentesters, podemos nos beneficiar ao procurar esses arquivos de área de trabalho remota (.rdp) salvos durante um compromisso.

Existem muitos outros aplicativos cliente de Área de Trabalho Remota, alguns dos quais estão listados neste artigo da Microsoft chamado Clientes de Área de Trabalho Remota(https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

# Usando xfreerdp

A partir de um host de ataque baseado em Linux, podemos usar uma ferramenta chamada xfreerdp para acessar remotamente os alvos do Windows. Você notará que usamos o xfreerdp em vários módulos por causa de sua facilidade de uso, conjunto de recursos, utilitário de linha de comando e eficiência.

![ConnectingwithXfreerdp](https://academy.hackthebox.com/storage/modules/49/ConnectingwithXfreerdp.gif)

Lembre-se que também podemos copiar e colar comandos xfreerdp na linha de comando, portanto não precisamos inserir opções manualmente. Existem várias opções disponíveis para nós com o xfreerdp, como redirecionamento de unidade para poder transferir arquivos de/para o host de destino.

Existem outros clientes RDP, como Remmina e rdesktop, e recomendamos que você experimente outros e veja o que funciona melhor para você.

# Conectando-se ao Windows Target

```shell-session
casluxd@htb[/htb]$ xfreerdp /v:<targetIp> /u:htb-student /p:Password
```

