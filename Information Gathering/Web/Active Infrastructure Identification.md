A infraestrutura de um aplicativo da web é o que o mantém em execução e permite que ele funcione. Os servidores da Web estão diretamente envolvidos na operação de qualquer aplicativo da web. Alguns dos mais populares são Apache, Nginx e Microsoft IIS, entre outros.

Se descobrirmos o servidor da web por trás do aplicativo de destino, ele poderá nos dar uma boa idéia do sistema operacional em execução no servidor de back-end. Por exemplo, se descobrirmos a versão do IIS em execução, podemos inferir a versão do sistema operacional Windows em uso, mapeando a versão do IIS de volta para a versão do Windows em que ela é instalada por padrão. Algumas instalações padrão são:

- IIS 6.0: Windows Server 2003
- IIS 7.0-8.5: Windows Server 2008 / Windows Server 2008R2
- IIS 10.0 ( v1607-v1709 ): Windows Server 2016
- IIS 10.0 ( v1809- ): Windows Server 2019

Embora isso geralmente esteja correto ao lidar com o Windows, não podemos ter certeza no caso de distribuições baseadas em Linux ou BSD, pois elas podem executar diferentes versões de servidores da web no caso de Nginx ou Apache. Esse tipo de tecnologia pode interferir ou alterar nossas atividades de teste futuras. No entanto, se lidarmos com um servidor da web, não conseguiremos encontrar impressões digitais sobre o servidor em HTML ou JS.

## Servidores da Web

Precisamos descobrir o máximo de informações possível do servidor da web para entender sua funcionalidade, o que pode afetar testes futuros. Por exemplo, funcionalidade de reescrita de URL, balanceamento de carga, mecanismos de script usados no servidor ou um `Intrusion detection system` (`IDS`) no local pode impedir algumas de nossas atividades de teste.

A primeira coisa que podemos fazer para identificar a versão do servidor da web é olhar para os cabeçalhos de resposta.

```shell-session
casluxd@htb[/htb]$ curl -I "http://${TARGET}"

HTTP/1.1 200 OK
Date: Thu, 23 Sep 2021 15:10:42 GMT
Server: Apache/2.4.25 (Debian)
X-Powered-By: PHP/7.3.5
Link: <http://192.168.10.10/wp-json/>; rel="https://api.w.org/"
Content-Type: text/html; charset=UTF-8
```

Também existem outras características a serem levadas em consideração ao imprimir os servidores da web nos cabeçalhos de resposta. Estes são:

- Cabeçalho X-Powered-By: este cabeçalho pode nos dizer o que o aplicativo da web está usando. Podemos ver valores como PHP, ASP.NET, JSP, etc.
- Cookies: os cookies são outro valor atraente, pois cada tecnologia, por padrão, possui seus cookies. Alguns dos valores padrão de cookies são:
    - .NET: `ASPSESSIONID<RANDOM>=<COOKIE_VALUE>`
    - PHP: `PHPSESSID=<COOKIE_VALUE>`
    - JAVA: `JSESSION=<COOKIE_VALUE>`

```shell-session
casluxd@htb[/htb]$ curl -I http://${TARGET}

HTTP/1.1 200 OK
Host: randomtarget.com
Date: Thu, 23 Sep 2021 15:12:21 GMT
Connection: close
X-Powered-By: PHP/7.4.21
Set-Cookie: PHPSESSID=gt02b1pqla35cvmmb2bcli96ml; path=/ 
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-type: text/html; charset=UTF-8
```

Outras ferramentas disponíveis analisam características comuns do servidor da Web, investigando-as e comparando suas respostas com um banco de dados de assinaturas para adivinhar informações como versão do servidor da web, módulos instalados e serviços ativados. Algumas dessas ferramentas são:

