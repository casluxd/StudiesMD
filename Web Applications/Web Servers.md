Um servidor da Web é um aplicativo executado no servidor de back-end, que manipula todo o tráfego HTTP do navegador do lado do cliente, o roteia para as páginas solicitadas e, finalmente, responde ao navegador do lado do cliente. Os servidores da Web geralmente são executados nas portas TCP 80 ou 443 e são responsáveis ​​por conectar os usuários finais a várias partes do aplicativo da Web, além de manipular suas várias respostas.

### Workflow

Um servidor da Web típico aceita solicitações HTTP do lado do cliente e responde com diferentes respostas e códigos HTTP, como um código 200 OK para uma solicitação bem-sucedida, um código 404 NOT FOUND ao solicitar páginas que não existem, código 403 PROIBIDO para solicitar acesso a páginas restritas e assim por diante.

![[Pasted image 20230611213105.png]]

Os servidores da Web também aceitam vários tipos de entrada do usuário em solicitações HTTP, incluindo texto, JSON e até mesmo dados binários (ou seja, para uploads de arquivos). Depois que um servidor da Web recebe uma solicitação da Web, ele é responsável por encaminhá-la ao seu destino, executar todos os processos necessários para essa solicitação e retornar a resposta ao usuário no lado do cliente. As páginas e arquivos que o servidor web processa e direciona o tráfego são os arquivos centrais do aplicativo web.

O exemplo a seguir mostra como solicitar uma página em um terminal Linux usando o utilitário cURL e receber a resposta do servidor usando o sinalizador -I, que exibe os cabeçalhos:

```shell-session
casluxd@htb[/htb]$ curl -I https://academy.hackthebox.com

HTTP/2 200
date: Tue, 15 Dec 2020 19:54:29 GMT
content-type: text/html; charset=UTF-8
...SNIP...
```

Enquanto este exemplo de comando cURL nos mostra o código-fonte da página da web:

```shell-session
casluxd@htb[/htb]$ curl https://academy.hackthebox.com

<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>Cyber Security Training : HTB Academy</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

Muitos tipos de servidores da Web podem ser utilizados para executar aplicativos da Web. A maioria deles pode lidar com todos os tipos de solicitações HTTP complexas e geralmente são gratuitas. Podemos até desenvolver nosso próprio servidor web básico usando linguagens como Python, JavaScript e PHP. No entanto, para cada idioma, há um aplicativo da web popular que é otimizado para lidar com grandes quantidades de tráfego da web, o que economiza tempo na criação de nosso próprio servidor da web.

![[Pasted image 20230612142126.png]]

Apache 'ou httpd' é o servidor web mais comum na internet, hospedando mais de 40% de todos os sites da internet. O Apache geralmente vem pré-instalado na maioria das distribuições Linux e também pode ser instalado em servidores Windows e macOS.

O Apache é geralmente usado com PHP para desenvolvimento de aplicativos da Web, mas também oferece suporte a outras linguagens como .Net, Python, Perl e até mesmo linguagens de sistema operacional como Bash por meio de CGI. Os usuários podem instalar uma ampla variedade de módulos do Apache para estender sua funcionalidade e oferecer suporte a mais idiomas. Por exemplo, para oferecer suporte a arquivos PHP, os usuários devem instalar o PHP no servidor de back-end, além de instalar o módulo mod_php para Apache.

O Apache é um projeto de código aberto e os usuários da comunidade podem acessar seu código-fonte para corrigir problemas e procurar vulnerabilidades. Ele é bem mantido e corrigido regularmente contra vulnerabilidades para mantê-lo seguro contra exploração. Além disso, está muito bem documentado, tornando relativamente fácil o uso e configuração de diferentes partes do servidor web. O Apache é comumente usado por startups e empresas menores, pois é fácil de desenvolver. Ainda assim, algumas grandes empresas utilizam o Apache, incluindo: Apple, Adobe, Baidu.

![[Pasted image 20230612142342.png]]

NGINX é o segundo servidor web mais comum na internet, hospedando aproximadamente 30% de todos os sites da internet. O NGINX se concentra em atender a muitas solicitações da Web simultâneas com memória e carga de CPU relativamente baixas, utilizando uma arquitetura assíncrona para fazer isso. Isso torna o NGINX um servidor da Web muito confiável para aplicativos da Web populares e grandes empresas em todo o mundo, e é por isso que é o servidor da Web mais popular entre os sites de alto tráfego, com cerca de 60% dos 100.000 principais sites usando NGINX.

O NGINX também é gratuito e de código aberto, o que oferece os mesmos benefícios mencionados anteriormente, como segurança e confiabilidade. Alguns sites populares que utilizam NGINX incluem: Google, Facebook, Twitter, Cisco, Intel, Netflix.

![[Pasted image 20230612142430.png]]

IIS (Internet Information Services) é o terceiro servidor web mais comum na internet, hospedando cerca de 15% de todos os sites da internet. O IIS é desenvolvido e mantido pela Microsoft e é executado principalmente em servidores Microsoft Windows. O IIS geralmente é usado para hospedar aplicativos da Web desenvolvidos para o framework Microsoft .NET, mas também pode ser usado para hospedar aplicativos da Web desenvolvidos em outras linguagens, como PHP, ou hospedar outros tipos de serviços, como FTP. Além disso, o IIS é muito bem otimizado para integração com o Active Directory e inclui recursos como o Windows Auth para autenticar usuários usando o Active Directory, permitindo que eles entrem automaticamente em aplicativos da web.

Embora não seja o servidor web mais popular, muitas grandes organizações usam o IIS como seu servidor web. Muitos deles usam o Windows Server em seu back-end ou dependem fortemente do Active Directory em sua organização. Alguns sites populares que utilizam o IIS incluem: Microsoft, Office365, Skype, Stack Overflow, Dell.

Além desses 3 servidores da web, existem muitos outros servidores da web comumente usados, como Apache Tomcat para aplicativos da web Java e Node.JS para aplicativos da web desenvolvidos usando JavaScript no back-end.
