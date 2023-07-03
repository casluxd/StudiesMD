Ao executar a varredura de serviço, geralmente encontramos servidores da Web em execução nas portas 80 e 443. Os servidores da Web hospedam aplicativos da Web (às vezes mais de 1) que geralmente fornecem uma superfície de ataque considerável e um alvo de valor muito alto durante um teste de penetração. A enumeração adequada da Web é crítica, especialmente quando uma organização não está expondo muitos serviços ou esses serviços são devidamente corrigidos.

## Gobuster

Depois de descobrir um aplicativo da Web, sempre vale a pena verificar se podemos descobrir arquivos ou diretórios ocultos no servidor da Web que não sejam destinados ao acesso público. Podemos usar uma ferramenta como [ffuf](https://github.com/ffuf/ffuf) ou [GoBuster](https://github.com/OJ/gobuster) para realizar essa enumeração de diretório. Às vezes, encontraremos funcionalidades ocultas ou páginas/diretórios que expõem dados confidenciais que podem ser aproveitados para acessar o aplicativo da Web ou até mesmo a execução remota de código no próprio servidor da Web.

#### Enumeração de diretório/arquivo

O GoBuster é uma ferramenta versátil que permite a execução de força bruta de DNS, vhost e diretório. A ferramenta tem funcionalidade adicional, como enumeração de buckets públicos do AWS S3. Para os propósitos deste módulo, estamos interessados ​​nos modos de força bruta de diretório (e arquivo) especificados com o switch `dir`. Vamos executar uma varredura simples usando a lista de palavras `dirb` `common.txt`.

```shell-session
casluxd@htb[/htb]$ gobuster dir -u http://10.10.10.121/ -w /usr/share/dirb/wordlists/common.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.121/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirb/wordlists/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/12/11 21:47:25 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/index.php (Status: 200)
/server-status (Status: 403)
/wordpress (Status: 301)
===============================================================
2020/12/11 21:47:46 Finished
===============================================================
```

Um código de status HTTP `200` revela que a solicitação do recurso foi bem-sucedida, enquanto um código de status HTTP 403 indica que estamos proibidos de acessar o recurso. Um código de status 301 indica que estamos sendo redirecionados, o que não é um caso de falha. Vale a pena nos familiarizarmos com os vários códigos de status HTTP, que podem ser encontrados [aqui](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) .

A verificação foi concluída com sucesso e identifica uma instalação do WordPress em `/wordpress`. O WordPress é o CMS (Sistema de gerenciamento de conteúdo) mais comumente usado e tem uma enorme superfície de ataque potencial. Nesse caso, a visita `http://10.10.10.121/wordpress` em um navegador revela que o WordPress ainda está no modo de configuração, o que nos permitirá obter a execução remota de código (RCE) no servidor.

#### Enumeração de subdomínio DNS

Também pode haver recursos essenciais hospedados em subdomínios, como painéis de administração ou aplicativos com funcionalidades adicionais que podem ser exploradas. Podemos usar `GoBuster` para enumerar os subdomínios disponíveis de um determinado domínio usando o sinalizador `dns` para especificar o modo DNS. Primeiro, vamos clonar o [repositório](https://github.com/danielmiessler/SecLists) GitHub SecLists , que contém muitas listas úteis para fuzzing e exploração:

```shell-session
casluxd@htb[/htb]$ git clone https://github.com/danielmiessler/SecLists
```

```shell-session
casluxd@htb[/htb]$ sudo apt install seclists -y
```

Em seguida, adicione um servidor DNS como 1.1.1.1 ao arquivo `/etc/resolv.conf`. Vamos segmentar o domínio `inlanefreight.com`, o site de uma empresa fictícia de frete e logística.

```shell-session
casluxd@htb[/htb]$ gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Domain:     inlanefreight.com
[+] Threads:    10
[+] Timeout:    1s
[+] Wordlist:   /usr/share/SecLists/Discovery/DNS/namelist.txt
===============================================================
2020/12/17 23:08:55 Starting gobuster
===============================================================
Found: blog.inlanefreight.com
Found: customer.inlanefreight.com
Found: my.inlanefreight.com
Found: ns1.inlanefreight.com
Found: ns2.inlanefreight.com
Found: ns3.inlanefreight.com
===============================================================
2020/12/17 23:10:34 Finished
===============================================================
```

## Dicas de enumeração da Web

Vamos percorrer algumas dicas adicionais de enumeração da Web que ajudarão a concluir as máquinas no HTB e no mundo real.

#### Captura de Banner / Cabeçalhos de Servidor Web

Na última seção, discutimos a captura de banners para fins gerais. Os cabeçalhos do servidor da Web fornecem uma boa imagem do que está hospedado em um servidor da Web. Eles podem revelar a estrutura de aplicativo específica em uso, as opções de autenticação e se o servidor está sem opções de segurança essenciais ou foi configurado incorretamente. Podemos usar `cURL`para recuperar informações do cabeçalho do servidor na linha de comando. `cURL`é outra adição essencial ao nosso kit de ferramentas de teste de penetração, e a familiaridade com suas muitas opções é incentivada.

```shell-session
casluxd@htb[/htb]$ curl -IL https://www.inlanefreight.com

HTTP/1.1 200 OK
Date: Fri, 18 Dec 2020 22:24:05 GMT
Server: Apache/2.4.29 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```

Outra ferramenta útil é [o EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness) , que pode ser usado para fazer capturas de tela de aplicativos da Web de destino, digitalizá-los e identificar possíveis credenciais padrão.

#### Whatweb

Podemos extrair a versão de servidores web, estruturas de suporte e aplicativos usando a ferramenta de linha de comando `whatweb`. Essas informações podem nos ajudar a identificar as tecnologias em uso e começar a procurar possíveis vulnerabilidades.

```shell-session
casluxd@htb[/htb]$ whatweb 10.10.10.121

http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
```

`Whatweb` é uma ferramenta útil e contém muitas funcionalidades para automatizar a enumeração de aplicativos da web em uma rede.

```shell-session
casluxd@htb[/htb]$ whatweb --no-errors 10.10.10.0/24

http://10.10.10.11 [200 OK] Country[RESERVED][ZZ], HTTPServer[nginx/1.14.1], IP[10.10.10.11], PoweredBy[Red,nginx], Title[Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux], nginx[1.14.1]
http://10.10.10.100 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.100], Title[File Sharing Service]
http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
http://10.10.10.247 [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[contact@cross-fit.htb], Frame, HTML5, HTTPServer[OpenBSD httpd], IP[10.10.10.247], JQuery[3.3.1], PHP[7.4.12], Script, Title[Fine Wines], X-Powered-By[PHP/7.4.12], X-UA-Compatible[ie=edge]
```

#### Certificados

Os certificados SSL/TLS são outra fonte de informações potencialmente valiosa se o HTTPS estiver em uso. Navegar `https://10.10.10.121/` e visualizar o certificado revela os detalhes abaixo, incluindo o endereço de e-mail e o nome da empresa. Eles podem ser usados ​​para realizar um ataque de phishing se isso estiver dentro do escopo de uma avaliação.

![[Pasted image 20230614120448.png]]

#### Robots.txt

É comum que os sites contenham um arquivo `robots.txt`, cujo objetivo é instruir os rastreadores da Web dos mecanismos de pesquisa, como o Googlebot, quais recursos podem e não podem ser acessados ​​para indexação. O arquivo `robots.txt` pode fornecer informações valiosas, como a localização de arquivos privados e páginas de administração. Nesse caso, vemos que o arquivo `robots.txt` contém duas entradas não permitidas.

![[Pasted image 20230614120530.png]]

![[Pasted image 20230614120536.png]]

#### Código-fonte

Também vale a pena verificar o código-fonte de qualquer página da Web que encontrarmos. Podemos clicar `[CTRL + U]` para abrir a janela do código-fonte em um navegador. Este exemplo revela um comentário de desenvolvedor contendo credenciais para uma conta de teste, que pode ser usada para fazer login no site.