[Whatweb](https://www.morningstarsecurity.com/research/whatweb) reconhece tecnologias da web, incluindo sistemas de gerenciamento de conteúdo ( CMS ), plataformas de blogs, pacotes estatísticos / analíticos, bibliotecas JavaScript, servidores da web e dispositivos incorporados. Recomendamos a leitura do `whatweb` menu de ajuda via `whatweb -h` entender as opções disponíveis, como os controles do nível de agressão ou a saída detalhada. Nesse caso, usaremos um nível de agressão de 3 através do `-a` sinalizador e saída verbosa via `-v`.

```shell-session
casluxd@htb[/htb]$ whatweb -a3 https://www.facebook.com -v

WhatWeb report for https://www.facebook.com
Status    : 200 OK
Title     : <None>
IP        : 31.13.92.36
Country   : IRELAND, IE

Summary   : Strict-Transport-Security[max-age=15552000; preload], PasswordField[pass], Script[text/javascript], X-XSS-Protection[0], HTML5, X-Frame-Options[DENY], Meta-Refresh-Redirect[/?_fb_noscript=1], UncommonHeaders[x-fb-rlafr,x-content-type-options,x-fb-debug,alt-svc]

Detected Plugins:
[ HTML5 ]
	HTML version 5, detected by the doctype declaration


[ Meta-Refresh-Redirect ]
	Meta refresh tag is a deprecated URL element that can be
	used to optionally wait x seconds before reloading the
	current page or loading a new page. More info:
	https://secure.wikimedia.org/wikipedia/en/wiki/Meta_refresh

	String       : /?_fb_noscript=1

[ PasswordField ]
	find password fields

	String       : pass (from field name)

[ Script ]
	This plugin detects instances of script HTML elements and
	returns the script language/type.

	String       : text/javascript

[ Strict-Transport-Security ]
	Strict-Transport-Security is an HTTP header that restricts
	a web browser from accessing a website without the security
	of the HTTPS protocol.

	String       : max-age=15552000; preload

[ UncommonHeaders ]
	Uncommon HTTP server headers. The blacklist includes all
	the standard headers and many non standard but common ones.
	Interesting but fairly common headers should have their own
	plugins, eg. x-powered-by, server and x-aspnet-version.
	Info about headers can be found at www.http-stats.com

	String       : x-fb-rlafr,x-content-type-options,x-fb-debug,alt-svc (from headers)

[ X-Frame-Options ]
	This plugin retrieves the X-Frame-Options value from the
	HTTP header. - More Info:
	http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
	aspx

	String       : DENY

[ X-XSS-Protection ]
	This plugin retrieves the X-XSS-Protection value from the
	HTTP header. - More Info:
	http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
	aspx

	String       : 0

HTTP Headers:
	HTTP/1.1 200 OK
	Vary: Accept-Encoding
	Content-Encoding: gzip
	x-fb-rlafr: 0
	Pragma: no-cache
	Cache-Control: private, no-cache, no-store, must-revalidate
	Expires: Sat, 01 Jan 2000 00:00:00 GMT
	X-Content-Type-Options: nosniff
	X-XSS-Protection: 0
	X-Frame-Options: DENY
	Strict-Transport-Security: max-age=15552000; preload
	Content-Type: text/html; charset="utf-8"
	X-FB-Debug: r2w+sMJ7lVrMjS/ETitC6cNpJXma0r3fbt0rIlnTPAfQqTc+U4PQopVL7sR/6YA/ZKRkqP1wMPoFdUfMBP1JSA==
	Date: Wed, 06 Oct 2021 09:04:27 GMT
	Alt-Svc: h3=":443"; ma=3600, h3-29=":443"; ma=3600,h3-27=":443"; ma=3600
	Connection: close

WhatWeb report for https://www.facebook.com/?_fb_noscript=1
Status    : 200 OK
Title     : <None>
IP        : 31.13.92.36
Country   : IRELAND, IE

Summary   : Cookies[noscript], Strict-Transport-Security[max-age=15552000; preload], PasswordField[pass], Script[text/javascript], X-XSS-Protection[0], HTML5, X-Frame-Options[DENY], UncommonHeaders[x-fb-rlafr,x-content-type-options,x-fb-debug,alt-svc]

Detected Plugins:
[ Cookies ]
	Display the names of cookies in the HTTP headers. The
	values are not returned to save on space.

	String       : noscript

[ HTML5 ]
	HTML version 5, detected by the doctype declaration


[ PasswordField ]
	find password fields

	String       : pass (from field name)

[ Script ]
	This plugin detects instances of script HTML elements and
	returns the script language/type.

	String       : text/javascript

[ Strict-Transport-Security ]
	Strict-Transport-Security is an HTTP header that restricts
	a web browser from accessing a website without the security
	of the HTTPS protocol.

	String       : max-age=15552000; preload

[ UncommonHeaders ]
	Uncommon HTTP server headers. The blacklist includes all
	the standard headers and many non standard but common ones.
	Interesting but fairly common headers should have their own
	plugins, eg. x-powered-by, server and x-aspnet-version.
	Info about headers can be found at www.http-stats.com

	String       : x-fb-rlafr,x-content-type-options,x-fb-debug,alt-svc (from headers)

[ X-Frame-Options ]
	This plugin retrieves the X-Frame-Options value from the
	HTTP header. - More Info:
	http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
	aspx

	String       : DENY

[ X-XSS-Protection ]
	This plugin retrieves the X-XSS-Protection value from the
	HTTP header. - More Info:
	http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
	aspx

	String       : 0

HTTP Headers:
	HTTP/1.1 200 OK
	Vary: Accept-Encoding
	Content-Encoding: gzip
	Set-Cookie: noscript=1; path=/; domain=.facebook.com; secure
	x-fb-rlafr: 0
	Pragma: no-cache
	Cache-Control: private, no-cache, no-store, must-revalidate
	Expires: Sat, 01 Jan 2000 00:00:00 GMT
	X-Content-Type-Options: nosniff
	X-XSS-Protection: 0
	X-Frame-Options: DENY
	Strict-Transport-Security: max-age=15552000; preload
	Content-Type: text/html; charset="utf-8"
	X-FB-Debug: 7bEryjJ3tsTb/ap562d5L6KUJyJJ3bJh9XoamIo2lCVrX4cK/VAGbLx7muaAwnyobVm9myC3fQ+CXJqkk0eacg==
	Date: Wed, 06 Oct 2021 09:04:31 GMT
	Alt-Svc: h3=":443"; ma=3600, h3-29=":443"; ma=3600,h3-27=":443"; ma=3600
	Connection: close
```

Também gostaríamos de instalar [Wappalyzer](https://www.wappalyzer.com/) como uma extensão do navegador. Ele tem funcionalidade semelhante ao Whatweb, mas os resultados são exibidos enquanto navega no URL de destino.

![[Pasted image 20230629221129.png]]

[WafW00f](https://github.com/EnableSecurity/wafw00f) é um firewall de aplicativo da web (Ferramenta de impressão digital  `WAF`) que envia solicitações e analisa respostas para determinar se uma solução de segurança está em vigor. Podemos instalá-lo com o seguinte comando:

```shell-session
casluxd@htb[/htb]$ sudo apt install wafw00f -y
```

Podemos usar opções como `-a` para verificar todos os WAFs possíveis no lugar, em vez de parar de digitalizar na primeira partida, leia os destinos de um arquivo de entrada através do `-i` sinalizar ou proxy das solicitações usando o `-p` opção.

```shell-session
casluxd@htb[/htb]$ wafw00f -v https://www.tesla.com

                   ______
                  /      \
                 (  Woof! )
                  \  ____/                      )
                  ,,                           ) (_
             .-. -    _______                 ( |__|
            ()``; |==|_______)                .)|__|
            / ('        /|\                  (  |__|
        (  /  )        / | \                  . |__|
         \(_)_))      /  |  \                   |__|

                    ~ WAFW00F : v2.1.0 ~
    The Web Application Firewall Fingerprinting Toolkit

[*] Checking https://www.tesla.com
[+] The site https://www.tesla.com is behind CacheWall (Varnish) WAF.
[~] Number of requests: 2
```

[Aquatone](https://github.com/michenriksen/aquatone) é uma ferramenta para inspeção automática e visual de sites em muitos hosts e é conveniente para obter rapidamente uma visão geral das superfícies de ataque baseadas em HTTP, digitalizando uma lista de portas configuráveis, visitando o site com um navegador Chrome sem cabeça e tirando uma captura de tela. Isso é útil, especialmente quando se lida com enormes listas de subdomínios. `Aquatone` não está instalado por padrão no Parrot Linux, portanto, precisaremos instalar através dos seguintes comandos.

```shell-session
casluxd@htb[/htb]$ sudo apt install golang chromium-driver
casluxd@htb[/htb]$ go get github.com/michenriksen/aquatone
casluxd@htb[/htb]$ export PATH="$PATH":"$HOME/go/bin"
```

Depois disso, precisamos explorar algumas opções:

```shell-session
casluxd@htb[/htb]$ aquatone --help

Usage of aquatone:
  -chrome-path string
    	Full path to the Chrome/Chromium executable to use. By default, aquatone will search for Chrome or Chromium
  -debug
    	Print debugging information
  -http-timeout int
    	Timeout in miliseconds for HTTP requests (default 3000)
  -nmap
    	Parse input as Nmap/Masscan XML
  -out string
    	Directory to write files to (default ".")
  -ports string
    	Ports to scan on hosts. Supported list aliases: small, medium, large, xlarge (default "80,443,8000,8080,8443")
  -proxy string
    	Proxy to use for HTTP requests
  -resolution string
    	screenshot resolution (default "1440,900")
  -save-body
    	Save response bodies to files (default true)
  -scan-timeout int
    	Timeout in miliseconds for port scans (default 100)
  -screenshot-timeout int
    	Timeout in miliseconds for screenshots (default 30000)
  -session string
    	Load Aquatone session file and generate HTML report
  -silent
    	Suppress all output except for errors
  -template-path string
    	Path to HTML template to use for report
  -threads int
    	Number of concurrent threads (default number of logical CPUs)
  -version
    	Print current Aquatone version
```

Agora, é hora de usar `cat` em nossa lista de subdomínios e canalize o comando para `aquatone` :

```shell-session
casluxd@htb[/htb]$ cat facebook_aquatone.txt | aquatone -out ./aquatone -screenshot-timeout 1000

aquatone v1.7.0 started at 2021-10-06T10:14:42+01:00

Targets    : 30
Threads    : 2
Ports      : 80, 443, 8000, 8080, 8443
Output dir : aquatone

edge-star-shv-01-cdg2.facebook.com: port 80 open
edge-extern-shv-01-waw1.facebook.com: port 80 open
whatsapp-chatd-edge-shv-01-ams4.facebook.com: port 80 open
edge-secure-shv-01-ham3.facebook.com: port 80 open
sv-se.facebook.com: port 80 open
ko.facebook.com: port 80 open
whatsapp-chatd-msgr-mini-edge-shv-01-lis1.facebook.com: port 80 open
synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com: port 80 open
edge-star-shv-01-cdg2.facebook.com: port 443 open
edge-extern-shv-01-waw1.facebook.com: port 443 open
whatsapp-chatd-edge-shv-01-ams4.facebook.com: port 443 open
http://edge-star-shv-01-cdg2.facebook.com/: 200 OK
http://edge-extern-shv-01-waw1.facebook.com/: 200 OK
edge-secure-shv-01-ham3.facebook.com: port 443 open
ondemand-edge-shv-01-cph2.facebook.com: port 443 open
sv-se.facebook.com: port 443 open
http://edge-secure-shv-01-ham3.facebook.com/: 200 OK
ko.facebook.com: port 443 open
whatsapp-chatd-msgr-mini-edge-shv-01-lis1.facebook.com: port 443 open
http://sv-se.facebook.com/: 200 OK
http://ko.facebook.com/: 200 OK
synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com: port 443 open
http://synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com/: 400 default_vip_400
https://edge-star-shv-01-cdg2.facebook.com/: 200 OK
https://edge-extern-shv-01-waw1.facebook.com/: 200 OK
http://edge-star-shv-01-cdg2.facebook.com/: screenshot timed out
http://edge-extern-shv-01-waw1.facebook.com/: screenshot timed out
https://edge-secure-shv-01-ham3.facebook.com/: 200 OK
https://sv-se.facebook.com/: 200 OK
https://ko.facebook.com/: 200 OK
http://edge-secure-shv-01-ham3.facebook.com/: screenshot timed out
http://sv-se.facebook.com/: screenshot timed out
http://ko.facebook.com/: screenshot timed out
https://synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com/: 400 default_vip_400
http://synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com/: screenshot successful
https://edge-star-shv-01-cdg2.facebook.com/: screenshot timed out
https://edge-extern-shv-01-waw1.facebook.com/: screenshot timed out
https://edge-secure-shv-01-ham3.facebook.com/: screenshot timed out
https://sv-se.facebook.com/: screenshot timed out
https://ko.facebook.com/: screenshot timed out
https://synthetic-e2e-elbprod-sli-shv-01-otp1.facebook.com/: screenshot successful
Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2021-10-06T10:14:42+01:00
 - Finished at : 2021-10-06T10:15:01+01:00
 - Duration    : 19s

Requests:
 - Successful : 12
 - Failed     : 5

 - 2xx : 10
 - 3xx : 0
 - 4xx : 2
 - 5xx : 0

Screenshots:
 - Successful : 2
 - Failed     : 10

Wrote HTML report to: aquatone/aquatone_report.html
```

Quando terminar, teremos um arquivo chamado `aquatone_report.html` onde podemos ver capturas de tela, tecnologias identificadas, cabeçalhos de resposta do servidor e HTML.

![[Pasted image 20230629221253.png]]

