Um host virtual (`vHost`) é um recurso que permite que vários sites sejam hospedados em um único servidor. Essa é uma excelente solução se você tiver muitos sites e não quiser passar pelo processo demorado ( e caro ) de configurar um novo servidor web para cada um. Imagine ter que configurar um servidor da web diferente para uma versão móvel e desktop da mesma página. Existem duas maneiras de configurar hosts virtuais:

- Hospedagem virtual baseada em IP
- Hospedagem virtual baseada em nome

#### Hospedagem virtual baseada em IP

Para esse tipo, um host pode ter várias interfaces de rede. Vários endereços IP, ou aliases de interface, podem ser configurados em cada interface de rede de um host. Os servidores ou servidores virtuais em execução no host podem se vincular a um ou mais endereços IP. Isso significa que diferentes servidores podem ser endereçados em diferentes endereços IP neste host. Do ponto de vista do cliente, os servidores são independentes um do outro.

#### Hospedagem virtual baseada em nome

A distinção para qual domínio o serviço foi solicitado é feita no nível do aplicativo. Por exemplo, vários nomes de domínio, como `admin.inlanefreight.htb` e `backup.inlanefreight.htb`, pode se referir ao mesmo IP. Internamente no servidor, eles são separados e distinguidos usando pastas diferentes. Usando este exemplo, em um servidor Linux, o vHost `admin.inlanefreight.htb` poderia apontar para a pasta `/var/www/admin`. Para `backup.inlanefreight.htb` o nome da pasta seria adaptado e poderia parecer algo `/var/www/backup`.

Durante nossa descoberta de subdomínios, vimos alguns subdomínios com o mesmo endereço IP que podem ser hosts virtuais ou, em alguns casos, servidores diferentes sentados atrás de um proxy.

Imagine que identificamos um servidor da web em `192.168.10.10` durante um pentest interno e mostra um site padrão usando o seguinte comando. Existem hosts virtuais presentes?

```shell-session
casluxd@htb[/htb]$ curl -s http://192.168.10.10

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Vamos fazer uma solicitação `cURL` enviando um domínio identificado anteriormente durante a coleta de informações no cabeçalho `HOST`. Podemos fazer isso assim:

```shell-session
casluxd@htb[/htb]$ curl -s http://192.168.10.10 -H "Host: randomtarget.com"

<html>
    <head>
        <title>Welcome to randomtarget.com!</title>
    </head>
    <body>
        <h1>Success! The randomtarget.com server block is working!</h1>
    </body>
</html>
```

Agora podemos automatizar isso usando um arquivo de dicionário de possíveis nomes de vhost (como `/opt/useful/SecLists/Discovery/DNS/namelist.txt` no Pwnbox ) e examinando o cabeçalho Content-Length para procurar diferenças.

#### vHosts List

```shell-session
app
blog
dev-admin
forum
help
m
my
shop
some
store
support
www
```

#### vHost Fuzzing

```shell-session
casluxd@htb[/htb]$ cat ./vhosts | while read vhost;do echo "\n********\nFUZZING: ${vhost}\n********";curl -s -I http://192.168.10.10 -H "HOST: ${vhost}.randomtarget.com" | grep "Content-Length: ";done


********
FUZZING: app
********
Content-Length: 612

********
FUZZING: blog
********
Content-Length: 612

********
FUZZING: dev-admin
********
Content-Length: 120

********
FUZZING: forum
********
Content-Length: 612

********
FUZZING: help
********
Content-Length: 612

********
FUZZING: m
********
Content-Length: 612

********
FUZZING: my
********
Content-Length: 612

********
FUZZING: shop
********
Content-Length: 612

********
FUZZING: some
********
Content-Length: 195

********
FUZZING: store
********
Content-Length: 612

********
FUZZING: support
********
Content-Length: 612

********
FUZZING: www
********
Content-Length: 185
```

Identificamos com sucesso um host virtual chamado `dev-admin`, que podemos acessar usando um `cURL` solicitação.

```shell-session
casluxd@htb[/htb]$ curl -s http://192.168.10.10 -H "Host: dev-admin.randomtarget.com"

<!DOCTYPE html>
<html>
<body>

<h1>Randomtarget.com Admin Website</h1>

<p>You shouldn't be here!</p>

</body>
</html>
```

## Automatizando a descoberta de hosts virtuais

Podemos usar essa abordagem manual para uma pequena lista de hosts virtuais, mas não será viável se tivermos uma lista extensa. Usando [ffuf](https://github.com/ffuf/ffuf), podemos acelerar o processo e o filtro com base nos parâmetros presentes na resposta. Vamos replicar o mesmo processo que fizemos com o ffuf, mas primeiro, vamos examinar algumas de suas opções.

```shell-session
MATCHER OPTIONS:
  -mc                 Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403,405)
  -ml                 Match amount of lines in response
  -mr                 Match regexp
  -ms                 Match HTTP response size
  -mw                 Match amount of words in response

FILTER OPTIONS:
  -fc                 Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl                 Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fr                 Filter regexp
  -fs                 Filter HTTP response size. Comma separated list of sizes and ranges
  -fw                 Filter by amount of words in response. Comma separated list of word counts and ranges
```

Podemos corresponder ou filtrar respostas com base em diferentes opções. O servidor da web responde com um site padrão e estático toda vez que emitimos um host virtual inválido no cabeçalho `HOST`. Podemos usar o filtro por tamanho usando a opção`-fs` para descartar a resposta padrão, pois ela sempre terá o mesmo tamanho.

```shell-session
casluxd@htb[/htb]$ ffuf -w ./vhosts -u http://192.168.10.10 -H "HOST: FUZZ.randomtarget.com" -fs 612

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.10.10
 :: Wordlist         : FUZZ: ./vhosts
 :: Header           : Host: FUZZ.randomtarget.com
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 612
________________________________________________

dev-admin               [Status: 200, Size: 120, Words: 7, Lines: 12]
www                     [Status: 200, Size: 185, Words: 41, Lines: 9]
some                    [Status: 200, Size: 195, Words: 41, Lines: 9]
:: Progress: [12/12] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```

Onde:
- `-w`: Caminho para a nossa lista de palavras
- `-u`: URL que queremos distorcer
- `-H "HOST: FUZZ.randomtarget.com"`: Este é o cabeçalho `HOST` e a palavra `FUZZ` será usado como o ponto de inflamação.
- `-fs 612`: Filtre as respostas com um tamanho de 612, tamanho de resposta padrão neste caso.


